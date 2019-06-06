# npm Script Tricks

## Get directory where command was run

Get the current ("initial") directory that an npm script was run:

```json
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

## Shell Positional Parameters in npm Scripts

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
