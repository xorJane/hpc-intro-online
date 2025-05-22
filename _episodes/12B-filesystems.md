---
title: "Exploring Remote Resources: Filesystems"
teaching: 15
exercises: 10
questions:
- "Where do I put my files?"
- "From where can I see my files?"
objectives:
- "Write files to $HOME, workspace, and lustre
- View or edit those files from multiple machines
keypoints:
- "Unlike on your laptop, here the computer and the filesystem are separate."
- "You have multiple options for storing your data, including NFS and parallel filesystems."
- "Most filesystems are cross-mounted. This means you can see the same data from multiple machines."
---

* The HPC system/machine is where you create the data. The data is written to and can be accessed from a filesystem. Multiple machines connect to and share multiple filesystems.
