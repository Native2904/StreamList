# StreamList

**View, edit and clean up hidden NTFS data streams in Total Commander**

Version 1.2.1 · File system plugin (WFX) for 32 and 64 bit · Author: Native2904 · License: MIT

The chapters build on each other: from the basics of alternate data streams, through working with individual files, to drive-wide analysis with Everything. If you already know StreamList, you will find the key assignments, fields, and settings and protocol collected in chapters 5, 10 and 12.

---

## 1. What is a data stream?

NTFS stores the content of a file in a data stream. Besides this main data stream, a file can have any number of additional, named data streams – **alternate data streams** (ADS), simply called **streams** below. Explorer does not show them, and the displayed file size does not include them. When a file is copied or moved within NTFS, its streams nevertheless stay with it.

They are most commonly encountered as the download mark, often called *Mark of the Web*: browsers attach a stream `Zone.Identifier` to every downloaded file. It records the security zone the file came from and usually its source address. The warning Windows shows when opening such files is based on it.

This can be made visible at the command prompt with `dir /r`:

| Size | Name |
|---:|---|
| 4,858,880 | `alrext.exe` |
| 52 | `alrext.exe:Zone.Identifier:$DATA` |
| 87,552 | `ChmLib.dll` |
| 52 | `ChmLib.dll:Zone.Identifier:$DATA` |

Lines of the form `file:name:$DATA` are streams; `alrext.exe` therefore has a 52-byte stream `Zone.Identifier`.

StreamList presents these streams in Total Commander as ordinary entries that can be viewed, edited, created and deleted.

---

## 2. Installation

Requirements are Total Commander 7.5 or later, Windows 7 or later, and **Everything 1.5** by voidtools with indexed stream properties (set-up in chapter 8). Open the archive in Total Commander and confirm the installation; StreamList then appears in the **Network Neighborhood**.

Chapters 3 to 7 describe working with individual folders and files, chapter 8 the drive-wide analysis via Everything.

---

## 3. First overview

The entry **StreamList** in the Network Neighborhood shows the system's NTFS drives and two lists with a magnifier icon:

| Name | Streams |
|---|---:|
| 🔍 `! Free search` | |
| 🔍 `! All files with streams` | |
| 🔍 `! Downloads (Zone.Identifier)` | |
| 📁 `C` | ∑ 58,448 |
| 📁 `D` | ∑ 312 |

The lists are covered in chapter 8. First, the display of ordinary folders.

---

## 4. The folder view

Within StreamList, a folder looks as usual, and file operations behave as in a normal panel: F5 copies, F6 moves, F8 deletes the real files (chapter 6). There is one difference: **Enter on a file does not open the file, but its streams.**

Which files have streams at all is shown by the column view StreamList brings along when entered:

| Name | Size | Streams | Content | Origin |
|---|---:|---:|---|---|
| 📁 `Tools` | `<DIR>` | ∑ 31 | | |
| 📦 `setup.zip` | 2,418,330 | 1 | Zone.Identifier | https://example.org/… |
| 📄 `note.txt` | 1,204 | 2 | Comment, one | |
| 📄 `image.png` | 88,112 | | | |

- **Streams** gives the number of streams for files. For folders, marked with `∑`, it shows the number of files with streams in the whole subtree; this value comes from the Everything index (chapter 8).
- **Content** lists the names of the streams.
- **Origin** contains the source address from the `Zone.Identifier` for downloaded files.

Empty fields, as with `image.png`, mean that there are no streams.

If the **folder itself** has streams, an additional entry `[Streams]` appears at the top. Chapter 5 explains it.

---

## 5. The stream view of a file

Enter on `note.txt` switches to the stream view of this file:

| Name | Size | Content |
|---|---:|---|
| 📄 `Comment.txt` | 7 | Invoice |
| 📄 `one.txt` | 16 | first stream |

Here the **Content** column holds the first text line of each stream; binary streams are shown as a hexadecimal sequence, e.g. `4D 5A 90 00 …`.

The `.txt` extension is borrowed: the streams are actually called `Comment` and `one`. StreamList adds it to text streams without an extension of their own so that they can be opened with the usual programs after copying them out. When editing, deleting and renaming, StreamList always works with the real name. Streams with an extension (such as `Zone.Identifier`) and binary streams keep their names; `TxtExtension=0` switches this behaviour off.

The stream view is operated with the usual function keys:

| Key | Effect |
|---|---|
| F3 | view a stream |
| F4 | edit a stream – saving writes it back into the file |
| F5 | copy streams out; they become normal files |
| F5 from the other panel | copy normal files in; they become streams |
| F7 | create a new, empty stream |
| Shift+F6 | rename a stream |
| F6 | move streams out |
| F8 | delete streams |
| Alt+Enter | information about the stream |

Backspace returns to the folder. The protective rules that apply are described in the next chapter.

