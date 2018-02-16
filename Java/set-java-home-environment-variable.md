# Set $JAVA_HOME environment variable

Apple recommends to set the `$JAVA_HOME` variable to `/usr/libexec/java_home`, so to set `$JAVA_HOME` environment variable we basically need to add `export JAVA_HOME=$(/usr/libexec/java_home)` as a new line into `~/.bash_profile` and then apply changes with `source ~/.bash_profile`. After that the variable will be set:

```bash
$ echo $JAVA_HOME
/Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home
```

We use `/usr/libexec/java_home` as it returns the Java version we have specified in our Java Preferences. We can use same command with an additional `-V` to see all JVMs in our machine and the one that will be chosen by default:

```bash
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (3):
    1.8.0_161, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home
    1.8.0_102, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_102.jdk/Contents/Home
    1.7.0_72, x86_64:	"Java SE 7"	/Library/Java/JavaVirtualMachines/jdk1.7.0_72.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home
```

Finally, if we are interested in some specific version, we can pick it manually:

```bash
$ /usr/libexec/java_home -v 1.7
/Library/Java/JavaVirtualMachines/jdk1.7.0_72.jdk/Contents/Home
```
