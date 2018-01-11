# Manage different Ruby instances with RVM

First of all, to know which ruby instances are installed at this moment:

```bash
$ which -a ruby
/Users/albertmata/.rvm/rubies/ruby-1.9.3-p551/bin/ruby
/usr/bin/ruby
```

And then to check which one is currently processing commands:

```bash
$ ruby -v
ruby 1.9.3p551 (2014-11-13 revision 48407) [x86_64-darwin14.5.0]
```

So it seems the instance managed by RVM is the one in use. To switch back to system ruby:

```bash
$ rvm use system
Now using system ruby.
$ ruby -v
ruby 2.0.0p648 (2015-12-16 revision 53162) [universal.x86_64-darwin15] 
```

And to use the RVM one again:

```bash
$ rvm default
$ ruby -v
ruby 1.9.3p551 (2014-11-13 revision 48407) [x86_64-darwin14.5.0]
```

To show all rubies managed by RVM and the one used by default:

```bash
$ rvm list
rvm rubies
=* ruby-1.9.3-p551 [ x86_64 ]
   ruby-2.1.1 [ x86_64 ]
# => - current
# =* - current && default
#  * - default
```

And to change the default ruby in RVM:

```bash
$ rvm --default use 2.1.1
Using /Users/albertmata/.rvm/gems/ruby-2.1.1
$ ruby -v
ruby 2.1.1p76 (2014-02-24 revision 45161) [x86_64-darwin12.0]
```

To upgrade to the most stable RVM version:

```bash
$ rvm get stable
...
RVM reloaded!
```

To show available rubies ready to be installed via RVM:

```bash
$ rvm list known
# MRI Rubies
...
[ruby-]2.0.0[-p643]
...
```

And to install one of them (it'll be set as the current in use, but not as the default):

```bash
$ rvm install 2.2.2
...
$ rvm list
rvm rubies
   ruby-1.9.3-p551 [ x86_64 ]
 * ruby-2.1.1 [ x86_64 ]
=> ruby-2.2.2 [ x86_64 ]
# => - current
# =* - current && default
#  * - default
```