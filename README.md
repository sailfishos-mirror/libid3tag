
libid3tag - ID3 tag manipulation library

Copyright (C) 2000-2004 Underbit Technologies, Inc.

Copyright (C) 2021-2024 Tenacity Team and Contributors

\$Id: README,v 1.5 2004/01/23 09:41:32 rob Exp \$

# Introduction

libid3tag is a library for reading and (eventually) writing ID3 tags, both ID3v1 and the various versions of ID3v2.

See the file `id3tag.h' for the current library interface.

# Building and Installing

libid3tag depends on zlib. On Linux, install the appropriate development
packages for zlib using your system's package manager (e.g., apt, dnf, pacman).
On Windows you can use vcpkg, which will build it from source. Alternatively,
you can use any MSYS2 environment of your choice or Cygwin.

libid3tag uses the CMake build system. To build it, no matter your platform,
run:

``` bash
$ cmake -DCMAKE_INSTALL_PREFIX=/where/you/want/to/install/to -S . -B build # Configures command
$ cmake --build build --parallel number-of-cpu-cores # Build command
$ cmake --install build # Install command
```

If using vcpkg, add
`-DCMAKE_TOOLCHAIN_FILE=C:/Path/To/vcpkg/scripts/buildsystems/vcpkg.cmake` to
the end of your build command.

To build libid3tag as a static library, simply add `-DBUILD_SHARED_LIBS=OFF` at
the end of your build command.

# Copyright

Please read the `COPYRIGHT` file for copyright and warranty information.
Also, the file `COPYING` contains the full text of the GNU GPL.

Send inquiries, comments, bug reports, suggestions, patches, etc. to
https://codeberg.org/tenacityteam/libid3tag.

See also Tenacity's homepage on the web: http://tenacityaudio.org

Finally, check out libid3tag's original homepage on the web, on Underbit's
website: https://www.underbit.com/products/mad/
