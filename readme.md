# npm Tricks

A collection of useful npm scripts tricks

## npm Scripts: Get Directory where Command was Run

Get the current ("initial") directory that an npm script was run:

```js
// <project root>/package.json
{
  "scripts": {
    "deck": "mdx-deck $INIT_CWD/index.mdx"
  }
}
```

Usage:

```sh
packages/presentation-1 $ npm run deck   # runs mdx-deck /Users/user/projects/decks/packages/presentation-1/index.mdx
```

### References

Background / Discussion: https://github.com/npm/npm/issues/9374#issuecomment-339004386

## npm Scripts: Regular expressions with sed

If you want to use regular expressions with features like backreferences, you may consider using `sed` within your npm script. 

The escaping in the regular expression is a bit weird (probably because of JSON).

This example uses the `s@pattern@replace@` syntax to avoid having to escape the folder slashes.

```js
{
  "scripts": {
    "names": "for file in dir/*.md; do echo `echo $file | sed 's@dir/\\(.*\\)\\.md@\\1.pdf@'`; done"
  }
}
```

Usage:

```sh
# Files within dir:
# dir/abc.md
# dir/def.md

npm run names
abc.pdf
def.pdf
```

## npm Scripts: Restart a Process if it Exits

If a process exits for any reason, restart it, with an optional message.

```js
{
  "scripts": {
    "develop": "until gatsby develop; do echo "Gatsby crashed with exit code $?. Restarting.." >&2; sleep 1; done"
  }
}
```

Usage:

```sh
$ npm run develop
```

### References

Stack Overflow: https://stackoverflow.com/a/697064/1268612

## npm Scripts: Run a Command for All Files in a Directory

To loop / foreach over all files matching a pattern.

```js
{
  "scripts: {
    "build": "for file in dir/*.md; do md-to-pdf $file; done"
  }
}
```

Usage:

```sh
npm run build   # will loop over all the .md files in the directory "dir" and run md-to-pdf with each
```

## npm Scripts: Shell Positional Parameters

Ever want to use Bash positional parameters / arguments / variables in npm scripts?

Here's two ways:

```json
{
  "scripts": {
    "1": "f(){ mdx-deck $1 index.mdx; }; f",
    "2": "bash -c 'mdx-deck $1 index.mdx' --"
  }
}
```

Usage:

```sh
npm run 1         # runs mdx-deck index.mdx
npm run 1 build   # runs mdx-deck build index.mdx
```

### More complex examples

#### Using all positional parameters via the `$*` shell variable

```json
{
  "scripts": {
    "1": "f(){ mdx-deck $* index.mdx; }; f",
    "2": "bash -c 'mdx-deck $* index.mdx' --"
  }
}
```

Usage:

```sh
npm run 1 build blah another   # runs mdx-deck build blah another index.mdx
```

#### All positional parameters via `$*` with default values

Default values are achieved by testing values of `$1` and whether something is included in `$*`:

```sh
{
  "scripts": {
    "1": "f(){ mdx-deck $* $([[ $1 = build && ! $* =~ '-d' ]] && echo \"-d default\" || echo \"\") index.mdx; }; f",
    "2": "bash -c 'mdx-deck $* $* $([[ $1 = build && ! $* =~ '-d' ]] && echo \"-d default\" || echo \"\") index.mdx' --"
  }
}
```

Usage:

```sh
npm run 1 build                 # runs mdx-deck build -d default index.mdx
npm run 1 build -d different    # runs mdx-deck build -d different index.mdx
```

### References

Tweet 1: https://mobile.twitter.com/karlhorky/status/1136577374072573952<br>
Tweet 2: https://mobile.twitter.com/karlhorky/status/1136584417533730816<br>
Background / Discussion: https://github.com/npm/npm/issues/9627#issuecomment-338752485
