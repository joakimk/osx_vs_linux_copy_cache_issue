This is a reproduction of a caching issue in Docker on OSX vs Linux.

---

I built and pushed the image from my OSX:

    $ uname -a
    Darwin 17.4.0 Darwin Kernel Version 17.4.0: Sun Dec 17 09:19:54 PST 2017; root:xnu-4570.41.2~1/RELEASE_X86_64 x86_64
    $ docker -v
    Docker version 18.03.0-ce, build 0520e24

    docker build . -t auctionet/osx_vs_linux_copy_cache_issue
    docker push auctionet/osx_vs_linux_copy_cache_issue

I cleared all local data in preferences with "Remove all data" and ran this:

    docker pull auctionet/osx_vs_linux_copy_cache_issue
    docker build . -t auctionet/osx_vs_linux_copy_cache_issue --cache-from auctionet/osx_vs_linux_copy_cache_issue

```
Sending build context to Docker daemon     64kB
Step 1/4 : FROM alpine:latest
latest: Pulling from library/alpine
ff3a5c916c92: Already exists
Digest: sha256:7b848083f93822dd21b0a2f14a110bd99f6efb4b838d499df6d04a49d0debf8b
Status: Downloaded newer image for alpine:latest
 ---> 3fd9065eaf02
Step 2/4 : RUN sleep 3
 ---> Using cache
 ---> 2e9e440fcdcd
Step 3/4 : COPY Dockerfile /tmp
 ---> Using cache
 ---> e012639834a8
Step 4/4 : RUN sleep 3
 ---> Using cache
 ---> 39fd9c43a613
Successfully built 39fd9c43a613
Successfully tagged auctionet/osx_vs_linux_copy_cache_issue:latest
```

All steps successfully used cache.

---

And then on a Linux host:

```
# uname -a
Linux 4.4.0-116-generic #140-Ubuntu SMP Mon Feb 12 21:23:04 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux

# docker -v
Docker version 18.03.0-ce, build 0520e24

Sending build context to Docker daemon  48.13kB
Step 1/4 : FROM alpine:latest
 ---> 3fd9065eaf02
Step 2/4 : RUN sleep 3
 ---> Using cache
 ---> 2e9e440fcdcd
Step 3/4 : COPY Dockerfile /tmp
 ---> 49ad7d6f9dc1
Step 4/4 : RUN sleep 3
 ---> Running in b62f94035126
Removing intermediate container b62f94035126
 ---> 3cfd995e42e5
Successfully built 3cfd995e42e5
Successfully tagged auctionet/osx_vs_linux_copy_cache_issue:latest
```

**Unexpected behavior**: Step 2/4 uses cache, but Step 3/4 does not.

---

I tried it on a different OSX host:

TODO

All steps successfully used cache?
