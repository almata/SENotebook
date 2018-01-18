# Install MIT Scheme on Mac

In theory I just needed to use Homebrew to install MIT Scheme on macOS Sierra:

```bash
$ brew install mit-scheme
```

And at the end it has worked, although in the process a newer version of Ruby has been required, so I've needed to install RVM:

```bash
$ curl -sSL https://get.rvm.io | bash -s stable --ruby
```

But that just didn't work because there were some problems with my Homebrew. So I've needed to update some things in my system first:

```bash
$ brew doctor
```

This has shown some recommendations, one of which was to install Xcode Command Line Tools:

```bash
$ xcode-select --install
```

And also to install many other packages:

```
$ brew install <...> <...> <...> <...>
```

But, again, after all this process I've finally been able to install MIT Scheme using Homebrew:

```bash
$ brew install mit-scheme
```

So finally I can play with Scheme's REPL:

```bash
$ scheme
MIT/GNU Scheme running under OS X
...
1 ]=> (define a 10)
;Value: a
1 ]=> a
;Value: 10
1 ]=> 
```

And I can also run my Scheme programs:

```bash
$ scheme --quiet < myprogram.scm
$ scheme < myprogram.scm 
```
