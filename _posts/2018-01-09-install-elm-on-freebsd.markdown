---
layout: post
title: Install ELM on Freebsd
date: 2018-01-09 22:00:00 +0100
description: How to install ELM on Freebsd. # Add post description (optional)
img: freebsd_elm.png # Add image post (optional)
tags: [freebsd, elm]
---
## What we want
To install ELM on Freebsd
## Problem
There is no port for ELM.

So we can't use
```
pkg install something
```
nor
 ```
cd /usr/ports/lang/elm && make clean install
```
## Solution
Install ELM from the source.

## Pre-requisites
* Having wget installed (if not => **pkg install wget**)

## First step : Install Haskell/ghc
We need to install Haskell to compile the ELM sources.
The right version here is ghc 7.10.3 since the latest version cannot be used to install ELM v18

**(Some of the following instructions have been retrieved from the great article on how to install ghc on freebsd : https://ryanglscott.github.io/2014/07/16/installing-ghc-on-freebsd/)
N.B. : Some adpatations have been made to meet the ELM install**

Install and configure gcc:
```
# Install compatibility package
$ pkg install compat8x-amd64
# Install gcc
$ pkg install gcc
# it gives you gcc6
```
Download ghc archive (size is 72.76M):
```
$ cd /tmp
$ wget https://downloads.haskell.org/~ghc/7.10.3/ghc-7.10.3-x86_64-portbld-freebsd.tar.xz
```
Extract archive
```
$ tar -xf ghc-7.10.3-x86_64-portbld-freebsd.tar.xz
$ cd ghc-7.10.3
```
Make install ghc
```
$ env CC=gcc6 ./configure --with-gcc=gcc6 --with-ld=/usr/local/bin/ld
$ gmake install
```
Check that ghc and Haskell are well installed:
```
$ ghc --version
The Glorious Glasgow Haskell Compilation System, version 7.10.3

$ runhaskell --version
runghc 7.10.3
```

## Second step : Install CABAL
We need cabal to manage haskell packages (see https://www.freshports.org/devel/hs-cabal-install)

Install cabal package
```
$ pkg install hs-cabal-install
```

## Third step : Build ELM from source
Create elm folder
```
$ mkdir -p /usr/local/elm
$ cd /usr/local/elm/
```
Get ELM source code
```
$ curl https://raw.githubusercontent.com/elm-lang/elm-platform/master/installers/BuildFromSource.hs > BuildFromSource.hs
```

Run haskell to build ELM from source
```
$ runhaskell BuildFromSource.hs 0.18
# about 10 minutes... (yes, that long)

```

## Last step : Configure path
Change Path to PATH="$PATH:/usr/local/elm/Elm-Platform/0.18/.cabal-sandbox/bin"

Last check:
```
$ elm --version
0.18.0
```

**The end**
