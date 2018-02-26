## mktemp

Make a temporary file or directory. Ensures the uniquess and randomness of the name of created file or directory.

[SO: Why do I need mktemp?](https://stackoverflow.com/questions/11636790/why-do-we-need-mktemp)

mktemp takes a **file/directory name template** as an argument. The template could be a any file name with some number of `X` symbols appended to it. Those `Xs` will be replaced with a unique comination of letters and number during creation of a file/directory.

Create a temporary file with the name generated from a given template:

```bash
$ mktemp -t test.XXXXXX
/tmp/test.W9xceN

```

---

`mktemp -d -t test.XXXXXX` - create a directory with a unique name generated from a given template.

---

Without a template argument, `mktemp` will create a file/directory with unique name using a default template.

```bash
$ mktemp
/tmp/tmp.I5ETfxUPuU
```

---

`mktemp -u -t test.XXXXXX` - generate a unique name form a given template, but don't create an actual file.

## Create temporary files in specific location

`mktemp` will create temporary files/directories under a directory which it chooses following the rules:

* If the user’s `$TMPDIR` environment variable is set, the directory contained therein is used. Therefore, if you want all your temporary files/directories to be created under a specific directory, you may want to set this variable.
* Otherwise, if the `-p` flag was given the specified directory is used. Note: this will not work if `$TMPDIR` is set:
    ```bash
    $ mktemp -t test.XXXXXX -p /home/ubuntu
    /home/ubuntu/test.ZD2zJm
    ```
* If none of the above apply, `/tmp` is used.

To find out the directory where `mktemp` will create the files by default, you can use the following command:

```bash
$ dirname `mktemp -u`
/tmp
```