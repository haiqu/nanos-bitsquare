# How to build and install Nano S firmware in Windows

## Tested in Windows 7 32-bit on an IBM T60 laptop



### Prerequisites - Machine and OS

- Reference: https://blogs.msdn.microsoft.com/kaushal/2011/10/02/support-for-ssltls-protocols-on-windows/
- Minimum Windows version is Windows 7. Windows XP does not support SSL/TLS 1.1 and Ledger requires it.
- Reference: https://en.wikipedia.org/wiki/3_GB_barrier
- Minimum physical memory: 3GB (maximum that Win32 will allow). May barely work with 2GB but untested.
- Minimum free hard disk space approximately 7-10GB.

### Prerequisites - Software Tools

Python 2.7 plus packages gevent, greenlet, msgpack and pil

- Install python 2.7 from https://www.python.org/downloads/ (not Python 3.x)
- `pip install gevent` or d/l from https://pypi.python.org/pypi/gevent (win exe install)
- `pip install greenlet` or d/l from https://pypi.python.org/pypi/greenlet and `easy_install greenlet-0.4.10-py2.7-win32.egg`
- `pip install msgpack-python` or d/l from https://pypi.python.org/pypi/msgpack-python and `pip install msgpack_python-0.4.8-cp27-cp27m-win32.whl`
- Download and install the Python Image Library from http://www.pythonware.com/products/pil/ (win exe install)

Visual Studio

- Visual Studio Community Edition https://www.visualstudio.com/downloads/
- May need updates or MSVC++ redistributables, same page and click on Tools

Git

- Git from https://git-scm.com/download/win and recommend you learn to use git-gui and git-bash


### Prerequisites - Configuration

Since we're building in the terminal (or Bash shell), all tools need to be visible to the environment. As a minimum the environment variables below should be set (note: BOLOS is covered further down).

BOLOS_ENV, BOLOS_SDK and PYTHON plus Path, LIB and INCLUDE entries as appropriate. Some non-intuitive settings might need to be made, such as a path to the Python27\Scripts directory, because of the odd choices constantly being made by Linux tool developers.

To edit environment entries, go to Start->Control Panel->System->Advanced system settings->Environment variables


### Getting the files - Github downloads

Open a cmd terminal - now hidden away at Start->All Programs->Accessories->Command Prompt - and you'll end up somewhere like `C:\Users\<yourname>` which is as good a place as any to download the files.

- `git clone https://github.com/haiqu/nanos-bitsquare` (sample project)
- `git clone https://github.com/haiqu/blue-devenv` (development environment)
- `git clone https://github.com/haiqu/nanos-secure-sdk` (the Nano S software development kit)

The first thing to note is that the original blue-devenv\build-llvm.sh script from Ledger has been chopped into three separate files called download-llvm.sh build-llvm.sh and clean-llvm.sh and for a very good reason. If the original script is run, it fails then deletes all the downloaded files (about 1.2-1.5GB of them!)

This is where we need to use the Git Bash shell. The files are designed for Linux but we can shortcut that by using it. So, open the git project directory blue-devenv in Git-Gui and then select Repository->Git Bash from the menu. Now we can start the real work.

- `./download-llvm.sh` (Only needed once, gets all the files to build this puppy.)
- `./build-llvm.sh` (Starts CMake and builds the Clang compiler scripts.)

You should see some failure messages at the end, don't worry about that. This script doesn't automatically build the compiler. But if you look in llvm-build you'll see two directories called `x86` and `arm` and they contain Visual Studio project files.

In both cases be sure to select Release in Visual Studio before starting the build. Open the x86 file called ALL_BUILD.vcxproj and build it, then once complete you can do the arm project as well. It will fail in the last few files on arm while looking for an x86 library (!) but this doesn't seem to affect anything.

Now we re-run the build script, so that the files are installed into blue-devenv\clang-arm-fropi where they belong, and finally clean up all the build files (but _not_ the source files!).

- `./build-llvm.sh`
- `./clean-llvm.sh`


### Set the paths in your environment

The system needs to know where to find this stuff now. In our example:

- BOLOS_SDK = `C://Users//<yourname>//nanos-secure-sdk`
- BOLOS_ENV = `C://Users//<yourname>//blue-devenv`

Paths used by Git Bash will need to have a double forward slash where usually a backslash would be used. This is an oddity of the Posix system rather than a bug. After setting these variables you will need to close and reopen Git-Gui and Git Bash to refresh the environment variables they know about.

Now this gives us our Clang compiler setup, but we also need a second arm compiler. Get the latest Windows installer package from here: https://launchpad.net/gcc-arm-embedded/+download and extract it to `C:\Users\<yourname>\blue-devenv` but be aware that the installer will try to add the version number as a subdirectory and we don't want that so be sure to edit the path before installing.

You should end up with `C:\Users\<yourname>\blue-devenv\arm-none-eabi` plus some other subdirectories like bin, lib and share also directly under the blue-devenv directory. If you got `C:\Users\<yourname>\blue-devenv\arm-none-eabi\5_3-2016q1\` or something similar then delete it and try again.

Congratulations, you finally have the main tools set up and we can leave that directory. Now let's see if we can build this thing and install it to the Nano S.


### Install Blue Loader

To install the application we need to first add a virtual environment to the machine. Open `C:\Users\<yourname>\nanos-bitsquare` in Git Gui and open a Git Bash shell.

Reference: https://github.com/LedgerHQ/blue-loader-python (for procedure)

- Install virtualenv: `virtualenv ledger`
- Run virtualenv:  `source ledger/scripts/activate`
- Install Visual C++ 9 for Python: https://www.microsoft.com/en-au/download/details.aspx?id=44266
- Install ledgerblue: `pip install ledgerblue`


### Building the sample application

From `C:\Users\<yourname>\nanos-bitsquare` type

`make`


### Install firmware (sample application) into Nano S

From the virtual environment, type

`python -m ledgerblue.loadApp --targetId 0x31100002 --apdu --fileName ./bin/token.hex --appName Bitsquare --appFlags 0x00 --icon "010000000000ffffffffffffffffff1ff11ff18ff177ef111111119199efff1ff11ff19ff9ffffffff"`

or more simply

`make load`


### Uninstall firmware from Nano S

If you wish to remove the app from your device, type

`python -m ledgerblue.deleteApp --targetId 0x31100002 --appName Bitsquare`

or more simply

`make delete`


### Exiting the Python virtual environment

Type `deactivate` or simply close the Bash window.


-------------------------------

![Screenshots](http://i.imgur.com/poHFClD.jpg)


-------------------------------

## Further notes 


### Working with Glyphs

Create an icon: Edit any icon, save as 16x16 1-bit gif. In Gimp it's Image->Mode->Indexed and click 1-bit.

Check an icon: `python icon.py 16 16 icon.gif hexbitmaponly`


### Working with Docker

Don't. Just don't.

Windows 32-bit was never supported and the last version it was "hacked" into is ancient. Such tools are an abomination, a waste of space and cause more problems than they solve. So, just don't.


### secp256k1 cryptography

Reference: https://github.com/LedgerHQ/blue-loader-python

The above page references the secp256k1 experimental elliptic curve cryptography package. In my experience this package is irretrievably borked and the developer is unresponsive to reports of problems. Installing it also leads us into Linux dependency hell, since it requires pkgconfig which in turn requires glib and gettext_runtime. So, just don't.
