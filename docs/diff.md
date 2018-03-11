## diff

Find the differences between files or folders. 

Note: check **colordiff** utility for colorized output.

## Compare files

Syntax:

```
diff [options] file1 file2
```

This command is used to display the differences in the files by comparing the files line by line. It tells you which lines in the `file1` have to be changed to make it look identical to the `file2`.

_If files don't differ, there will be no output._

`diff` uses special symbols to indicate a type of change that needs to happen in the _first file_:

```
a - add 
c - change
d - delete
```

It also shows you the lines that differ in two files:

* Lines preceded by a **<** are lines from the first file
* Lines preceded by **>** are lines from the second file.
* Lines from the first files are separated from lines in the second file with three dashes `---`.

### Example

In file1.txt I have:

```bash
$ cat file1.txt
December 31
January 31
February 28
March 31
April 30
```

In file2.txt I have:

```bash
$ cat file2.txt
January 31
February 29
March 31
April 30
May 31
June 30
```

If I try to compare `file1.txt` to `file2.txt` by running `diff`, I will get:

```bash
$ diff file1.txt file2.txt
1d0
< December 31
3c2
< February 28
---
> February 29
5a5,6
> May 31
> June 30
```

`1d0` means that I need to delete (`d`) the first line (`1`) in `file1.txt` to match the beginning (line `0`) of the `file2.txt`.

`2c2`  means that the second line (`2`) in the `file1.txt` file needs to be changed (`c`) to match the second line (`2`) in the `file2.txt`. It then shows how those lines look in each file:

* The second line of the first file contains `February 28` as indicated by `<` symbol
* The second line of the second file contains `February 29` as indicated by `>` symbol
*  Lines in two files are separated by `---`

The next change that needs to happen is indicated by `4a5,6`. This means that after line `4` in the first file we need to add lines to match the lines from `5` to `6` (indicated by range `5,6`) in the second file. It then shows what those lines in the second file are (indicated by `>` symbol) so we know what to add:

```
May 31
June 30
```

To sum up, I need in order to make the first file look like the second one, I need to delete the first line (`December 31`), change the second line `February 28` in file1.txt to `February 29` and add lines `May 31` and `June 30` to the end of the file.

#### Useful options

**-y** side by side comparison. Shows files contents in 2 collumns:

```bash
$ diff -y file1.txt file2.txt
December 31						      <
January 31							January 31
February 28						      |	February 29
March 31							March 31
April 30							April 30
							      >	May 31
							      >	June 30
```

**-q** (**--brief**) simply shows if files are different or not without showing the lines that differ:

```bash
$ diff -q file1.txt file2.txt
Files file1.txt and file2.txt differ
$ echo $? # check the status code
1
$ diff -q file1.txt file1.txt # there will be no output
$ echo $? # check the status code
0
```

**-w** ignore whitespaces and focus on the text only. The **-s** makes it print the message if files are identical:

```bash
$ cat a.txt
January      31
$ cat b.txt
January 31
$ diff a.txt b.txt
1c1
< January      31
---
> January 31
$ diff -sw a.txt b.txt
Files a.txt and b.txt are identical
```

**-i** ignore case differences in file contents.

```bash
$ cat a.txt
january
$ cat b.txt
JANUARY
$ diff -sq a.txt b.txt
Files a.txt and b.txt differ
$ diff -siq a.txt b.txt
```

**-B** ingore blank lines when comparing files:

```bash
$cat a.txt
january


$ cat b.txt
january
$ diff -s a.txt b.txt
2,3d1
<
<
$ diff -sB a.txt b.txt
Files a.txt and b.txt are identical
```

## Compare directories 

The diff command can also be used to quickly compare the contents of two directories:

```
$ ls dir*
dir1:
file1 file2 file3

dir2:
file1 file3 file4
$ diff -rq dir1 dir2
Files dir1/file1 and dir2/file1 differ
Only in dir1: file2
Only in dir2: file4
```

### Useful options

**-r** recursively compare any subdirectories found.

**-N** treat absent files as empty.


## Context and Unified modes

There are 2 different modes available to view the output form diff command.

### Context mode

Pass the **-c** option to view diff output in context mode.

```
$ diff -c file1.txt file2.txt
*** file1.txt	2018-03-11 16:03:26.000000000 -0700
--- file2.txt	2018-03-11 16:03:42.000000000 -0700
***************
*** 1,5 ****
- December 31
  January 31
! February 28
  March 31
  April 30
--- 1,6 ----
  January 31
! February 29
  March 31
  April 30
+ May 31
+ June 30
```

The first file is indicated by `***`, and the second file is indicated by `—--`.

The line with `***************` is simply a separator.

The first two lines of this output show us information about the files we're comparing. It lists the file name, modification date, and modification time of each file.

Next it shows starts the section which shows the contents of the first file. The section starts with a header which consists of three asterisks `***` (which indicate the first file) followed by a line range information of the first file (`1,5`) and four more asterisks `****`. After that it shows the contents of the first file with and marks each line that differes from the second file with a special indicator:

* `+` indicates that a line in the second file that needs to be added to the first file to make them identical.
* `-` indicates a line in the first file that needs to be deleted to make them identical.
* `!` indicates a line is part of a group of one or more lines that needs to be changed. There is a corresponding group of lines prefixed with an `!` in the other file's context too.

After that there are three dashes `—--` (which indicate the first file) followed by a line range information of the second file (`1,6`) and four more dashes `----`. Then it shows the contents of the second file.

### Unified mode

To view differences in unified mode, use the **-u** option. It is similar to context mode but it doesn’t display any redundant information and the output looks more compact.

```bash
$ diff -u file1.txt file2.txt
--- file1.txt	2018-03-11 16:03:26.000000000 -0700
+++ file2.txt	2018-03-11 16:03:42.000000000 -0700
@@ -1,5 +1,6 @@
-December 31
 January 31
-February 28
+February 29
 March 31
 April 30
+May 31
+June 30
```

The output is similar to the context mode example. The first two lines show info about the files. The next line shows info about the line ranges of the files: the first file is indicated by `-` (`-1,5`) and the second is indicated by `+` sign (`+1,6`). The file differences are then shown in the _unified form_.  It shows which lines in the first file need to be removed (`-`) and added (`+`) to make it look identical to the second file.



## Resources used to create this document
* https://www.geeksforgeeks.org/diff-command-linux-examples/ 
* http://landoflinux.com/linux_diff_command.html
* man diff









