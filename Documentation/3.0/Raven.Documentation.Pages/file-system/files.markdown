﻿#Files

RavenFS can stores data by using one of the following storage engines: Esent or Voron. You can choose that at the moment of creating a new file system.

##What is a file?

A file in Raven File System consists of:

* name (full path),
* total size,
* uploaded size,
* metadata,
* sequence of bytes that make up file content.

##Pages

Internally each file is divided into pages. A page is a sequence of bytes, its maximum size is 64KB and has an unique identifier - a pair of hashes calculated on a page content.
The concept of pages implicates a few facts:

* stored pages are unique,
* file content is an ordered list of page references,
* single page might be referenced by multiple files,
* pages are immutable - once they are written to storage, they cannot be modified (a page is removed if there is no file referencing it),
* occupied disk space is reduced if files have common information (or even a single file that has repeated data patterns).

##Directories

In RavenFS directories are just a virtual concept. The directory tree is built upon names of existing files. A file name must be a full path e.g. `/docs/pics/wall.jpg`.
A directory part of a file name is indexed together with the file metadata what allows you to browse files by catalogs - you just need to query an appropriate index entry field. 
Note that moving a file between directories is actually the rename operation.

##Default metadata

Each file has an associated collection of properties called metadata. A user can attach any information about a file by adding another metadata record. 
Some properties are defined by RavenFS itself because they are necessary for internal work. This is metadata of a sample file:

{CODE-BLOCK:json}
{
	ETag: "00000000-0000-0100-0000-000000000002",
	Content-MD5: "0d7a08e7f58bfe020c59d739911ee519",
	RavenFS-Size: 23552,
	Raven-Creation-Date: 2015-02-09T12:20:06.7257923+00:00,
	Raven-Last-Modified: 2015-02-09T12:20:06.7669533+00:00,
	Raven-Synchronization-Version: 1,
	Raven-Synchronization-Source: c6230a52-d1d7-4ea0-9942-6312431f32a1
	Raven-Synchronization-History: [],
}
{CODE-BLOCK/}



* `ETag` is an internal file identifier, updated every time if a file is modified. The file is considered as modified when new content is uploaded, a name or its metadata are changed or any of those changes has been synchronized from a remote file system,
* `Content-MD5` is a hash of file content, calculated on the fly during an upload by using MD5 algorithm,
* `RavenFS-Size` is a total size of a file,   
* `Raven-Creation-Date`,  `Raven-Last-Modified` - dates of creation and last file modification,
* `Raven-Synchronization-Version` is a number describing a file version in a file system,
* `Raven-Synchronization-Source` is an unique identifier of an origin file server (where a last file modification has been made),
* `Raven-Synchronization-History` is a list that consists of previous {`Raven-Synchronization-Version`, `Raven-Synchronization-Source`} pairs,
 updated every time a file is modified or synchronized between servers.

{INFO: Updating synchronization history}
`Raven-Synchronization-Version`, `Raven-Synchronization-Source` and `Raven-Synchronization-History` are always updated together.
Existing `Raven-Synchronization-Version`, `Raven-Synchronization-Source` values are added to the history array (`Raven-Synchronization-History`)
and new values are assigned. All of those properties, according to their names, are utilized for synchronization purposes (dealing with conflicts).
{INFO/}