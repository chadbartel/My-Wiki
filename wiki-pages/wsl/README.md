# WSL

This is the root wiki page for WSL (Windows Subsystem for Linux).

## Table of Contents

* [Return to the main page...](../../README.md)
* [Summary](#summary)
* [Install WSL on Windows 10](#install-wsl-on-windows-10)

### Summary

From [Wikipedia](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux):

> **Windows Subsystem for Linux** (WSL) is a compatibility layer for running Linux binary executables (in ELF format) natively on Windows 10 and Windows Server 2019. \[...\] The first release of WSL provides a Linux-compatible kernel interface developed by Microsoft, containing no Linux kernel code, which can then run a GNU user space on top of it, such as that of Ubuntu, openSUSE, SUSE Linux Enterprise Server, Debian and Kali Linux.

### Install WSL on Windows 10

Probably the best way to install WSL on your system is to follow this guide [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10). However, this is a *slightly less verbose* version of the same steps in the guide linked previously.

#### Simplified Install

1. Complete the following prerequisites:

    * Join the [Windows Insider Program](https://insider.windows.com/getting-started)
    * Install a preview build of Windows 10 (OS build 20262 or higher)
    * Open a command line windows with Administrator privileges

2. Install WSL by running the following command in an Admin-elevated command line and then **restart your machine**:

    ```cmd
        wsl.exe --install
    ```

    * The `--install` command performs the following:
        * Enables the optional WSL and Virtual Machine Platform components
        * Downloads and installs the latest Linux kernel
        * Sets WSL 2 as the default
        * Downloads and installs a Linux distribution (*reboot may be required*)

3. Launch the newly installed Linux distribution and [create a user account and password when prompted](https://docs.microsoft.com/en-us/windows/wsl/user-support).

    * The default installed Linux distribution is Ubuntu. You can change this by executing `wsl --install -d <Distribution Name>`. You can see a list of available Linux distributions with `wsl --list --online`.

#### Manual Install

1. Open PowerShell as Administrator and Enable the Windows Subsystem for Linux:

    ```PowerShell
        dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    ```

2. Check requirements for running WSL 2:

    * For x64 systems: Version 1903 or higher, with Build 18362 or higher.
    * For ARM64 systems: Version 2004 or higher, with Build 19041 or higher.
    * Builds lower than 18362 do not support WSL 2. Use the [Windows Update Assistant](https://www.microsoft.com/en-us/software-download/windows10) to update your version of Windows.

3. Open PowerShell as Administrator, Enable Virtual Machine feature, then restart your machine:

    ```PowerShell
        dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
    ```

4. Download the latest [WSL2 Linux kernel update package for x64 machines](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) and run the downloaded update package.

5. Open PowerShell as Administrator and set WSL 2 as your default version:

    ```PowerShell
        wsl --set-default-version 2
    ```

6. Open the [Microsoft Store](https://aka.ms/wslstore), select your favorite Linux distribution, and click "Get" to install it.

You can execute `wsl --help` in a terminal to get more information on how to use WSL.
