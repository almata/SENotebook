# Understand and edit $PATH content

In order to find out where the system will look for binaries we need to check the content of the `$PATH` environment variable. We can specify a set of directories where executable programs are located using `$PATH`. The `$PATH` variable is specified as a list of directory names separated by colon `:` characters:

```bash
$ echo "$PATH"
/Users/albertmata/anaconda/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/share/dotnet:/Library/TeX/texbin
```

Some of these entries are common stuff that comes by default, while some others come from software I have installed (and allowed to modify my `$PATH` variable). For instance, that first entry `/Users/albertmata/anaconda/bin` comes from file `~/.bash_profile`, which in my case contains this:

```bash
$ cat ~/.bash_profile
[[ -s "$HOME/.profile" ]] && source "$HOME/.profile" # Load the default .profile
# added by Anaconda2 4.4.0 installer
export PATH="/Users/albertmata/anaconda/bin:$PATH"
```

And the last three entries `/opt/X11/bin:/usr/local/share/dotnet:/Library/TeX/texbin` do exist because my `/etc/paths.d` directory contains three text files with those three paths:

```bash
$ pwd
/etc/paths.d
$ ls
40-XQuartz	TeX		dotnet
$ cat 40-XQuartz 
/opt/X11/bin
```

So, in short, if we want to add a new path to `$PATH` so the system looks for binaries there we can either:

* Edit the `~/.bash_profile` to add a new `export` line (if we need to generate the `$PATH` variable for a single user account), or...
* Add a new file to `/etc/paths.d` directory (if we want to generate the `$PATH` variable for all user accounts on the system).
