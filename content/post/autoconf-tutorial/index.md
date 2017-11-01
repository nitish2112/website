+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "Getting Started with Autotools"
tags = ["autotools"]

[header]
  caption = ""
  image = ""

+++

This is an introduction tutorial to autoconf, automake and libtool, the three
basic tools that you find in most of the build systems. 
<!--more-->


# Auto-Tools
Auto-tools mainly consist of these 3 basic set of tools:

## Autoconf 
It is a set of the following tools:

**autoconf** : Autoconf is used to generate the configure scripts from 
configure.ac

**autoheader** : It is used to generate C header files (.h files) from header
file templates (.h.in files)

**autom4te** : It is mainly used for caching.

**autoreconf** : It is used to run the different tools in the right order.

**autoscan** : It basically scans the progect and generates a reasonable 
configure.ac for a new project.

**autoupdate** : It is used to update configure.ac and template (*.in files) 
into the current version of autotools.

**ifnames** :