### Streams on folders

Not only files, folders too can carry streams. They are even less conspicuous than those of files and are therefore occasionally used as a hiding place. Since Enter on a folder leads into the folder, StreamList shows them via an entry of their own:

| Name | Streams |
|---|---:|
| 📁 `[Streams]` | 2 |
| 📁 `Subfolder` | ∑ 5 |
| 📄 `note.txt` | 1 |

`[Streams]` only appears if the opened folder itself has streams, and leads to an ordinary stream view with all function keys. Two more ways lead there: the command `em_StreamListFolder` (chapter 7) for the selected folder, and Alt+Enter on a folder with streams, which offers the choice of showing its streams or its properties.

---

## 6. Protective rules

StreamList distinguishes two levels, and the function keys always act only on the level currently displayed:

| Level | F5 / F6 / F8 / Shift+F6 act on … |
|---|---|
| Overview: folder view, file lists (chapter 8) | the real files and folders |
| Stream view of a file, stream lists (chapter 8) | the streams |

This results in the following principles:

- **Deleted files and folders go to the Recycle Bin.** In file system plugins deleting would otherwise be final; the Recycle Bin makes a slip reversible. Drives themselves are never deleted or renamed.
- **Copying takes the streams along.** A copied file keeps its streams on NTFS targets.
- **Streams are only deleted where they are explicitly listed as streams.** F8 on a file removes the file – never just some of its streams; conversely, F8 in the stream view can never hit the file itself.
- **Time stamps are preserved.** Writing to a stream changes the file's modification date on NTFS; StreamList restores it afterwards.
- **Read-only files** stay unchanged; StreamList states the reason.

Anyone who wants to use StreamList purely as a tool for streams switches the file operations off with `FileOperations=0`. StreamList then changes streams only and points to this setting when file operations are attempted in the overview.

---

## 7. Direct access by command

For everyday use, the way via the Network Neighborhood is cumbersome. Four user-defined commands in `usercmd.ini` (next to `wincmd.ini`) lead straight to the desired place:

```ini
[em_StreamList]
cmd=cd
param=\\\StreamList\%P%N

[em_StreamListDir]
cmd=cd
param=\\\StreamList\%P

[em_StreamListFolder]
cmd=cd
param=\\\StreamList\%P%N\[Streams]

[em_StreamListSearch]
cmd=cd
param=\\\StreamList\!find
```

Hotkeys can be assigned under *Configuration → Options → Misc.*; the commands can also be placed on buttons.

| Command | triggered in a normal panel on … | Target |
|---|---|---|
| `em_StreamList` | a file | stream view of this file |
| `em_StreamListDir` | any position | current folder as displayed by StreamList |
| `em_StreamListFolder` | a folder | streams of this folder (chapter 5) |
| `em_StreamListSearch` | any position | free search (chapter 9) |

The commands are meant for the normal panel; inside StreamList, Enter is sufficient.

Total Commander replaces placeholders such as `%P` and `%N` only in the **Parameter** field of a command or button, not in the **Command** field. If everything is entered there, the path arrives unchanged; StreamList detects this and points it out.

---

## 8. Drive-wide analysis with Everything

**Everything 1.5** by voidtools keeps an index of all files on a system and can also search it for streams. StreamList uses this index for the ∑ totals and for drive-wide lists that are available within a few seconds. Reading, writing and deleting the streams themselves is done by StreamList directly on the drive.

### One-time preparation

For Everything to search streams, two properties must be indexed (in Everything roughly under *Tools → Options → Indexes → Properties*):

- **Alternate Data Stream Names**
- **Alternate Data Stream Count**

The first indexing run takes some time; afterwards Everything keeps the index up to date by itself. If the properties are missing, StreamList points this out instead of starting a search that would take several minutes without an index.

### Totals per folder

The `∑` values mentioned in chapter 4 show where streams are concentrated:

| Name | Streams |
|---|---:|
| 📁 `Users` | ∑ 32,192 |
| 📁 `mingw64` | ∑ 23,044 |
| 📁 `tcmd` | ∑ 961 |
| 📁 `Windows` | ∑ 46 |
| 📁 `Program Files` | |

High values for unpacked programs such as `mingw64` are typical: when unpacking, Windows transfers the download mark of a ZIP archive to every file it contains.

### Lists in the root folder

The two lists from chapter 3 show the results of an Everything search across all drives as a flat list.

**`! All files with streams`** contains every file with at least one stream; Enter opens its stream view. Since the entries are real files, they can be copied, moved or deleted as in any search result list.

**`! Downloads (Zone.Identifier)`** is built differently: each entry here stands directly for the `Zone.Identifier` of a file.

