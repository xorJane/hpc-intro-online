---
title: "Transferring files with remote computers"
teaching: 15
exercises: 15
questions:
- "How do I transfer files to (and from) the cluster?"
objectives:
- "Transfer files to and from a computing cluster."
keypoints:
- "`wget` and `curl -O` download a file from the internet."
- "`scp` and `rsync` transfer files to and from your computer."
- "You can use an SFTP client like FileZilla to transfer files through a GUI."
---

Performing work on a remote computer is not very useful if we cannot get files
to or from the cluster. There are several options for transferring data between
computing resources using CLI and GUI utilities, a few of which we will cover.

## Hopper

Most of this lesson comes from the standard HPC Carpentries. We've left in the
original coverage of `scp` for transferring files, but we recommend using Hopper:

[Hopper docs](https://hpc.llnl.gov/software/data-management-tools/using-hopper)

Hopper provides a graphical user interface for transferring files between your
laptop/desktop and LC systems. We'll demo this in session and encourage you to try
it out too!

## Download Lesson Files From the Internet

One of the most straightforward ways to download files is to use either `curl`
or `wget`. One of these is usually installed in most Linux shells, on Mac OS
terminal and in GitBash. Any file that can be downloaded in your web browser
through a direct link can be downloaded using `curl` or `wget`. This is a
quick way to download datasets or source code. The syntax for these commands is

* `wget [-O new_name] https://some/link/to/a/file`
* `curl [-o new_name] https://some/link/to/a/file`

Try it out by downloading some material we'll use later on, from a terminal on
your local machine, using the URL of the current codebase:

<https://github.com/hpc-carpentry/amdahl/tarball/main>

> ## Download the "Tarball"
>
> The word "tarball" in the above URL refers to a compressed archive format
> commonly used on Linux, which is the operating system the majority of HPC
> cluster machines run.
> A tarball is a lot like a `.zip` file.
> The actual file extension is `.tar.gz`, which reflects the two-stage process
> used to create the file:
> the files or folders are merged into a single file using `tar`, which is then
> compressed using `gzip`, so the file extension is "tar-dot-g-z."
> That's a mouthful, so people often say "the _xyz_ tarball" instead.
>
> You may also see the extension `.tgz`, which is just an abbreviation of
> `.tar.gz`.
>
> By default, `curl` and `wget` download files to the same name as the URL:
> in this case, `main`.
> Use one of the above commands to save the tarball as `amdahl.tar.gz`.
>
> > ## `wget` and `curl` Commands
> >
> > ```
> > {{ site.local.prompt }} wget -O amdahl.tar.gz https://github.com/hpc-carpentry/amdahl/tarball/main
> > # or
> > {{ site.local.prompt }} curl -o amdahl.tar.gz -L https://github.com/hpc-carpentry/amdahl/tarball/main
> > ```
> > {: .language-bash}
> > The `-L` option to `curl` tells it to follow URL redirects (which `wget` does by default).
> {: .solution}
{: .challenge}

After downloading the file, use `ls` to see it in your working directory:

```
{{ site.local.prompt }} ls
```
{: .language-bash}

## Archiving Files

One of the biggest challenges we often face when transferring data between
remote HPC systems is that of large numbers of files. There is an overhead to
transferring each individual file and when we are transferring large numbers of
files these overheads combine to slow down our transfers to a large degree.

The solution to this problem is to _archive_ multiple files into smaller
numbers of larger files before we transfer the data to improve our transfer
efficiency.
Sometimes we will combine archiving with _compression_ to reduce the amount of
data we have to transfer and so speed up the transfer.
The most common archiving command you will use on a (Linux) HPC cluster is
`tar`.

`tar` can be used to combine files and folders into a single archive file and,
optionally, compress the result.
Let's look at the file we downloaded from the lesson site, `amdahl.tar.gz`.

The `.gz` part stands for _gzip_, which is a compression library.
It's common (but not necessary!) that this kind of file can be interpreted by
reading its name: it appears somebody took files and folders relating to
something called "amdahl," wrapped them all up into a single file with `tar`,
then compressed that archive with `gzip` to save space.

Let's see if that is the case, _without_ unpacking the file.
`tar` prints the "**t**able of contents" with the `-t` flag, for the file
specified with the `-f` flag followed by the filename.
Note that you can concatenate the two flags: writing `-t -f` is interchangeable
with writing `-tf` together.
However, the argument following `-f` must be a filename, so writing `-ft` will
_not_ work.

```
{{ site.local.prompt }} tar -tf amdahl.tar.gz
hpc-carpentry-amdahl-46c9b4b/
hpc-carpentry-amdahl-46c9b4b/.github/
hpc-carpentry-amdahl-46c9b4b/.github/workflows/
hpc-carpentry-amdahl-46c9b4b/.github/workflows/python-publish.yml
hpc-carpentry-amdahl-46c9b4b/.gitignore
hpc-carpentry-amdahl-46c9b4b/LICENSE
hpc-carpentry-amdahl-46c9b4b/README.md
hpc-carpentry-amdahl-46c9b4b/amdahl/
hpc-carpentry-amdahl-46c9b4b/amdahl/__init__.py
hpc-carpentry-amdahl-46c9b4b/amdahl/__main__.py
hpc-carpentry-amdahl-46c9b4b/amdahl/amdahl.py
hpc-carpentry-amdahl-46c9b4b/requirements.txt
hpc-carpentry-amdahl-46c9b4b/setup.py
```
{: .language-bash}

This example output shows a folder which contains a few files, where `46c9b4b`
is an 8-character [git][git-swc] commit hash that will change when the source
material is updated.

Now let's unpack the archive. We'll run `tar` with a few common flags:

* `-x` to e**x**tract the archive
* `-v` for **v**erbose output
* `-z` for g**z**ip compression
* `-f «tarball»` for the file to be unpacked

> ## Extract the Archive
>
> Using the flags above, unpack the source code tarball into a new
> directory named "amdahl" using `tar`.
>
> ```
> {{ site.local.prompt }} tar -xvzf amdahl.tar.gz
> ```
> {: .language-bash}
>
> ```
> hpc-carpentry-amdahl-46c9b4b/
> hpc-carpentry-amdahl-46c9b4b/.github/
> hpc-carpentry-amdahl-46c9b4b/.github/workflows/
> hpc-carpentry-amdahl-46c9b4b/.github/workflows/python-publish.yml
> hpc-carpentry-amdahl-46c9b4b/.gitignore
> hpc-carpentry-amdahl-46c9b4b/LICENSE
> hpc-carpentry-amdahl-46c9b4b/README.md
> hpc-carpentry-amdahl-46c9b4b/amdahl/
> hpc-carpentry-amdahl-46c9b4b/amdahl/__init__.py
> hpc-carpentry-amdahl-46c9b4b/amdahl/__main__.py
> hpc-carpentry-amdahl-46c9b4b/amdahl/amdahl.py
> hpc-carpentry-amdahl-46c9b4b/requirements.txt
> hpc-carpentry-amdahl-46c9b4b/setup.py
> ```
> {: .output}
>
> Note that we did not need to type out `-x -v -z -f`, thanks to flag
> concatenation, though the command works identically either way --
> so long as the concatenated list ends with `f`, because the next string
> must specify the name of the file to extract.
{: .discussion}

The folder has an unfortunate name, so let's change that to something more
convenient.

```
{{ site.local.prompt }} mv hpc-carpentry-amdahl-46c9b4b amdahl
```
{: .language-bash}

Check the size of the extracted directory and compare to the compressed
file size, using `du` for "**d**isk **u**sage".

```
{{ site.local.prompt }} du -sh amdahl.tar.gz
8.0K     amdahl.tar.gz
{{ site.local.prompt }} du -sh amdahl
48K    amdahl
```
{: .language-bash}

Text files (including Python source code) compress nicely:
the "tarball" is one-sixth the total size of the raw data!

If you want to reverse the process -- compressing raw data instead of
extracting it -- set a `c` flag instead of `x`, set the archive filename,
then provide a directory to compress:

```
{{ site.local.prompt }} tar -cvzf compressed_code.tar.gz amdahl
```
{: .language-bash}
```
amdahl/
amdahl/.github/
amdahl/.github/workflows/
amdahl/.github/workflows/python-publish.yml
amdahl/.gitignore
amdahl/LICENSE
amdahl/README.md
amdahl/amdahl/
amdahl/amdahl/__init__.py
amdahl/amdahl/__main__.py
amdahl/amdahl/amdahl.py
amdahl/requirements.txt
amdahl/setup.py
```
{: .output}

If you give `amdahl.tar.gz` as the filename in the above command, `tar` will
update the existing tarball with any changes you made to the files.
That would mean adding the new `amdahl` folder to the _existing_ folder
(`hpc-carpentry-amdahl-46c9b4b`) inside the tarball, doubling the size of the
archive!

## Transferring Single Files and Folders With `scp`

To copy a single file to or from the cluster, we can use `scp` ("secure copy").
The syntax can be a little complex for new users, but we'll break it down.
The `scp` command is a relative of the `ssh` command we used to
access the system, and can use the same public-key authentication
mechanism.

To _upload to_ another computer, the template command is

```
{{ site.local.prompt }} scp local_file {{ site.remote.user }}@{{ site.remote.login }}:remote_destination
```
{: .language-bash}

in which `@` and `:` are field separators and `remote_destination` is a path
relative to your remote home directory, or a new filename if you wish to change
it, or both a relative path _and_ a new filename.
If you don't have a specific folder in mind you can omit the
`remote_destination` and the file will be copied to your home directory on the
remote computer (with its original name).
If you include a `remote_destination`, note that `scp` interprets this the same
way `cp` does when making local copies:
if it exists and is a folder, the file is copied inside the folder; if it
exists and is a file, the file is overwritten with the contents of
`local_file`; if it does not exist, it is assumed to be a destination filename
for `local_file`.

Upload the lesson material to your remote home directory like so:

```
{{ site.local.prompt }} scp amdahl.tar.gz {{ site.remote.user }}@{{ site.remote.login }}:
```
{: .language-bash}

> ## Why Not Download on {{ site.remote.name }} Directly?
>
> Most computer clusters are protected from the open internet by a _firewall_.
> For enhanced security, some are configured to allow traffic _inbound_, but
> not _outbound_.
> This means that an authenticated user can send a file to a cluster machine,
> but a cluster machine cannot retrieve files from a user's machine or the
> open Internet.
>
> Try downloading the file directly. Note that it may well fail, and that's
> OK!
>
> > ## Commands
> >
> > ```
> > {{ site.local.prompt }} ssh {{ site.remote.user }}@{{ site.remote.login }}
> > {{ site.remote.prompt }} wget -O amdahl.tar.gz https://github.com/hpc-carpentry/amdahl/tarball/main
> > # or
> > {{ site.remote.prompt }} curl -o amdahl.tar.gz https://github.com/hpc-carpentry/amdahl/tarball/main
> > ```
> > {: .language-bash}
> {: .solution}
>
> Did it work? If not, what does the terminal output tell you about what
> happened?
{: .challenge}

## Transferring a Directory

To transfer an entire directory, we add the `-r` flag for "**r**ecursive":
copy the item specified, and every item below it, and every item below those...
until it reaches the bottom of the directory tree rooted at the folder name you
provided.

```
{{ site.local.prompt }} scp -r amdahl {{ site.remote.user }}@{{ site.remote.login }}:
```
{: .language-bash}

> ## Caution
>
> For a large directory -- either in size or number of files --
> copying with `-r` can take a long time to complete.
{: .callout}

When using `scp`, you may have noticed that a `:` _always_ follows the remote
computer name.
A string _after_ the `:` specifies the remote directory you wish to transfer
the file or folder to, including a new name if you wish to rename the remote
material.
If you leave this field blank, `scp` defaults to your home directory and the
name of the local material to be transferred.

On Linux computers, `/` is the separator in file or directory paths.
A path starting with a `/` is called _absolute_, since there can be nothing
above the root `/`.
A path that does not start with `/` is called _relative_, since it is not
anchored to the root.

If you want to upload a file to a location inside your home directory --
which is often the case -- then you don't need a _leading_ `/`. After the `:`,
you can type the destination path relative to your home directory.
If your home directory _is_ the destination, you can leave the destination
field blank, or type `~` -- the shorthand for your home directory -- for
completeness.

With `scp`, a trailing slash on the target directory is optional, and has no effect.
A trailing slash on a source directory is important for other commands, like `rsync`.



{% include links.md %}

[git-swc]: https://swcarpentry.github.io/git-novice/
[rsync]: https://rsync.samba.org/
