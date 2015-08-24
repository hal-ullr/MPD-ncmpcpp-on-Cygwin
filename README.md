# MPD + NCMPCPP Guide

* * *

This guide will serve as a replacement (or update) for the existent guide on Nanami.

The purpose of this guide is to instruct people on manually compiling the newer versions of said packages, resolving dependencies and installing binaries.

The existent guide is outdated, only applies to x86 (32-bit) Cygwin installation, and depends on unsafe binary distribution of an out-of-date ncmpcpp version that could potentially fuck up your Cygwin installation.

This guide won't cover any details on what is MPD, or how the server works, just the installation part. If you want documentation, you can check the website of each one of them.

MPD – http://musicpd.org
MPD Wiki – http://mpd.wikia.com/
NCMPCPP – http://ncmpcpp.rybczak.net/

## Preparations
As you already should know, Cygwin has it's own repository, which you can use by running its installer. Sadly, there's no such a thing as a terminal package manager, like pacman (for Arch Linux) or apt-get (for Ubuntu).

Both MPD and NCMPCPP require binaries for them work, and some other packages that is essential for the compilation.

You can find most of them on the official repository, others, you'll have to manually download them and compile them yourself.

Again, I won't delve into what binaries/packages does.

Going back to the package manager, there is a tool called cyg-fast, a community-made terminal package manager for CygWin forked from apt-cyg, it lets you install/search packages through the official repository and delete/reinstall already existing packages.

https://github.com/tmshn/cyg-fast

As mentioned, cyg-fast requires aria2 for it work, you can find aria2 in the official repository, just run the CygWin installer and search for the package.

After installing Aria2, paste the cyg-fast script you just downloaded on your /bin folder, and run this command on your terminal:

chmod +x /bin/cyg-fast

This will make it executable.

## Binaries and Packages
Like I said, you can download all this packages from the installer, we're using cyg-fast here to speed up this process so it can download all packages in one go without needing to search each and every package on the installer.

We'll need a server for cyg-fast to work on, you can choose one from the server list on the CygWin installer, but on this guide, I'll favor the one I used on my first setup. Type:

cyg-fast -m ftp://ftp.iij.ad.jp/pub/cygwin/ update

Where the link is the server.

Now for the installation of the required packages. You can install more than one package using cyg-fast, just add a space between the package name:

cyg-fast install package, package, etc

You'll need the following packages: 
gcc-core 
gcc-g++ 
make 
libboost-devel 
doxygen 
cygrunsrv 
libsqlite3-devel 
autoconf 
automake 
pkg-config 
libglib2.0-devel 
zlib-devel 
libiconv 
flac 
flac-devel 
libogg 
libvorbis 
libvorbis-devel 
libao-devel 
libao4 
libao 
libcurl-devel 
curl 
libopus-devel 
libsndfile 
libsndfile-devel
wget
tar

Optional packages:
nano or vim

It's recommended you just copy-paste this to avoid any typo. Compiling on CygWin takes time, more than on Linux, depending on your processor. In any case, cyg-fast will usually tell you if it didn't found any package with a specific name. If so, try searching for it on the installer and most likely it'll be there.

### MPD

BUT FIRST, A WORD OF WARNING. MPD has also a Windows version, if you followed the outdated guide, you already know that. You can download it from the official mpd site, mentioned above. On this guide, I'll cover the compilation of MPD through CygWin. If you can't be arsed with this, just ignore this bit and jump straight to the ncmpcpp part.

-> libmpdclient
This is the library for MPD. You can download it on their official website, but we'll be using a nice package we just installed earlier: wget

wget downloads a specified file from your terminal THROUGH it, kind of like a package manager does but without installing it. wget downloads the file in your current folder, so if you're on your home folder (~), it'll download there. Let's make a folder inside our /tmp (temporary folder) for it to download there. You'll be doing this for each and every package you download from now on, don't forget do always cd (bash command to move between folders) back to /tmp so you don't get lost. To create a folder, type:

mkdir -p /tmp/foldername (which in this case, mpd)

After that, type cd !$ , this will take us to our new created folder.
Then, type the following:
wget http://www.musicpd.org/download/libmpdclient/2/libmpdclient-2.10.tar.gz

Notice that this links to the last version of libmpdclient, ALWAYS check for updates on your packages. After downloading the package, instead of opening your explorer and extracting everything manually, you can do that through another util we installed earlier: tar
Type:

