StreamList 0.2.0
NTFS alternate data streams for Total Commander (file system plugin, 32/64 bit)
Author: Native2904 (TC forum / GitHub)   License: MIT


WHAT ARE ALTERNATE DATA STREAMS?
--------------------------------
On NTFS, a file can carry additional hidden "sub-files" besides its normal
content. Explorer does not show them and the file size does not include them,
but they stay with the file as long as it remains on NTFS.

You almost certainly have some: browsers attach a stream named
"Zone.Identifier" to every download. It contains the zone (Internet) and
often the address the file came from - this is where the Windows warning
"This file came from another computer" comes from.

Try it yourself (command prompt):
  echo Main content > test.txt
  echo Hello from the stream > test.txt:secret
  dir /r


REQUIREMENTS
------------
Total Commander 7.5 or later, Windows 7 or later, NTFS drives.


INSTALLATION
------------
Open the archive in Total Commander and confirm the installation.
StreamList then appears in the Network Neighborhood.


USAGE
-----
Network Neighborhood -> StreamList shows your NTFS drives. Below that you
browse normal folders, but files are shown as folders: Enter on a file
opens the list of its streams.

Direct entry from a normal panel - add to usercmd.ini (next to wincmd.ini):

  [em_StreamList]
  cmd=cd
  param=\\\StreamList\%P%N

Then assign a hotkey under Configuration -> Options -> Misc.
Use the hotkey in a normal panel; inside StreamList simply press Enter.

Inside a file (stream view):
  F3          view a stream
  F4          edit a stream - saving writes it back into the file
  F5          copy streams out (they become normal files)
  F5 into     copy normal files into the stream view: they become streams
  F7          create a new, empty stream
  Shift+F6    rename a stream
  F6          move streams out
  F8          delete streams
  Alt+Enter   stream info; on a file in the folder view: Explorer properties


COLUMNS
-------
On entering, StreamList shows: Size | Date | Streams | Content | Origin
  Folder view:  number of streams, their names, download address
  Stream view:  first text line of each stream (binary streams as hex)

Fields for your own column sets ([=streamlist.<field>]):
  Streams, StreamNames, Origin, Referrer, Zone, Content, Preview, Kind

Content plugins of other authors (e.g. ntfs_diz, xytags) also work in the
folder view, because StreamList reports the real file behind each entry.


SAFETY
------
- StreamList never deletes, renames or creates real files or folders.
- Streams can only be deleted or moved from inside their file's stream view.
  F8 on a file or folder in the folder view only shows a notice.
- The date/time of a file is kept when its streams are changed (KeepTime).
- Read-only files: streams cannot be written; StreamList shows a notice.


NTFS_DIZ COMPATIBILITY
----------------------
ntfs_diz stores each comment field as a stream with the field's name
(Comment, Comment1, ...). StreamList shows these streams and lets you read
and edit them - both plugins can be used side by side.


SETTINGS (StreamList.ini, next to the plugin)
---------------------------------------------
  [Options]  KeepTime=1     1 = keep the file's date when streams change
  [Settings] Language=auto  auto = TC language, or eng/deu/rus/ukr/dan
  [Debug]    Log=0          1 = write StreamList_debug.log next to the plugin


GOOD TO KNOW
------------
- Streams are lost when a file is copied to FAT32/exFAT, sent by e-mail or
  uploaded to many cloud services.
- If Total Commander does not copy streams, see the CopyStreams option in
  wincmd.ini (TC help).
- Network and CD drives are not listed in the root, but can be opened via
  the hotkey or cd \\\StreamList\X:\...
- Streams of folders are not shown yet.
- Pressing the hotkey inside StreamList works, but TC then shows the plugin
  name twice in the path. Inside the plugin, use Enter instead.


TRANSLATIONS
------------
StreamList.lng contains English, German, Russian, Ukrainian and Danish.
New languages are welcome - please send them via the TC forum or GitHub.


HISTORY
-------
0.2.0  Own columns and default view, content plugins of other authors in
       the folder view, icon, notices instead of generic errors
0.1.1  Hotkey also works inside the plugin
0.1.0  First version: list, view, edit, add, rename, delete streams
