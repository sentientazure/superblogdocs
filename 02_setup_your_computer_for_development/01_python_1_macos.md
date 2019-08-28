# MacOS Setup

### Installing `Homebrew`

If you don't already have Homebrew installed, run the following command to install it:

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Homebrew is a software package management system that simplifies the installation of software on Apple's macOS operating system. Homebrew installs the stuff you need that Apple didn’t in a better and cleaner way. We're using it here to install Python 3. ([read more about Homebrew](https://brew.sh))

### Installing Python 3

If you don't already have Python 3 installed, run the following command to install it:

```bash
$ brew install python3
```

Python 3 is the newest version of the Python programming language. The latest version of Django runs on Python 3.

### Installing `pip`

If you don't already have pip installed, run the following command to install it.

```bash
$ sudo easy_install pip
```

This'll ask you to enter your password. Just type it and hit Enter, it won't display what you're typing.
