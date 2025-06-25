# catgit - run cat on files under git control

Although one can certainly do `cat glob...` in a directory what if you only want
files that are under git control? You can use `git grep . 'glob'` but this won't
work if you want `cat` options or you want to use a different `cat` like the
[bat
command](https://www.cyberciti.biz/open-source/bat-linux-command-a-cat-clone-with-written-in-rust/)
(though it should be noted this was not tested).

## Warning: this script does **NOT** try and detect binary files!

Although it might be possible to do this it would unnecessarily complicate the
script in ways I do not deem worth it as this is no different from `cat(1)`
itself. If this is not clear you should be careful about the globs you specify
just like with `cat(1)`.

## Disclaimer

This is something of a hack that was based on another script of mine,
[sgit](https://github.com/xexyl/sgit), and so was not written from scratch. Thus
it is possible there are bugs that are not known. I was sick at the time, 21
September 2023, so I had to limit what I did, but a look at it later with a new
option and it seems good.

## Usage

```sh
usage: catgit [-h] [-V] [-v level] [-x] [-c cat] [-o options] [-n] <glob...>

    -h			    print help and exit
    -V			    print version and exit
    -v level		    set verbosity level
    -x			    turn on tracing (set -x)
    -o options		    options to cat
    -c cat		    set path to cat

    -n			    dry-run: only show files that would be modified but do not touch them

				NOTE: depending on verbosity level, only the files considered will
				be printed or the cat command (including options if any) and the files
				will be printed

catgit version: 1.0.1 25-06-2025
```

You **MUST** specify at least one glob but you can specify more than one. If
it does not exist no errors will be shown.

You can specify options to `cat` with the `-o` option to the script. It is
advisable to quote the options specified with the `-o` option and you **must**
pass the `-` (or if long options `--`) to the script. This is one of the reasons
it is advisable to quote the options. It is recommended to use `-o` for each
option but you might be able to get away with not doing this in some cases.


## Examples

### cat specific C files under git control in the `src` directory

```sh
catgit 'src/*.c'
```

### cat catgit itself with the `-n` option (number output lines)

```sh
catgit -o '-n' catgit
```

### With tracing enabled, cat `*.sh` files under git control

```sh
catgit -x '*.sh'

```

### Verbosely (level 1) use the script to cat all files in the repo

Obviously this could be problematic if there are a lot of files or if there are
binary files.

```sh
catgit -v1 '*'

```

You could also specify for the glob `'.'`.

### Dry-run mode: _ONLY SHOW_ files that would be catted rather than catting them

If you want to _ONLY SEE FILES_ that would be considered _WITHOUT_ catting them
you can use the `-n` option like so:

```sh
catgit -n .
```

This would print every file in the repository from the current working
directory. For instance it might show something like:

```sh
.gitignore
Makefile
README.md
catgit
catgit.1
```

### Dry-run mode: _ONLY SHOW_ cat command (with options) along with the files found rather than actually running cat on them

If you want to see the cat command along with the files found _WITHOUT_ catting
them them you can use the `-n` option with a verbosity level greater than or equal to
1 like so:

```sh
catgit -v 1 -n .
```

This would show what would be run if `-n` was not specified. For instance the
above command might show:

```sh
$ catgit -v 1 -n .
/opt/local/libexec/gnubin/cat .gitignore
/opt/local/libexec/gnubin/cat Makefile
/opt/local/libexec/gnubin/cat README.md
/opt/local/libexec/gnubin/cat catgit
/opt/local/libexec/gnubin/cat catgit.1
```

If verbosity was 2 and 3 you would see something like the following,
respectively:

```sh
$ catgit -v 2 -n .
debug[2]: looping through all globs
debug[2]: found glob: 0
/opt/local/libexec/gnubin/cat .gitignore
/opt/local/libexec/gnubin/cat Makefile
/opt/local/libexec/gnubin/cat README.md
/opt/local/libexec/gnubin/cat catgit
/opt/local/libexec/gnubin/cat catgit.1
```

and

```sh
$ catgit -v 3 -n .
debug[2]: looping through all globs
debug[2]: found glob: 0
/opt/local/libexec/gnubin/cat .gitignore
/opt/local/libexec/gnubin/cat Makefile
/opt/local/libexec/gnubin/cat README.md
/opt/local/libexec/gnubin/cat catgit
/opt/local/libexec/gnubin/cat catgit.1
debug[2]: 0 remaining globs
```


## Other details and examples

At verbose level 3 you can see more of what the script is doing but this is less
useful because it is interspersed with the output of the files. However if you
wish to look at how it works you can redirect stdin to `/dev/null` like:


```sh
catgit -v 3 '*README.md' '*.c' 1>/dev/null
```

With that you might see something like:

```sh
debug[2]: cat option(s):
debug[2]: looping through all globs
debug[2]: found glob: 0
debug[1]: about to run: git ls-files *README.md | xargs /opt/local/libexec/gnubin/cat
debug[2]: 1 remaining glob
debug[2]: found glob: 1
debug[1]: about to run: git ls-files *.c | xargs /opt/local/libexec/gnubin/cat
debug[2]: 0 remaining globs
```

Level 2 would not show how many globs remain after each operation.


## Installation

If you wish to install it you may either copy it to a place in your path or if
you have make installed you can just run `make install` either as root or via
`sudo` like:

```sh
sudo make install
```

## Documentation?

Right now this is it but the Makefile is set up so that if a man page is added
it can easily install it as well.

## Limitations

One that comes to mind is you cannot specify `git ls-files` options but this
could be problematic anyway especially if one were to specify the `-z` option.

There are probably other limitations but it works well for what I needed.

## Bugs

Cats are known to push things off tables, computers and would even push things
off the planet if Earth was flat :-)

No other bugs are known at this time but obviously if you specify invalid
options to `cat` there will be a problem or maybe problems.


## History

In the early hours of 21 September 2023 I had the thought that this might be a
nice thing to have in some cases along the lines of my admittedly much more
useful [sgit tool](https://github.com/xexyl/sgit) which is the same idea but for
`sed(1)`.

I had forgotten about it when I woke up proper but by a happy chance it popped
into my head later on in the day so I quickly put it together. I was sick at the
time so I had to not do much but I think it is in good enough shape.

Later I added the dry-run option `-n` that I had added to
[sgit](https://github.com/xexyl/sgit) that works in the same way: depending on
verbosity level it will show either the files that would be catted or the actual
command (with any options) and the file.

## IAQ / NAQ (infrequently asked question(s) / never asked question(s) :-) )

Q: Why did you call it `catgit` and not `gitcat`?

A: Because as I noted above cats can be right little gits. :-) That's the only
reason. It's a joke for ailurophiles (cat lovers) like myself. Of course one
might argue that 'git cat' could be read as a git of a cat but it also reads
better as `catgit` but still retains the pun: 'cat files under git control' or
so.

Q: Did I see an acrostic in the options list?

A: You might have indeed :-) The options spell out CON and when one thinks of
cats they might very well think of cats conning you. For instance one of my cats
likes to try and trick us to give him and the dogs another treat even though
they already had their two treats for the day. He knows too that if he's cute or
annoying enough he will get his way and what's more is that one of the dogs
works together with him, sometimes instigating it and sometimes being brought in
by the cat, and it works well.

## Other thoughts

Pull requests are welcome but I think it's mostly in a good enough state where
this will probably generally not be needed. Of course this was written very
quickly and was actually done by modification of a copy of
[sgit](https://github.com/xexyl/sgit) so it's possible that there is indeed a
bug that needs fixing but it seems good with some quick tests both with options
and otherwise.
