## Vim

### Move cursor

`gg` - go to the first line of the file.
`G`  - go to the last line of the file.

---

`0` - go to the beginning of a current line.
`$` - go to the end of a current line.

---

`w` - jump forwards to the start of a word.
`e` - jump forwards to the end of a word.
`b` - jump backwards to the start of a word.

---

`h` - move cursor left.
`j` - move cursor down.
`k` - move cursor up.
`l` - move cursor right.

### Paste

`:set paste` - turn off auto indent when pasting into vim.

[SO question](https://stackoverflow.com/questions/2514445/turning-off-auto-indent-when-pasting-text-into-vim)

### Delete

`dd` - delete the current line

---

`D` delete to the end of line.

---

`dG` - delete lines below the current one.


### Undo/redo changes

`u` - undo the changes.

---

`Ctrl + r` - redo the changes

### Write and quit

`:w` -  write to a file.

---

`:wq`- write and quit.

---

`:q!` - quit and discard the changes made.

### Search

`/pattern` - search for pattern.

---

`n` - repeat search in the same direction.

---

`N` - repeat search in the opposite direction.

### Replace

`%s/old/new/g` - replace `old` with `new` throughout the file.

---

`%s/old/new/gc` - the same thing but prompts for confirmation.

### Mark text and do a command

`v` - start a visual mode, mark lines, then do a command:

`y` - copy marked text (to paste before the cursor use `P`);
`d` - delete marked text;

### Insert

`i` - insert before the cursor.
`a` - insert after the cursor.

---

`I` - insert at the beginning of the line.
`A` - insert at the enf of the line.

---

`o` - append a new line below the current one.
`O` - append a new line above the current one.