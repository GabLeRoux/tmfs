TMFS
====

```
__/\\\\\\\\\\\\\\\___/\\\\____________/\\\\___/\\\\\\\\\\\\\\\___/\\\\\\\\\\\___
 _\///////\\\/////___\/\\\\\\________/\\\\\\__\/\\\///////////__/\\\/////////\\\_
  _______\/\\\________\/\\\//\\\____/\\\//\\\__\/\\\____________\//\\\______\///__
   _______\/\\\________\/\\\\///\\\/\\\/_\/\\\__\/\\\\\\\\\\\_____\////\\\_________
    _______\/\\\________\/\\\__\///\\\/___\/\\\__\/\\\///////_________\////\\\______
     _______\/\\\________\/\\\____\///_____\/\\\__\/\\\___________________\////\\\___
      _______\/\\\________\/\\\_____________\/\\\__\/\\\____________/\\\______\//\\\__
       _______\/\\\________\/\\\_____________\/\\\__\/\\\___________\///\\\\\\\\\\\/___
        _______\///_________\///______________\///___\///______________\///////////_____
```

Time Machine File System is a read-only virtual filesystem which helps you to read your Apple's time machine backup.

This filesystem does not targets performances, it has been written for a friend who has lost his macbook and wants to recover its data on Linux.

It's actually not perfect, feel free to report bugs or suggestions at [github.com/abique/tmfs/issues](https://github.com/abique/tmfs/issues).

Enjoy!

How to use it?
--------------

First you have to mount your HFS partition, by doing something like:

```bash
mount /dev/sdXX /mnt/hfs-root
```

Then as root run:

```bash
tmfs /mnt/hfs-root /mnt/tm-root -ouid=$(id -u),gid=$(id -g),allow_other
```

Then as a normal user, go to the directory `/mnt/tm-root/` and enjoy your data! :-)

Sparsebundle Disk Images
------------------------

I haven't used it myself and I can't comment too much on it, but in short you should be able to mount a sparse bundle using https://github.com/torarnv/sparsebundlefs and then mount tmfs on top of it. Please have a look at their project page there should be further explanation.

Dependencies
------------

 - c++ 17
 - cmake >= 3.16
 - fuse

How to build and install it?
----------------------------

Manually, run these commands:

```bash
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=/usr/local ..
make
DESTDIR=install-test make install
```

Then if the installation looks ok to you in `install-test/` do make install as root with following command:

```bash
sudo make install
```

Internals
---------

Time Machine structure:

Snapshot root: 

```bash
${hfs_root}/Backups.backupdb/${comp_name}/${date}/${disk_name}/
```

Hardlink count equals to dir_id: 

```bash
${hfs_root}/Backups.backupdb/${comp_name}/${date}/${disk_name}/.../Folder
```

Real folder with data: 

```bash
${hfs_root}/.HFS+ Private Directory Data/dir_${dir_id}/
```

Our representation:

```bash
/${comp_name}/${date}/${disk_name}/${Real root}
```
