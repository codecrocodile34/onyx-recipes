# onyx-recipes

Recipe tree for [darkpkg](https://github.com/codecrocodile34/darkpkg), the package
manager used by the onyx Linux distribution.

Each recipe is one TOML file named after its package, so `darkpkg install ncurses`
reads `ncurses.toml`. A recipe points at upstream source and pins it, and the
target machine downloads, verifies, compiles and installs it. Nothing is hosted
here but the metadata.

```toml
name = "ncurses"
version = "6.4"
description = "Terminal handling library for text user interfaces"
source = { type = "tarball", url = "https://ftp.gnu.org/gnu/ncurses/ncurses-6.4.tar.gz", sha256 = "6931283d9ac87c5073f30b6290c4c75f21632bb4fc3603ac8100812bed248159" }
build = "autotools"
configure_args = ["--with-shared"]
```

## Fields

| Field | Required | Meaning |
| --- | --- | --- |
| `name` | yes | Package name, and the filename without `.toml` |
| `version` | yes | Upstream version, compared when resolving constraints |
| `description` | no | One line saying what the package is, shown by `darkpkg info` |
| `source` | yes | Where the source comes from, see below |
| `build` | yes | One of `autotools`, `make`, `cmake` |
| `depends` | no | Packages needed first, such as `["ncurses (>= 6.4)"]` |
| `configure_args` | no | Extra arguments for the configure step, such as `["--with-shared"]` |
| `patches` | no | Patch files sitting beside the recipe, applied with `patch -p1` |

## Sources

A source is one of two kinds, and each carries the thing that pins it. A tarball
without a digest or a checkout without a revision will not parse.

```toml
source = { type = "tarball", url = "https://example.com/foo-1.0.tar.gz", sha256 = "..." }
source = { type = "git", url = "https://github.com/example/foo.git", rev = "..." }
```

Write `source` as an inline table on one line. A `[source]` section would swallow
every key written after it, which is a TOML rule rather than anything darkpkg does.

**Prefer a release tarball.** Use a git source only for projects that do not
publish one.

**For git sources, pin a full commit SHA rather than a tag.** A commit id is
content addressed, so it gives the same guarantee a sha256 gives a tarball. A tag
is a mutable pointer that upstream can move, which would silently change what gets
built.

## Adding a recipe

Download the tarball, take its digest with `sha256sum`, and write the file. The
digest must come from a tarball you actually downloaded rather than from an
upstream web page, because that is the only way it verifies what will really be
fetched.

Some upstreams republish the same version with different bytes. GitHub's generated
tag archives have changed before. If a digest stops matching, that is why, and the
fix is to check what changed rather than to update the digest reflexively.
