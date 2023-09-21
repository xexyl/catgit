# catgit - run cat on files under git control

Although one can certainly do `cat glob...` in a directory what if you only want
files that are under git control? You can use `git grep . 'glob'` but this won't
work if you want `cat` options or you want to use a different `cat` like the
[bat
command](https://www.cyberciti.biz/open-source/bat-linux-command-a-cat-clone-with-written-in-rust/)
(though it should be noted this was not tested).



## Usage

```sh
usage: catgit [-h] [-V] [-v level] [-x] [-c cat] [-o options] <glob...>

    -h			    print help and exit
    -V			    print version and exit
    -v level		    set verbosity level
    -x			    turn on tracing (set -x)
    -o options		    options to cat
    -c cat		    set path to cat

catgit version: 0.0.0-1 21-09-2023
```

You **MUST** specify at least one glob but you can specify more than one. If
it does not exist no errors will be shown.

You can specify options to `cat` with the `-o` option to the script. It is
advisable to quote the options specified with the `-o` option and you **must**
pass the `-` (or if long options `--`) to the script. This is one of the reasons
it is advisable to quote the options. You shouldn't have to use `-o` more than
once but you certainly can.


# Examples

## cat specific C files under git control in the `src` directory

```sh
catgit 'src/*.c'
```

## cat catgit itself with the `-n` option (number output lines)

```sh
catgit -o '-n' catgit
```

## With tracing enabled, cat `*.sh` files under git control

```sh
catgit -x '*.sh'

```

## Verbosely (level 1) use the script to cat all files in the repo

Obviously this could be problematic if there are a lot of files or if there are
binary files.

```sh
catgit -v1 '*'

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
options to `cat` there will be a problem or problems.


## History

In the early hours of 21 September 2023 I had the thought that this might be a
nice thing to have in some cases along the lines of my [sgit
tool](https://github.com/xexyl/sgit). I had forgotten this but by a happy chance
it popped into my head later on in the day.

## IAQ / NAQ (infrequently asked question(s) / never asked question(s) :-) )

Q: Why did you call it `catgit` and not `gitcat`?
A: Because as I noted above cats can be right little gits. :-) That's the only
reason. It's a joke for ailurophiles (cat lovers) like myself.


## Other thoughts

Pull requests are welcome but I think it's mostly in a good enough state where
this will probably generally not be needed. Of course this was written very
quickly and was actually done by modification of a copy of
[sgit](https://github.com/xexyl/sgit) so it's possible that there is indeed a
bug that needs fixing but it seems good with some quick tests both with options
and otherwise.

