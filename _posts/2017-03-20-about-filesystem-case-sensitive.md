---
title: 关于MacOS文件系统的文件名大小写问题
date: 2017-03-20 13:52:00
tags: 
  - 操作系统
  - 文件系统
categories: 操作系统
---

## 背景


## 现状


| 操作系统  | 文件系统     | 是否大小写敏感   |
|----------|-------------|----------------|
| Unix     |             | 是             |
| Linux    |             | 是             |
| MacOS    | 日志式 HFS+  | 默认否         |
| Windows  | FAT32 NTFS  | 默认否         |

> HFS+ (the Mac filesystem) is usually configured to be case insensitive but case preserving. This means that the file system will consider foo and FoO to be the same, but when you create a new file it will remember which letters where capitalized and which were not.
> To check whether a disk is case sensitive, run for example:
> `diskutil info disk0s2`
> Look for the Name: line. If it reads something like Mac OS Extended (Case-sensitive, Journaled) it means that it is case-sensitive. If it just reads Mac OS Extended (without the Case-sensitive) then it is only case preserving but not case sensitive.
> [Is bash in OSX case-insensitive?](http://apple.stackexchange.com/questions/22297/is-bash-in-osx-case-insensitive)