| Name | Content | Folder |
|---|---|---|
| 📄 `__init__.py` | …\winlibs.zip | C:\mingw64\lib\python3.9\asyncio |
| 📄 `__init__ [2].py` | …\winlibs.zip | C:\mingw64\lib\python3.9\collections |
| 📦 `setup.zip` | https://example.org/… | C:\Users\…\Downloads |

The **Folder** column gives the location; files with the same name from different folders are distinguished by a suffix such as `[2]`.

### Removing download marks

Since every entry of this list is a stream, the function keys act directly: F3 shows the mark, **F8 removes it** – even for a large number of selected entries in one go, with Total Commander's usual confirmation and progress display.

The effect in detail:

| | before | after |
|---|---|---|
| File | `alrext.exe` | `alrext.exe` – unchanged, same date |
| Stream | `alrext.exe:Zone.Identifier` | removed |
| Windows | warns when opening | no longer warns |

The mark serves a protective purpose. For trusted programs you unpacked yourself it is dispensable; for files of unclear origin it is advisable to leave it in place.

---

## 9. Own lists

The two lists are defaults. Up to twenty searches of your own can be defined in `StreamList.ini` next to the plugin, each consisting of a name, an Everything search expression and optionally a stream name:

```ini
[Search1]
Name=! Comments (ntfs_diz)
Query=alternate-data-stream-names:comment
Stream=Comment
```

The entry `Stream=` determines the type of list:

| `Stream=` | Type of list | An entry is … | Enter / F8 |
|---|---|---|---|
| empty | file list | a file | Enter opens its streams; F5/F6/F8 act on the file |
| set | stream list | exactly this stream of the file | F3/F4/F8 act directly on the stream |

As soon as a section `[Search1]` exists, your own lists replace the defaults.

### Free search

For changing search terms – such as tag streams like `MyTag` – the root folder contains the entry **"! Free search"**. When entered, it shows the results of the last search used. At the top is the entry **"! New search…"**: Enter or a double-click on it asks for any Everything search; the last ten searches are offered for selection. The results appear as a file list: F5, F6 and F8 act on the files, Enter opens their streams, and the *Preview* column shows the content of the streams. The results stay until the next new search. The command `em_StreamListSearch` (chapter 7) leads directly to the list.

---

## 10. Own column views

For your own column views, StreamList provides the following fields:

| Field | shows … |
|---|---|
| `StreamCount` | number of streams; for folders their own streams and the ∑ total of their content, e.g. `1 · ∑ 5` |
| `StreamNames` | names of the streams of a file |
| `Content` | the most useful value for each level: names, first line or origin |
| `Origin` | download address (HostUrl) |
| `Referrer` | referring page or archive (ReferrerUrl) |
| `Zone` | Internet, Intranet, Local … |
| `Preview` | first text line of a stream; for files and folders the content of their streams in short form (without Zone.Identifier) – e.g. for tag streams such as `MyTag` |
| `Kind` | Text, Binary, Zone info, Empty – or **Program (EXE/DLL)** if a stream contains executable code. This is a well-known hiding place for malware and deserves a closer look. |
| `Folder` | folder of a file in the Everything lists |
| `Size` | size of the file or stream; replaces Total Commander's size column in the default view |
| `StreamsTotal` | total size of all streams of a file, or of a folder's own streams |
| `AllocatedSize` | space actually occupied by a stream or by all streams of a file or folder |
| `EntryModified` | last change of the NTFS entry – changes even when the file's modification date is preserved |
| `FullStreamName` | full name in the form `C:\…\file.txt:Comment` |

In the folder view, the fields of other **content plugins** are available as well, e.g. from ntfs_diz or xytags, since StreamList tells Total Commander the underlying file of each entry.

Note for users of a test version: Total Commander adopts a plugin's default view only once. If columns stay empty, delete the stored view; StreamList creates it anew the next time it is entered.

---

## 11. Working with ntfs_diz

ntfs_diz stores file comments as streams, each comment field in a stream of its own named after the field (`Comment`, `Comment1` etc.). In StreamList these comments therefore appear as ordinary streams that can be read with F3 and edited with F4. Both plugins can be used side by side.

---

## 12. Settings and protocol

The settings can be changed conveniently in a dialog: **right-click StreamList in the Network Neighborhood → Properties.** It also shows whether Everything is reachable and whether the two stream properties are indexed – the most common cause when the lists stay empty. Own lists (chapter 9) can be created, edited and tried out immediately with "Test" there.

The **?** in the title bar or **F1** opens this manual in the selected language, directly at the chapter for the control currently selected. For this purpose the manual is supplied as Windows help `StreamList_eng.chm` next to the plugin; StreamList removes a download mark from this file itself when opening it, otherwise its pages would stay blank.

After OK, StreamList reloads everything; the changes become visible the next time a list is read (Ctrl+R or changing folders). Restarting Total Commander is not necessary.

