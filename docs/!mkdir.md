## mkdir

### Create a directory



`mkdir -p /home/dir1/dir2` - create a directory `dir2` and also create any parent directories, i.e. `/home` and `/home/dir1`, if they don't exist.

---

`mkdir -m 777 dirname` - create a directory and specify its permissions

### Create multiple directories

`mkdir /home/dir1 dir2` - create `dir1` directory in `/home` directory and create `dir2` in the current directory.

---

`mkdir /home/{dir1,dir2}` - create multiple directories under the same path. Directories listed in curly brackets`{}`, i.e. `dir1` and `dir2`, will be created in `/home` directory.
