===================================================
Midnight Commander virtual filesystem for d64 files
===================================================

This extfs provides an access to disk image files for the Commodore
VIC20/C64/C128 for Midnight Commander. It requires the utility c1541 that comes
bundled with Vice, the emulator for the VIC20, C64, C128 and other computers
made by Commodore.

Features
========

* view, remove, copy files in and out of the image
* support for read from and write to d64 images
* gzipped disk images support
* onfly characters remapping (see below)

Remarks
=======

Due to different way of representing file entries on regular D64 disk images,
there could be issues with filenames that are transfered from/to the image.
Following rules was applied to represent a single file entry:

1. An extension is attached to the end of a filename depending on a file type.
   Possible extensions are: ``prg``, ``del``, ``seq``, ``usr`` and ``rel``.
2. Every non-ASCII character (which could be some of characters specific to
   PET-ASCII, or be a control character) will be replaced by dot (``.``), since
   ``c1541`` program will list them that way.
3. Every slash character (``/``) will be replaced by pipe character (``|``).
4. Leading space will be replaced by tilda (``~``).

While copying from D64 image to filesystem, filenames will be stored as they
are seen on a listing.

While copying from filesystem to D64 image, filename conversion will be done:

1. Every ``$`` and ``*`` characters will be replaced by question mark (``?``)
2. Every pipe (``|``) and backslash (``\``) characters will be replaced by
   slash (``/``)
3. Every tilda (``~``) will be replaced by a space
4. ``prg`` extension will be truncated

Representation of a directory can be sometimes confusing - in case when one
copied file without extension it stays there in such form, till next access
(after flushing VFS). Also file sizes are not accurate, since D64 directory
entries have sizes stored as 256 bytes blocks.

Rquirements
===========

* Python 2.7
* Vice installation (c1541 program in path)

Installation
============

* copy ``uc1541`` to ``~/.local/share/mc/extfs.d/``
* add or change entry for files handle in ``~/.config/mc/mc.ext``::

    # Disk images for Commodore computers (VIC20, C64, C128)
    regex/\.(d64|D64|d6z|D6z|d6Z|D6Z)$
        Open=%cd %p/uc1541://
        View=%view{ascii} c1541 %f -list
        Extract=c1541 %f -extract

Configuration
=============

Here are specific for this script variable, which while set, can influence
script behaviour:

* ``UC1541_DEBUG`` - if set, uc1541 will produce log in /tmp/uc1541.log file.
* ``UC1541_VERBOSE`` - if set, script will be more verbose, i.e. error messages
  form c1541 program will be passed to Midnight Commander, so that user will be
  aware of error cause if any.
* ``UC1541_HIDE_DEL`` - if set, no DEL entries will be shown.

License
=======

This software is licensed under 3-clause BSD license. See LICENSE file for
details.