# Windows Setup

To run the command line in Windows as administrator:

1. Right Click on cmd.exe
2. Select "Run as an administrator"

### Installing `Chocolatey`

1. Go [here](https://chocolatey.org/install#install-with-cmdexe) to install Chocolatey.
2. Run the command on the page (should look like this: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile ...`)
3. Once it's done installing, restart the command line to be able to use it.

We need it to install Python 3

### Installing Python 3

Python 3 is the newest version of the Python programming language. The latest version of Django runs on Python 3. Run the command line as administrator, then run the following command:

```bash
$ choco install python
```

Restart the command line to be able to user Python 3.

### Installing `pip`

Run the following command in the command line:

```bash
$ choco install pip
```
