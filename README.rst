===================================================
Midnight Commander virtual filesystem for C64 disks
===================================================

This extfs provides an access to disk image files for the Commodore
VIC20/C64/C128 for Midnight Commander. It requires the utility c1541 that comes
bundled with Vice, the emulator for the VIC20, C64, C128 and other computers
made by Commodore.

Features
========

* View, remove, copy files in and out of the image,
* Support for read from and write to d64, d71 and d81 images,
* Gzipped disk images support,
* On fly characters remapping (see below).

Remarks
=======

Due to different way of representing file entries on regular Commodore disk
images, there could be issues with filenames that are transfered from/to the
image. Following rules was applied to represent a single file entry:

1. An extension is attached to the end of a filename depending on a file type.
   Possible extensions are: ``prg``, ``del``, ``seq``, ``usr`` and ``rel``.
2. Every non-ASCII character (which could be some of characters specific to
   PET-ASCII, or be a control character) will be replaced by dot (``.``), since
   ``c1541`` program will list them that way.
3. Every slash character (``/``) will be replaced by pipe character (``|``).
4. Leading space will be replaced by tilda (``~``).

While copying from disk image to filesystem, filenames will be stored as they
are seen on a listing.

While copying from filesystem to disk image, filename conversion will be done:

1. Every ``$`` and ``*`` characters will be replaced by question mark (``?``)
2. Every pipe (``|``) and backslash (``\``) characters will be replaced by
   slash (``/``)
3. Every tilde (``~``) will be replaced by a space
4. ``prg`` extension will be truncated

Representation of a directory can be sometimes confusing - in case when one
copied file without extension it stays there in such form, till next access
(after flushing VFS). Also file sizes are not accurate, since directory entries
in CBM format have sizes stored as 256 bytes blocks.

Screens below shows how it looks like on real C64 machine and its representation
on Midnight Commander listing.

|image1| |image2|

In ``media`` directory you can find the ``test.d6z`` gzip compressed D64 image
file.

Rquirements
===========

* Python 3.6 or higher (checked recently with 3.11)
* Vice installation (c1541 program in path)

Installation
============

* copy ``uc1541`` to ``~/.local/share/mc/extfs.d/``
* add or change entry for files handle in ``~/.config/mc/mc.ext``:

.. code:: ini

   # Disk images for Commodore computers (VIC20, C64, C128)
   [d64]
   Regex=\.(d64|d71|d81)$
   RegexIgnoreCase=true
   Open=%cd %p/uc1541://
   View=%view{ascii} c1541 %f -list

   [d6z]
   Regex=\.(d[678]z)$
   RegexIgnoreCase=true
   Open=%cd %p/uc1541://
   View=%view{ascii} t=$(mktemp); zcat %f > ${t}; c1541 ${t} -list 2>/dev/null; rm ${t}
   

Configuration
=============

Here are specific for this script variable, which while set, can influence
script behaviour:

* ``UC1541_DEBUG`` - if set, uc1541 will produce log in /tmp/uc1541.log file.
* ``UC1541_VERBOSE`` - if set, script will be more verbose, i.e. error messages
  form c1541 program will be passed to Midnight Commander, so that user will be
  aware of error cause if any.
* ``UC1541_HIDE_DEL`` - if set, no DEL entries will be shown.

Changelog
=========

* **3.6** Fixed line matcher for directory entries.
* **3.5** Drop Python2 support.
* **3.4** Code cleanup. Removed dummy logger class and sys.args based argument
  parsing.
* **3.3** Added support for .d71 and .d81 disk images.
* **3.2** Changed shebang to ``python`` executable instead of ``python3``
* **3.1** Argparse on Python3 have different behaviour, where if no subcommand
  is provided, it will pass anyway. Fixed.
* **3.0** Added beta quality Python3 support
* **2.8** Treat non standard discs a bit better
* **2.7** Added support for gzipped disk images
* **2.6** Added mkdir and run handling (or rather lack of handling :). Minor
  refactoring.
* **2.5** Fixed bug with filenames started with a '-' sign.
* **2.4** Fixed endless loop bug for reading directory in Python implemented
  directory reader.
* **2.3** Re added and missing method _correct_fname used for writing files
  into d64 image.
* **2.2** Fixed bug(?) with unusual sector end (marked as sector 0, not 255),
  causing endless directory reading on random locations.
* **2.1** Fixed bug with filenames containing slash.
* **2.0** Added reading raw D64 image, and mapping for jokers. Now it is
  possible to read files with PET-ASCII/control sequences in filenames. Working
  with d64 images only. Added workaround for space at the beginning of the
  filename.
* **1.2** Added configuration env variables: ``UC1541_VERBOSE`` and
  ``UC1541_HIDE_DEL``.  First one, if set to any value, will cause that error
  messages from ``c1541`` program will be redirected as a failure messages
  visible in MC.
  The other variable, when set to any value, cause *del* entries to be not
  shown in the lister.
* **1.1** Added protect bits, added failsafe for ``argparse`` module
* **1.0** Initial release

License
=======

This software is licensed under 3-clause BSD license. See LICENSE file for
details.

.. |image1| image:: /media/vice_dir.png?raw=true
.. |image2| image:: /media/mc.png?raw=true
