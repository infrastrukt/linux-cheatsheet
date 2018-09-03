# less

**Less** is a program which is used to view the content of a file.

It is superior to alternative commands such as `cat` and `more` because it allows forward and backward navigation through the file and unlike `cat` it also allows searching throught the file.

It is superior to text editors like `vim`, because it doesn't have to read the entire file before starting which makes it start up faster than a text editor.

Syntax:

```bash
$ less filename # view a file content
$ ps -ef | less # view and navigate through a command output
```

## Navigation

_The navigation keys in less command are similar to `vim` editor._

#### Scroll forward

---

By default, the less command will scroll a single page at a time.

To scroll forward one page, press either the `space` or the `f` key.

---

You can change the number of lines that are scrolled when you press the `space` or `f` key by pressing the number immediately before pressing the key.

For example, enter `10` followed by either the `space` or `f` key, and this will scroll forward by 10 lines.

To make this the default you can enter the number followed by the `z` key.

For example, enter `10` and then press `z`. Now when you press the `space` or `f` key the screen will always scroll by 10 lines.

---

`d` - scroll half the screen

---

`return` or `j` - scroll forward one line at a time. Type a number before pressing a key to scroll a specific number of lines.

#### Scroll backward

`b` scroll back one page. Entering a number before pressing the `b` key scrolls back up the specified number of lines.

To make the `b` key permanently scroll by the specified number of lines enter the number you wish to use followed by the `w` key.

---

`u` scroll back half the screen.

---

`k` to scroll back one line at a time. Type a number before pressing a key to scroll a specific number of lines.

#### Scroll horizontally

Use right and left arrows.

#### Move to a specific line

`g` - go to the beginning of file.

`G` - go to the end of file.

If you enter a number before pressing the `g` or `G` keys, it will go to a specifi line.

#### Mark a position in a file

To mark a line in a file press `m` key and then enter any lowercase letter to set a marker. To return to the marked place, press `'` and enter the marker (the lowercase that you used to set the marker).

#### Get information about the file

`=` - get information about the file. This will show at the bottom of the screen the name of the file, the total number of lines, the line numbers you're currently viewing.

#### Edit the file

`v` - edit the file you're viewing. This will open the file in your default text editor. After editing, you'll return to `less`.

#### Searching

Forward search: 

* `/pattern` - search for a pattern which will take you to the next occurrence.
* `n` – for next match in forward
* `N` – for previous match in backward

Backward search:

* `?pattern` – search for a pattern which will take you to the previous occurrence.
* `n` – for next match in backward direction
* `N` – for previous match in forward direction

---

`&pattern` – display only the lines that match the pattern


#### Help

`h` get a summary of less commands.

#### Exit

Press either `q` or `Q` key.

## Useful options

`less -i` causes searches to ignore case; that is, uppercase and lowercase are  considered identical. This option is ignored if any upper-case letters appear in the search pattern; in other words, if a pattern contains uppercase letters, then that search does not ignore case.

`less -M` - displays the line numbers of the output. For example `lines 1-23`.

`less -N` - displays line numbers on each line.

`less -p pattern` - This starts less at the first occurrence of the pattern specified.


## Rersources used to create this document

* https://www.lifewire.com/what-to-know-less-command-4051972
* https://www.thegeekstuff.com/2010/02/unix-less-command-10-tips-for-effective-navigation
* man less
