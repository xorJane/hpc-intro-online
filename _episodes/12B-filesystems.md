---
title: "Exploring Remote Resources: Filesystems"
teaching: 15
exercises: 10
questions:
- "Where do I put my files?"
- "From where can I see my files?"
objectives:
- "Write files to $HOME, workspace, and lustre"
- "View or edit those files from multiple machines"
keypoints:
- "Unlike on your laptop, here the computer and the filesystem are separate."
- "You have multiple options for storing your data, including NFS and parallel filesystems."
- "Most filesystems are cross-mounted. This means you can see the same data from multiple machines."
---

## Your data does not live on the HPC machine

When we talk about an HPC machine or an HPC system, we're referring to the supercomputer that performed the calculations. This is the same machine/system that you connect to via `ssh <username>@<machine>.llnl.gov`. (For available systems see [https://hpc.llnl.gov/hardware/compute-platforms](https://hpc.llnl.gov/hardware/compute-platforms).)

However, when you login to `dane` and generate data, the data does not live on ruby. Your data will write to a directory owned by you on a filesystem of your choosing that is *attached* to `dane`. Examples might include

* /usr/workspace/$USER
* $HOME
* /p/vast1/$USER
* /p/lustre1/$USER

Try `cd`'ing to any of these and run `pwd` to ensure you landed where you think you did. 

Next, try writing data to workspace. For example, while logged onto `dane`

```
cd /usr/workspace/$USER
touch testfile
ls
```

Running `ls` should prove to yourself that the file `testfile` exists after running `touch`. You can add content to that file or leave it as is.

Now, try viewing that file from a different system, like `tioga`: `ssh` to a different LC machine, `cd /usr/workspace/$USER`, and run `ls`. 

**The HPC system/machine is where you create the data. The data is written to and can be accessed from a filesystem. Multiple machines connect to and share multiple filesystems.**

## Filesystem options

When choosing where to create and store your files, you'll want to consider

* How much data do I have to store?
* How bad (catastrophic? annoying?) would it be if I accidentally deleted my data?
* How am I going to create this data?

Every filesystem option -- Home directories, workspace, Lustre, Vast, etc. -- has tradeoffs. On each filesystem, you have a quota -- a max amount of data you can create. (Try running `quota -v` to see these limits and what you've used so far.) Some filesystems have copies and/or backups of your data, while others do not. Some filesystems are designed for parallel I/O and others are not.

For example, you can only write about 24 GB to your $HOME, but this directory has both a `.snapshot` subdirectory with recent copies of your data from different timestamps *and* backups from the last 6 months. In workspace you still get recent copies of your data in `.snapshot` but no longer term backups; there's less redundancy, but you can write up to 2 TB. If you'll be doing parallel I/O (running jobs that read from/write to multiple files at once), you'll probably want to work with Lustre or Vast, where you start with 20 TB on each filesystem but none of your data is copied or backed up.

See [here](https://hpc.llnl.gov/documentation/user-guides/using-lc-file-systems) for more information on your options.