tar -xvf libmpdclient-2.10.tar.gz

This will extract everything to a folder of the same name, just not with the .tar.gz extension. To actually list everything on your current folder. type in "ls" without the quotes, and cd in the libmpdclient folder (by typing cd libmpdclient-2.10 if you didn't figured this)

## COMPILING

This is the fun part, and the slower one, and the one which can actually end up in an error. Just calm your tits, and cross your fingers.
In the libmpdclient folder, type in on your term:
./configure --prefix=/usr
It'll start compiling, and this may take a while. After everything ends, type in:
make && make install
Which will actually prepare the package and install it.

This'll basically be the entire process for the rest of the packages, so I won't cover every of this detail again in every package mentioned. If you're lost, come back here and read everything again.

## TAGS AND FORMATS

MPD just doesn't work out of the box, it needs a specific set of packages if you want to run multiple sound formats (mp3, mp4, flac, m4a and so on). Each of the following packages will basically cover the most popular formats, if you want anything else, which is probably a hard thing to consider, you can google for it.


-> libid3tag - binary for running ID3 formats
cd /tmp/mpd
wget 'http://sourceforge.net/projects/mad/files/libid3tag/0.15.1b/libid3tag-0.15.1b.tar.gz'
tar xvf libid3tag-0.15.1b.tar.gz
cd libid3tag-0.15.1b
./configure --prefix=/usr

## WARNING

In x64, it is possible for the configuration process to go wrong, so you'll need to specify a different path for the configuration. If you get the following error:
"configure: error: I can not guess build type; you must specify one"

try using ./configure --prefix=/usr --build=x86_64-unknown-cygwin instead of the normal command. This may happen more than once, so be sure to use this fix again for other packages. If this error happens, to the configuration again.

If not, just proceed to using make && make install after the configuration.

-> libmad, MPEG Audio Decoder, also used for MP3
cd /tmp/mpd
wget 'http://sourceforge.net/projects/mad/files/libmad/0.15.1b/libmad-0.15.1b.tar.gz'
tar -xvf libmad-0.15.1b.tar.gz
cd libmad-0.15.1b
./configure --prefix=/usr

##### ERROR WARNING
Either during the configuration or make process (I don't fucking remember), you'll probably get this error:
gcc :  error :  Unrecognized  command  line  option  '-fforce-MEM'

This is an error in the "Makefile" file, present on your libmad package you just extracted.

## Handling the file

Since everything is coded, you can edit the files. As an optional package mentioned, you can use either nano or vim. nano is a basic text editor and easy to use, vim is an advanced text editor with syntax highlighting that's pretty difficult to master, and most neckbeards know their way around it. If you can't be arsed to use terminal-based text editors, use another native text editor with Windows, such as Notepad, Notepad++ or Sublime Text, whatever the fuck floats your boat. The Makefile is in your libmad folder, if you're using nano ir vim, type in your terminal nano /tmp/mpd/libmad/Makefile or vim /tmp/mpd/libmad/Makefile

For any other text editor, just open them manually. As for the error, just comment out (literally delete the text) '--fforce-MEM', just keep scrolling until you find it. Re-attempt confuration (if the error happens on that part).

After fixing it, just proceed with:
make && make install

-> LAME, another MP3 encoder.
cd /tmp/mpd
wget 'http://sourceforge.net/projects/lame/files/lame/3.99/lame-3.99.5.tar.gz'
tar lame-3.99.5.tar.gz
cd lame-3.99.5
./configure --prefix=/usr

##### ERROR WARNING
Another error, if you get

> Lametime . c: 139 : 25 :  error :  ' _O_BINARY '  is not declared  ( first use in this function ) 
>      setMode ( fileno ( fp ),  _O_BINARY );

Open the lametime.c file, located on the frontend folder, inside the lame folder, with a text editor of choose, and search for:


> \#elif defined __CYGWIN__ 
>     setMode (fileno (fp), _O_BINARY); 


remove the _ before _O_BINARY so it stays O_BINARY

Re-attempt configuration and procceed with
make && make install

-> FAAD2, .aac and .m4a formats
cd /tmp/mpd
wget 'http://sourceforge.net/projects/faac/files/faad2-src/faad2-2.7/faad2-2.7.tar.bz2'
tar -xvf faad2-2.7.tar.bz2
cd faad2-2.7
./configure --prefix =/usr
make && make install

You'll also need some other audio libraries, use cyg-fast for this:

cyg-fast install audiofile libaudiofile-devel

## MPD INSTALLATION

Alright, time for the actual stuff.

cd /tmp/mpd
wget 'http://www.musicpd.org/download/mpd/0.19/mpd-0.19.10.tar.gz'
tar -xvf mpd-0.19.10.tar.gz
cd mpd-0.19.10

##### ERROR WARNING ##### 
While in the middle of the make process, you might this error:

src / system / SocketUtil . cxx: 79 : 29 :  error :  ' SO_PASSCRED '  WAS  not  declared  in  this  scope 
  setsockopt ( fd ,  SOL_SOCKET ,  SO_PASSCRED , 

if so, you'll have to reconfigure it again, this time using ./configure --disable-Un

## ATTENTION ##
While in the process of the MPD configuration, pay attention to the positive packages, cause MPD will attempt to recognize them if they are installed:

########### MPD CONFIGURATION ############

Archive support:
        (-bzip2) (-ISO9660) (-ZIP)
Client support:
        (+ IPv6) (+ TCP) (+ UNIX Domain Sockets)
Storage support:
        (-NFS) (-SMB)
File format support:
        (+ AAC) (-AdPlug) (+ DSD) (-C64 SID) (- FFMPEG) (+ FLAC) (-FluidSynth) (-GME)
        (+ Libsndfile) (-MikMod) (-MODPLUG) (+ MAD) (- MPG123) (-Musepack)
        (+ Opus) (-OggTremor) (+ OggVorbis) (+ WAVE) (-WavPack) (-WildMidi)
Other features:
        (-libsamplerate) (-libsoxr) (+ Libmpdclient) (-inotify) (+ SQLite)
Metadata support:
        (+ ID3)
Playback support:
        (-ALSA) (+ FIFO) (+ File Recorder) (+ HTTP Daemon) (-JACK)
        (+ Libao) (+ OSS) (-OpenAL) (-OS X) (-Pipeline)
        (-PulseAudio) (-ROAR) (-SHOUTcast) (-Solaris) (-WinMM)
Streaming encoder support:
        (+ FLAC) (+ LAME) (-Shine) (+ Ogg Vorbis) (+ Opus) (-TwoLAME) (+ WAVE)
Streaming support:
        (-CDIO_PARANOIA) (+ CURL) (-SMBCLIENT) (-Soundcloud)
        (-MMS)
Event loop:
        poll

Names tagged as (+) means they're installed, and obviously (-) means not. If you followed this guide, all of the packages above are installed and is the essential for you to use MPD. Pay special attention to libmpdclient. if it isn't checked, you're probably using an outdated binary, depending on which date you saw this. Check my tip at the beginning of this guide and download the updated ones.

Proceed with
make && make install

after everything is finished.

### CONFIGURING MPD

MPD configuration is made through a config file, and it's stored on your home folder. On your terminal, type the following:

mkdir ~/.mpd
mkdir ~/.mpd/playlists

The .mpd folder serves as a host for your configuration files, and playlists folder is self-explanatory.

Afterwards, type:

touch mpd.conf

"touch" serves as the bash command to create a blank file and, unless you make it otherwise, a blank one. We just created the configuration file for mpd.
Using a text editor of your choosing, open your mpd.conf file and add the following:

music_directory          "" 
db_file                  "~/.mpd/database" 
playlist_directory               "~/.mpd/playlists" 
log_file                         "~/.mpd/log"
mixer_type				  "software"

audio_output { 
        type             "oss" 
        name             "My OSS Device" 
        format           "44100: 16: 2" 
}

filesystem_charset               "UTF-8" 
id3v1_encoding                   "UTF-8"

### ATTENTION
Is important for you to specify your current Music directory. It's highly recommendable that you don't store it on you Libraries (as in C:\Users\user\Libraries\Music), instead, you could store it on a different drive or your current one. Ideally, you could store it on your home folder, like on any Linux distro.

Also, don't use \, just /, since it's the Ganoo way.

And, you're basically done. Try to run mpd with the following command:

mpd --verbose --no-daemon --stdout

If no errors ocurred, you're good to go. If it did, you did something wrong. Re-read the guide and if any error still persists, try contacting me (jumi) or hal, by deviantArt:
http://jumiknight.deviantart.com
http://hal-ullr.deviantart.com

or on IRC, you can find me on #/w/alls, #dprk, #plez and #rice.
As for hal, he's always on #rice.

### NCMPCPP
This half of the guide is made by hal, and it favors the use of CygWin Ports.
CygWin Ports is a project that provides packages and binaries not commonly available on the official repository. 
Some of the packages mentioned were previously installed, so you can ignore that part if you want.

# Installing ncmpcpp in Cygwin

* * *

## Step 1: Getting Cygwin and Cygwin Ports working

First off, you’ll want to download the setup [x86](https://cygwin.com/setup-x86.exe) or [x64](https://cygwin.com/setup-x86_64.exe)

### Cygwin Ports

Nobody who I have ever walked through installing ncmpcpp has ever gotten this right without me explicitly telling them how, and admittedly, it is a little counterintuitive.

Their website is here: http://cygwinports.org/, but the important part is:

> 2\. Launch setup-*.exe with the -K flag, e.g.:

>    `cygstart -- /path/to/setup-x86.exe -K http://cygwinports.org/ports.gpg`

> 3\. On Choose Installation Type page, select “Install from Internet”.

> 4\. On Choose Download Site(s) page, select a distro mirror, then enter ftp://ftp.cygwinports.org/pub/cygwinports in the User URL field and press Add (making sure that both are highlighted).

The easiest way to do this is to open the Command Prompt in the directory with *setup-x86\*.exe* in it (shift + right click in Explorer, *“Open command window here”*) and typing `setup-x86.exe -K http://cygwinports.org/ports.gpg`. Choose default settings up until you get to mirror selection, and select a mirror (I always choose kernel.org, YMMV) and then add the Cygwin Ports URL. **It is extremely important that both URLs are selected – your installation will fail if you select only the Cygwin Ports URL and if you have only the Cygwin mirror URL, not all of the packages you need will be available.** Optionally, you can set the download directory to *%TEMP%* or something if you don’t want the download folders polluting wherever you have your installer. It may take a minute for things to look like they’re moving.

### Installation

The following is a list of packages you’ll need to install. **Cygwin Ports is required only for the *libmpdclient* package.** You don’t have to bother with Cygwin Ports if you plan to build this package from source yourself.

|  Package                     |  Version as of time of writing   |
| :--------------------------- | -------------------------------: |
|  devel/git                   |  2.4.5-3                         |
|  devel/gcc-core              |  4.9.3-1                         |
|  devel/gcc-g++               |  4.9.3-1                         |
|  devel/pkg-config            |  0.28-1                          |
|  devel/make                  |  4.1-1                           |
|  devel/automake              |  9-1                             |
|  devel/autoconf              |  13-1                            |
|  devel/libtool               |  2.4.6-1                         |
|  devel/libboost-devel        |  1.57.0-1                        |
|  devel/libncurses-devel      |  5.9-20150530-1                  |
|  devel/libreadline-devel     |  6.3.8-1                         |
|  libs/libmpdclient-devel     |  2.10-1                          |
|  libs/libcurl-devel          |  7.43.0-1                        |

## Step 2: Installing ncmpcpp

#### ncmpcpp was not designed for cygwin, and as of the time of writing, there are three things you have to do to get it to build normally.

1. Download and extract the current version of ncmpcpp. At time of writing this is *6.5*. It is important to download the stable version – I have had luck building the git versions in the past, but now the stable version is the only manageable one
2. **Extra step #1:**

    ```sh
    export AM_FORCE_VERSION=1.9
    export NOCONFIGURE=true
    ```

3. Run *./autogen.sh*

4. **Extra step #2:**

    ```sh
    export BOOST_LIB_SUFFIX=""
    ```

5.  **Extra step #3:**

    In *src/status.cpp*, add the following to the top, with the rest of the inclusions.
    ```c++
    #include <sys/socket.h>
    #include <sys/types.h>
    ```
    And delete the following line:

    ```c++
    #include <netinet/tcp.h>
    ```

6. Run `./configure --with-curl`

    You should have no errors at this point. If you do, carefully review the steps. If you still do, feel free to contact me.


7. Run `make` and `make install`
