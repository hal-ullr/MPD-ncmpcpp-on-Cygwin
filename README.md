# Installing ncmpcpp in Cygwin

* * *

## Step 1: Getting Cygwin and Cygwin Ports working

First off, you’ll want to download the setup [x86](https://cygwin.com/setup-x86.exe) or [x64](https://cygwin.com/setup-x86_64.exe)

### Cygwin Ports

Nobody who I have ever walked through installing ncmpcpp has ever gotten this right without me explicitly telling them how, and admittedly, it is a little counterintuitive.

Their website is here: http://cygwinports.org/, but the important part is:

> 2\. Launch setup-\*.exe with the -K flag, e.g.:

>    `cygstart -- /path/to/setup-x86.exe -K http://cygwinports.org/ports.gpg`

> 3\. On Choose Installation Type page, select “Install from Internet”.

> 4\. On Choose Download Site(s) page, select a distro mirror, then enter ftp://ftp.cygwinports.org/pub/cygwinports in the User URL field and press Add (making sure that both are highlighted).

The easiest way to do this is to open the Command Prompt in the directory with `setup-*.exe` in it (shift + right click in Explorer, *“Open command window here”*) and typing `setup-x86.exe -K http://cygwinports.org/ports.gpg`. Choose default settings up until you get to mirror selection, and select a mirror (I always choose kernel.org, YMMV) and then add the Cygwin Ports URL. **It is extremely important that both URLs are selected – your installation will fail if you select only the Cygwin Ports URL and if you have only the Cygwin mirror URL, not all of the packages you need will be available.** Optionally, you can set the download directory to *%TEMP%* or something if you don’t want the download folders polluting wherever you have your installer. It may take a minute for things to look like they’re moving.

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
