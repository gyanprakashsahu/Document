### CREATE YOUR OWN YUM REPOSITORY

It's very easy task. Just follow below steps:

```
- Create a directory. Suppose it is /var/ftp/local_repo/{i386, x86_64}.

- Now copy your 32 bit rpm packages in i386 and 64 bit in x86_64 directory.

- yum install createrepo

- createrepo --database /var/ftp/local_repo

## Above command create necessary metadata for your yum repo as well as sqlite database for speeding up yum operations.

```

If you face any problem with above command then make following script in /var/ftp/

local_repo/, make it executable and run it. And you repo will be ready to use.

```

#!/bin/sh
destdir="/var/ftp/local_repo"
for arch in i386 x86_64
do
    pushd ${destdir}/${arch} >/dev/null 2>&1
        createrepo .
    popd >/dev/null 2>&1
done

```

#### Install ftp on the yum server. and make /var/ftp as it's default root directory.

#### Now, to make this repo available for other client systems. Create a yum repo config file called /etc/yum.repos.d/my.repo with following contents:

```

[myrepo]
name=My Repository
baseurl=ftp://internalhost.com/local_repo/$basearch
enabled=1

```

#### After yum update your repo will be available to use in your organization.
