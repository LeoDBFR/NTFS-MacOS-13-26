# NTFS-MacOS-13-26 UPDATED
How to write on an NTFS drive on macOS 15 Sequoia and macOS 26 Tahoe, for Apple Silicon, without a kernel module.

If you used my old tutorial, check the other file in the repo.

This is an update, a better way to do this, thanks to the people at MacOS-Fuse-T

First we need to install some dependencies with homebrew, if you don't have it, check how to install it on https://brew.sh

Let's run these command in the terminal, it will first add the repository needed to install fuse-t, then it will install the dependencies to build ntfs-3G, and it will install fuse-t, which is fuse without the need of a kernel driver. Their site's at https://www.fuse-t.org

Before fuse-t can be installed, the `/usr/local/include` folder that stores the headers for fuse-t needs to be created, otherwise their install script fails to link them to that location, and the `make install` fails.

```bash
sudo mkdir -p /usr/local/include
```

```bash
brew tap macos-fuse-t/homebrew-cask
```

```bash
brew install mounty fuse-t git automake autoconf libtool libgcrypt pkg-config gnutls
```

Now go into a directory of your choice and run this command, to clone ntfs-3g, the ntfs driver.

```bash
git clone https://github.com/macos-fuse-t/ntfs-3g

cd ntfs-3g
```

We'll need to define some flags for it to install properly

```bash
export CPPFLAGS="-I/usr/local/include/fuse"
export LDFLAGS="-L/usr/local/lib -lfuse-t -Wl,-rpath,/usr/local/lib"
```

Now run this command, preparing the configuration files

```bash
./autogen.sh
```

Then, we'll configure it automatically

```bash
./configure  \
        --prefix=/usr/local \
        --exec-prefix=/usr/local \
        --with-fuse=external \
        --sbindir=/usr/local/bin \
        --bindir=/usr/local/bin
```

Now we just need to build/compile it

```bash
make -j"$(sysctl -n hw.ncpu)"
```

And lastly, we install it

```bash
sudo make install
```

Now ntfs-3g should be installed.

Now :

Mount your drive using Mounty

We installed Mounty, launch it and agree.

Plug your NTFS drive AFTER LAUNCHING MOUNTY and in the toolbar click on the Mounty icon, then you should see "Re-mount", click on it, then click on "mount automatically". 

Now go to finder and you should see a new volume with a computer icon called "fuse-t" containing a folder. This folder is your NTFS drive and you can write in it

Now, when you'll plug your drive and Mounty is launched, it will automatically mount your drive.

If you have any questions or problem, comment, or open an issue on Github, or contact me by mail at leodomecbialek@outlook.fr

Thnaks :)