**Protocol.** StreamList records every file operation with date, time, action, source, target and result in the file `StreamList_operations.txt` next to the plugin. The button "Show protocol…" in the dialog opens it as a sortable, filterable list:

| Time | Action | Source | Result |
|---|---|---|---|
| 2026-10-05 14:03:12 | Stream deleted | `C:\…\setup.zip:Zone.Identifier` | OK |
| 2026-10-05 14:05:40 | Moved to Recycle Bin | `C:\Temp\old.txt` | OK |
| 2026-10-05 14:06:02 | Stream written | `C:\ADSTest\aa.txt:MyTag` | OK |

Merely viewing (F3) and the temporary copy when editing (F4) are not recorded, but writing back is. The file is tab-separated text and can also be opened in Lister or in a spreadsheet.

Dialog and file are equivalent: all settings are stored in `StreamList.ini` next to the plugin and can also be changed by hand. When saving from the dialog for the first time, the file is converted to UTF-16 so that list names in non-Latin scripts are preserved.

| Section | Entry | Meaning |
|---|---|---|
| `[Options]` | `KeepTime=1` | keep the file's date when changing streams |
| | `TxtExtension=1` | show text streams without extension as `.txt` (chapter 5) |
| | `FileOperations=1` | file operations on real files in the overview (chapter 6) |
| `[Protocol]` | `Enabled=1` | record file operations |
| | `MaxLines=10000` | at most this many entries; older ones are dropped |
| `[Settings]` | `Language=auto` | language of Total Commander, otherwise `eng`, `deu`, `rus`, `ukr`, `dan` |
| `[Everything]` | `Timeout=30000` | wait at most this long (ms) for Everything |
| | `MaxResults=100000` | at most this many entries per list |
| | `Exclude=!\$Recycle.Bin\` | appended to every search; hides the Recycle Bin |
| | `FolderQuery=…` | what is counted for the ∑ totals; empty switches them off |
| | `FolderCacheMinutes=5` | how long the totals are reused |
| `[Search1]` … `[Search20]` | | own lists (chapter 9) |

About `FolderQuery`: synchronisation services such as Dropbox give every file a stream of their own and then dominate the totals. With `FolderQuery=alternate-data-stream-names:zone.identifier` the totals are limited to download marks.

---

## 13. Notes and limits

- Streams are lost as soon as a file is copied to FAT32 or exFAT, sent by e-mail or uploaded to many cloud services.
- Whether Total Commander takes streams along when copying is controlled by the option `CopyStreams` (see Total Commander's help).
- Network and CD drives are not listed in the root folder, but can be reached via `em_StreamListDir`.
- Sizes and time stamps in columns are delivered as text, because Total Commander otherwise does not reliably display numeric values in folder rows. Sizes are therefore right-padded and time stamps written as `YYYY-MM-DD hh:mm:ss`; this also keeps sorting correct.
- The ∑ totals come from the Everything index, the values of individual files are read by StreamList directly from the drive. Immediately after changes, both may briefly differ.
- If Everything is not running, the ∑ totals stay empty and the lists show a note. StreamList tries again after about 30 seconds; restarting Total Commander is not necessary.

---

## 14. Troubleshooting

A **debug version** is available for troubleshooting. It logs to the file `StreamList_debug.log` next to the plugin; the first line shows which plugin and which settings file Total Commander actually loads:

```text
=== DEBUG-Build geladen: C:\…\StreamList.wfx64 | INI: C:\…\StreamList.ini (vorhanden) | Log=1 ===
```

Bug reports with this log and a short description are welcome via the Total Commander forum or GitHub. The regular version contains no logging code.

---

## 15. Translations

`StreamList.lng` contains English, German, Russian, Ukrainian and Danish. Contributions in further languages are gladly accepted via the Total Commander forum or GitHub.

---

## 16. Versions

| Version | Changes |
|---|---|
| 1.2.1 | Help as CHM in all five languages: F1 and ? reliably jump to the matching chapter, with contents and search |
| 1.2.0 | Free search: a new search is only started via the entry "! New search…" – the input dialog no longer opens unintentionally, e.g. when moving the mouse over it |
| 1.1.2 | Folders also show their own streams in StreamCount, StreamsTotal and AllocatedSize |
| 1.1.1 | Alt+Enter on a folder with streams: "Show streams" now reliably opens the stream view |
| 1.1.0 | Free search: any Everything search as a file list, with history |
| 1.0.1 | Column Preview shows the content of the streams of files and folders in the overview |
| 1.0.0 | First official version: stream view for files and folders, Everything lists and ∑ totals, file operations in the overview, borrowed `.txt` extension, columns comparable to AlternateStreamView, settings dialog with help, protocol |
| 0.1 – 0.7 | Preview versions for testers in the Total Commander forum |

---

<p class="signoff"><em>Made with love – and with <a href="https://somafm.com">SomaFM</a> playing in the background. ♥</em></p>
