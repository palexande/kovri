## Step 1. Minimum requirements

### Linux / MacOSX (10.9.5) / FreeBSD 10
- [Boost](http://www.boost.org/) 1.58
- [CMake](https://cmake.org/) 2.8.12
- [GCC](https://gcc.gnu.org/) 5.3.0
- [OpenSSL](https://openssl.org/) (always the latest stable version)

### Windows
- [MSYS2](https://msys2.github.io/)
- [MinGW-w64](http://mingw-w64.org/doku.php)

Optional:

- [Clang](http://clang.llvm.org/) 3.6
- [Doxygen](http://www.doxygen.org/)
- [MiniUPnP](http://miniupnp.free.fr/files/)

### MacOSX
- [Homebrew](http://brew.sh/)

## Step 2. Install dependencies

### Debian / Ubuntu
```bash
$ sudo apt-get install g++-5 cmake libboost-all-dev libssl-dev libssl1.0.0
$ sudo apt-get install libminiupnpc-dev doxygen  # optional
```

### Ubuntu Trusty 14.04:

```bash
$ sudo add-apt-repository ppa:ubuntu-toolchain-r/test
$ sudo add-apt-repository ppa:kojoley/boost
$ sudo apt-get update
$ sudo apt-get install libboost-{chrono,log,program-options,date-time,thread,system,filesystem,regex,test}1.58{-dev,.0}
$ sudo apt-get install g++-5 cmake libboost-all-dev libssl-dev libssl1.0.0
$ sudo apt-get install libminiupnpc-dev doxygen  # optional

```

### Arch Linux
```bash
$ sudo pacman -Syu cmake boost  # gcc/g++ and openssl installed by default
$ sudo pacman -S miniupnpc doxygen  # optional
```

### MacOSX
```bash
$ export CXXFLAGS="-maes -march=native"  # weidai11/cryptopp#232
$ brew install cmake boost openssl
$ brew install miniupnpc doxygen  # optional
```

### Windows
* Download the [MSYS2 installer](http://msys2.github.io/), 64-bit or 32-bit as needed, and run it.
* Use the shortcut associated with your architecture to launch the MSYS2 environment. On 64-bit systems that would be the MinGW-w64 Win64 Shell shortcut. Note that if you are running 64-bit Windows, you will have both 64-bit and 32-bit environments.
* Update the packages in your MSYS2 install:
```
pacman -Sy
pacman -Su --ignoregroup base
pacman -Su
```
* For those of you already familiar with pacman, you can run the normal ```pacman -Syu``` to update, but you may get errors and need to restart MSYS2 if pacman's dependencies are updated.
* Install dependencies: ```pacman -S make mingw-w64-x86_64-cmake mingw-w64-x86_64-gcc mingw-w64-x86_64-boost mingw-w64-x86_64-openssl```
* Optional: ```mingw-w64-x86_64-doxygen mingw-w64-x86_64-miniupnpc```

### FreeBSD 10
Currently unsupported and requires building Boost 1.58.
We're working on it, stay tuned!

## Step 3. Build
Minimum requirement:
```bash
$ git clone --recursive https://github.com/monero-project/kovri
$ make dependencies && make && make install-resources # to decrease build-time, run make -j [available CPU cores]
```
- End-users MUST run ```make dependencies``` and ```make install-resources``` for new installations
- Developers SHOULD run ```make dependencies``` and ```make install-resources``` after a fresh fetch

Other options:

- ```make static``` produces static binary

- ```make upnp``` produces vanilla binary with UPnP support (requires [MiniUPnP](http://miniupnp.free.fr/files/))
- ```make optimized-hardening``` produces optimized, hardened binary
- ```make all-options``` produces optimized, hardened, UPnP enabled binary

- ```make tests``` produces all unit-tests and benchmarks
- ```make doxygen``` produces Doxygen documentation (output will be in doc/Doxygen)

- ```make help``` shows available CMake build options
- ```make clean``` between subsequent builds

All build output will be in the build directory.

### Clang
To build with clang, you must export the following:

```bash
$ export CC=clang CXX=clang++
$ export CXXFLAGS="-maes -march=native"  # weidai11/cryptopp#232
```

Replace ```clang``` with a clang version/path of your choosing.

### Clang for Ubuntu Trusty 14.04:

```bash
# We currently need clang 3.6.2 minimum requirement
- curl -sSL "http://llvm.org/apt/llvm-snapshot.gpg.key" | sudo -E apt-key add -
- sudo add-apt-repository -y "deb http://llvm.org/apt/precise/ llvm-toolchain-precise-3.6 main"
- sudo apt-get -q update
- sudo apt-get -y install clang-3.6

```

### Custom data path
You can customize Kovri's data path to your liking. Simply export ```KOVRI_DATA_PATH```; example:

```bash
$ export KOVRI_DATA_PATH=$HOME/.another-kovri-data-path && make && make install-resources
```

## Step 4. Open your NAT/Firewall
1. Choose a port between ```9111``` and ```30777```
2. Poke a hole in your NAT/Firewall to allow incoming TCP/UDP connections to that port
3. Don't share this number with anyone as it will effect your anonymity!

If you do not choose a port via cli or ```kovri.conf```, Kovri will randomly generate a new one on each startup. If you do not have access to your NAT, you can instead install and build with [MiniUPnP](http://miniupnp.free.fr/files/) support

## Step 5. Run Kovri
```bash
$ ./kovri -p [your chosen port]
```
or set your port in kovri.conf


For a full list of options:

```bash
$ ./kovri --help-with all
```

## Step 6. Configuration files *(optional)*

Configuration files has INI-like syntax: <key> = <value>.
All command-line parameters are allowed as keys, for example:

kovri.conf:

    log = 1
    v6 = 0
    ircdest = irc.dg.i2p

tunnels.conf:

    [IRC]
    type = client
    port = 6669
    destination = irc.dg.i2p
    keys = irc-keys.dat
