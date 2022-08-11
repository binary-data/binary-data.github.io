---
layout: post
title:  "How to install Ruby gems without sudo"
date:   2022-08-11 12:20:00 +0400
comments: true
tags: ruby rubygems linux
---

You don't want to clutter your system with extra ruby gems, don't you?

### Solution

The option is to install gems to a folder in your home. The `gem install` command has an option just for that:
- `--user-install` installs gems in the user’s home directory instead of `GEM_HOME`.

So the command may look like this:
```shell
gem install --user-install jekyll
```

### The .gemrc file

To avoid typing the options every time, we could add them to the `~/.gemrc` file:
```yaml
---
gem: --user-install
install: --no-document # avoids generating documentation, you may omit this option
```
And then just type
```shell
gem install jekyll
```

### PATH
If you see a warning

```
WARNING:  You don't have /home/anton/.local/share/gem/ruby/bin in your PATH,
          gem executables will not run.
```

make sure to add the provided bin directory to the `$PATH`. Usually, this is done in shell configuration files like `~/.bashrc` or `~/.zhsrc`:

```shell
export PATH=$HOME/.gems/bin:$PATH
```

Reload the terminal or use

```shell
source ~/.bashrc
```

after edit so that new paths will be available.

### Debugging
To see the current paths, use the `gem environment` command. By default, it loads your `~/.gemrc`. It will give you the following output:

```shell
RubyGems Environment:
  - RUBYGEMS VERSION: 3.3.7
  - RUBY VERSION: 3.1.2 (2022-04-12 patchlevel 20) [x86_64-linux]
  - INSTALLATION DIRECTORY: /usr/share/gems
  - USER INSTALLATION DIRECTORY: /home/anton/.local/share/gem/ruby
  - RUBY EXECUTABLE: /usr/bin/ruby
  - GIT EXECUTABLE: /usr/bin/git
  - EXECUTABLE DIRECTORY: /usr/bin
  - SPEC CACHE DIRECTORY: /home/anton/.local/share/gem/specs
  - SYSTEM CONFIGURATION DIRECTORY: /etc
  - RUBYGEMS PLATFORMS:
     - ruby
     - x86_64-linux
  - GEM PATHS:
     - /usr/share/gems
     - /home/anton/.local/share/gem/ruby
     - /usr/local/share/gems
  - GEM CONFIGURATION:
     - :update_sources => true
     - :verbose => true
     - :backtrace => false
     - :bulk_threshold => 1000
     - "gem" => "--user-install"
     - "install" => "--no-document"
  - REMOTE SOURCES:
     - https://rubygems.org/
  - SHELL PATH:
     - /home/anton/programs
     - /home/anton/.gems/bin
     - /usr/local/bin
     - /usr/bin
     - /bin
     - /usr/local/sbin
     - /usr/sbin
     - /sbin
```

#### References
- <https://guides.rubygems.org/command-reference/#gem-install>
- <https://guides.rubygems.org/command-reference/#gem-environment>
