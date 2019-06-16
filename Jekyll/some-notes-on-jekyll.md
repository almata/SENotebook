# Some notes on Jekyll

## Installation

```bash
$ gem install jekyll
```

## Creating a new site

```bash
$ jekyll new my-awesome-site
```

## Running the server

There are two main options to generate the site and start the server so that it can be browsed at http://localhost:4000:

```bash
// option 1
$ cd my-awesome-site
$ jekyll serve

// option 2
$ cd my-awesome-site
$ jekyll serve --detach
```

**Option 1** starts the server with auto-regeneration enabled (`--no-watch` can be specified to disable it). This way, the site will be automatically updated to reflect all changes in `_posts`. To kill the server, `Ctrl + C`.

**Option 2** also starts the server but will detach from current terminal and auto-regeneration will be disabled. To then kill the server, find out its PID number and kill the process:

```bash
$ kill -9 pid-number
```

Modifications on `_config.yml` are never taken into consideration even if auto-regeneration mode is enabled (the server needs to be stopped and started again).

## Posts

Every post has a header with some metadata, including this:

```
date: 2016-02-23 11:40:12 +0100
```

The post won't be generated and published if it contains a date in the future. So this can be a way to have drafts or to schedule future posts.
