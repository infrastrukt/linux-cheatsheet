## pbcopy 

Takes standard input and places it in the clipboard buffer. This command is available for OSX, use `xclip` for Linux.

`pbcopy < ~/.ssh/test.pub` - copy file content to the clipboard. Then you can paste it with `command + v`.

---

`ps aux | pbcopy` - copy command output to the clipboard.

---

## pbpaste

You can use `command + v` to paste in the terminal what you copied with `pbcopy`. But you can also use `pbpaste` instead.

`pbpaste` - paste data from clipboard buffer.

---

`pbpaste > file.txt` - paste into a file.
