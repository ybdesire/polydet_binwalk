# polydet_binwalk
a demo for polyglot file detection by binwalk

# 1. binwalk install


ubuntu: `apt install binwalk`
other os: https://github.com/ReFirmLabs/binwalk/


# 2. detection

 `git-lfs clone xxx` the repo (https://github.com/Polydet/polyglot-database/tree/master/files) to get file `EXE+HTML+PDF-1.pdf`

```
root@aabbcc:/home/prjs/polyglot/binwalk# binwalk EXE+HTML+PDF-1.pdf

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Microsoft executable, portable (PE)
64            0x40            PDF document, version: "1.3"
386           0x182           PDF document, version: "1.3"
457           0x1C9           HTML document header
740           0x2E4           HTML document footer
827           0x33B           Zlib compressed data, best compression
2496          0x9C0           Zlib compressed data, best compression
15192         0x3B58          Zlib compressed data, best compression
25313         0x62E1          Zlib compressed data, best compression

```

As can be seen, binwalk is able to detect the presence of PE (exe) files, pdf files, and html files. However, there are also some incorrect detections of zlib files as false positives.



# 3. extract files 


```
root@aabbcc:/home/prjs/polyglot/binwalk# binwalk --dd='.*' EXE+HTML+PDF-1.pdf

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Microsoft executable, portable (PE)
64            0x40            PDF document, version: "1.3"
386           0x182           PDF document, version: "1.3"
457           0x1C9           HTML document header
740           0x2E4           HTML document footer
827           0x33B           Zlib compressed data, best compression
2496          0x9C0           Zlib compressed data, best compression
15192         0x3B58          Zlib compressed data, best compression
25313         0x62E1          Zlib compressed data, best compression
```
After the command is successfully run, the output is consistent with the previous section, and a _ Open the folder at the beginning, enter this folder, and you can see the subfiles extracted by binwalk:

```
root@aabbcc:/home/prjs/polyglot/binwalk# cd _EXE+HTML+PDF-1.pdf.extracted/
root@aabbcc:/home/prjs/polyglot/binwalk/_EXE+HTML+PDF-1.pdf.extracted# ll
total 340
drwxr-xr-x 2 root root  4096 Sep 22 18:59 ./
drwxr-xr-x 3 root root  4096 Sep 22 18:59 ../
-rw-r--r-- 1 root root 36371 Sep 22 18:59 0
-rw-r--r-- 1 root root 35985 Sep 22 18:59 182
-rw-r--r-- 1 root root 35914 Sep 22 18:59 1C9
-rw-r--r-- 1 root root 35631 Sep 22 18:59 2E4
-rw-r--r-- 1 root root   337 Sep 22 18:59 33B
-rw-r--r-- 1 root root 35544 Sep 22 18:59 33B.zlib
-rw-r--r-- 1 root root 10291 Sep 22 18:59 3B58
-rw-r--r-- 1 root root 21179 Sep 22 18:59 3B58.zlib
-rw-r--r-- 1 root root 36307 Sep 22 18:59 40
-rw-r--r-- 1 root root 10009 Sep 22 18:59 62E1
-rw-r--r-- 1 root root 11058 Sep 22 18:59 62E1.zlib
-rw-r--r-- 1 root root 12893 Sep 22 18:59 9C0
-rw-r--r-- 1 root root 33875 Sep 22 18:59 9C0.zlib
```

use cmd flie to check file type:


```
root@aabbcc:/home/prjs/polyglot/binwalk/_EXE+HTML+PDF-1.pdf.extracted# file 0
0: MS-DOS executable PE32 executable (console) Intel 80386, for MS Windows
root@aabbcc:/home/prjs/polyglot/binwalk/_EXE+HTML+PDF-1.pdf.extracted# file 182
182: PDF document, version 1.3
root@aabbcc:/home/prjs/polyglot/binwalk/_EXE+HTML+PDF-1.pdf.extracted# file 1C9
1C9: data
root@aabbcc:/home/prjs/polyglot/binwalk/_EXE+HTML+PDF-1.pdf.extracted# vim 1C9

```

Among them, the file named 0 is a PE file, the file named 182 is a pdf file, and the file named 1C9 can be opened with vim to see that it is an html file.


# 4. reference

1. https://github.com/ReFirmLabs/binwalk/
2. https://stackoverflow.com/questions/36530643/use-binwalk-to-extract-all-files
3. https://github.com/Polydet/polyglot-database/tree/master/files
