# onyx-recipes

Recipe tree for [darkpkg](https://github.com/codecrocodile34/darkpkg), the package
manager used by the onyx Linux distribution.

Each recipe is one TOML file named after its package, so `darkpkg install ncurses`
reads `ncurses.toml`. A recipe points at an upstream release tarball and pins its
sha256, and the target machine downloads, verifies, compiles and installs it.
Nothing is hosted here but the metadata.

```toml
name = "ncurses"
version = "6.4"
source = "https://ftp.gnu.org/gnu/ncurses/ncurses-6.4.tar.gz"
sha256 = "6931283d9ac87c5073f30b6290c4c75f21632bb4fc3603ac8100812bed248159"
build = "autotools"
```

## Fields

| Field | Required | Meaning |
| --- | --- | --- |
| `name` | yes | Package name, and the filename without `.toml` |
| `version` | yes | Upstream version, compared when resolving constraints |
| `source` | yes | URL of the release tarball |
| `sha256` | yes | Digest of that tarball, as `sha256sum` prints it |
| `build` | yes | One of `autotools`, `make`, `cmake` |
| `depends` | no | Packages needed first, such as `["ncurses (>= 6.4)"]` |
| `configure_args` | no | Extra arguments for the configure step, such as `["--with-shared"]` |

## Adding a recipe

Download the tarball, take its digest with `sha256sum`, and write the file. The
digest must come from a tarball you actually downloaded rather than from an
upstream web page, because that is the only way it verifies what will really be
fetched.
