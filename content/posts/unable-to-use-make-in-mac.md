---
title: "Unable to use CLI tools for Xcode"
date: 2020-12-01T20:57:21-06:00
draft: false
tags:
 - cli
 - xcode
 - mac
 - tools

categories:
 - Hard learned lessons
---
Trying to use [GNU Make](https://www.gnu.org/software/make/) in my Mac, I was stuck:
```shell
% make test
xcrun: error: active developer path ("/Applications/Xcode.app/Contents/Developer") does not exist
Use `sudo xcode-select --switch path/to/Xcode.app` to specify the Xcode that you wish to use for command line developer tools, or use `xcode-select --install` to install the standalone command line developer tools.
```
The error message made it sound like I didn't had Xcode nor the CLI tools installed (I had CLI tools installed).
When I tried the command suggested by the error: I already had CLI tools installed.
```shell
% xcode-select --install
xcode-select: error: command line tools are already installed, use "Software Update" to install updates
```
I confirmed there was no 'Software Update' pending, so I hit a wall.

I tried a couple of times uninstalling the CLI tools and installing again, but ended up in the same place.
```shell
% sudo rm -rf /Library/Developer/CommandLineTools
Password:
% xcode-select --install
xcode-select: note: install requested for command line developer tools
```
What sucks is that I could see working binaries in `/Library/Developer/CommandLineTools/usr/bin/`
but the default binary being used was pointing somewhere else (confirmed via `which make`).

What ultimately resolved it was:
```shell
% sudo xcode-select --reset
```

Nothing like copy-pasting lines from [Stack Overflow](https://stackoverflow.com/a/53089500) into the CLI in sudo.

