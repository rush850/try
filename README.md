# try

<img src="docs/try_logo.png" alt="try logo" width="100" height="130">

"Do, or do not. There is no try."

We're setting out to change that.

## Description

`try` lets you run a command and inspect its effects before changing your live system. `try` uses Linux's [namespaces (via `unshare`)](https://docs.kernel.org/userspace-api/unshare.html) and the [overlayfs](https://docs.kernel.org/filesystems/overlayfs.html) union filesystem.

## Getting Started

### Dependencies

* `Ubuntu 18.04 LTS` or later

### Installing

You should only need the [`try` script](https://raw.githubusercontent.com/binpash/try/main/try).

```ShellSession
$ curl https://raw.githubusercontent.com/binpash/try/main/try -o try
$ chmod +x try
$ sudo chown root:wheel try
$ sudo cp try /usr/local/bin
```

You can also get the script by cloning this repository:

```ShellSession
$ git clone https://github.com/binpash/try.git
Cloning into 'try'...
remote: Enumerating objects: 190, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 190 (delta 4), reused 4 (delta 4), pack-reused 178
Receiving objects: 100% (190/190), 29.99 KiB | 1.67 MiB/s, done.
Resolving deltas: 100% (83/83), done.
$ # try/try should already be executable
$ sudo chown root:wheel try
$ sudo cp try/try /usr/local/bin
```

## Example Usage

`try` is a higher-order command, like `xargs`, `exec`, `nohup`, or `find`. For example, to ungzip file, you can invoke `try` as follows:

```ShellSession
$ try gunzip file.txt.gz
... # output continued below
```

By default, *try* will ask you to commit the changes made at the end of its execution.

```ShellSession
...
Changes detected in the following files:

/tmp/tmp.0caZdxnHuR/upperdir/home/me/file.txt
/tmp/tmp.0caZdxnHuR/upperdir/home/me/file.txt.gz

Commit these changes? [y/N] y
```

Sometimes, you might want to pre-execute a command and commit its result at a later time. Invoking *try* with the -n flag will return the overlay directory, wothout committing the result.

```ShellSession
$ try -n gunzip file.txt.gz
/tmp/tmp.uCThKq7LBK
```

Alternatively, you can specify your own overlay directory as follows (note that *gunzip_try_sandbox* already exists):

```ShellSession
$ try -D gunzip_try_sandbox gunzip file.txt.gz
$ ls gunzip_try_sandbox
temproot  upperdir  workdir
```

As you can see from the output above, *try* has created an overlay environment in the *gunzip_try_sandbox* directory.

Manually inspecting upperdir reveals the changes to the files made inside the overlay during the execution of the previous command with *try*:

```ShellSession
$ ls -l gunzip_try_sandbox/upperdir/home/me/
total 40
-rw-rw-r-- 1 me me 38704 Jun 19 16:46 file.txt
c--------- 2 me me  0, 0 Jun 19 16:52 file.txt.gz
```

You can inspect the changhes made inside a given overlay directory using *try*:

```ShellSession
$ try summary gunzip_try_sandbox
Changes detected in the following files:

gunzip_try_sandbox/upperdir/home/gliargo/try/file.txt.gz
gunzip_try_sandbox/upperdir/home/gliargo/try/file.txt
```

You can also choose to commit the overlay directory contents:

```ShellSession
$ try commit gunzip_try_sandbox
```

## Version History

* 0.1
    * Initial Release

## License

This project is licensed under the MIT License - see LICENSE.md for details.

Copyright (c) 2023 The PaSh Authors.
