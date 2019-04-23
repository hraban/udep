# udep: minimal dependency build framework

The smallest possible build framework which does dependency resolution and dirty
vs clean build management. All actual logic is offset to the (user defined)
build scripts, which must indicate whether a resource is up-to-date, and what
its dependencies are.

Inspire by Make and Luigi. Like Luigi and Make, udep requires the user to
manually specify the dependencies of each task. Like Luigi, but unlike Make,
udep does no smart detection of whether a resource is stale or up-to-date,
whatsoever.

The best explanation is probably to have a look at the examples directory.

## Usage

In Make, there is a central location (Makefile) which contains declarations for
every rule, its dependencies, and its recipe. In udep, there is no such central
location: every rule is a separate script / program / binary in the PWD, which
accepts an argument indicating how it's being called:

* `my-rule run`: do the thing to satisfy `my-rule`, e.g. create a file, compile
  something, launch a VPS, etc.
* `my-rule is-done`: is this already done? print "yes" on stdout. Or is it
  stale, and does it need running? Print "no".
* `my-rule dependencies`: for each dependent rule, print a line with its name to
  stdout. May include spaces, which will end up as arguments.

For example, a rule `website`:

```sh
#!/bin/bash

set -eu -o pipefail

function if-modified-200 {
    curl -H "If-Modified-Since: $(date -uRj site/)" \
      -sS -w "%{http_code}" https://myhost.example.com
}

case "$1" in
    dependencies)
        echo build-site
        ;;
    run)
        scp -r site/ sftp.myhost.example.com:/var/www
        ;;
    is-done)
        [[ $(if-modified-200) == 304 ]] && echo yes || echo no
        ;;
    *)
        >&2 echo "$0: Unexpected argument: $1."
        >&2 echo "Call me as: udep $0"
        exit 1
        ;;
esac
```

This would need another rule in the same directory called `build-site`, which
builds to `site/`.

Have a look in the examples/ directory for a straight-forward (but contrived)
example using files only.

## Dependencies

- bash
- xargs

That's it. Should be compatible with GNU and BSD xargs.

## Installation

Just dump it in your PATH somewhere. Or symlink to it.

## License

Copyright © 2019 Hraban Luyat <hraban@0brg.net>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.
