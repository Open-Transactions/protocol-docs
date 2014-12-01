# protocol-docs

Documentation for the opentxs protocol: see the `content/` directory.

## Documentation Format

All documentation is written in Markdown. To help readability of text and diffs
please hard-wrap text at 72 chars, and code blocks at 79.

Please use links to commit hashes, not to branches (hit the `y` key if you are
looking at a file on Github to get a stable link).

# Offline rendering

The documentation can be rendered offline by means of the
[MkDocs](http://www.mkdocs.org/) document generator, v. 0.11.1 or greater.
Install it (preferably using `pip3` rather than `pip`), run the `mkdocs build`
command in the top level directory of the repo, and a static website will be
generated in the `site/` directory.

You can also run the `mkdocs serve` command and point a browser at
<http://localhost:8000/>.

The output is deployed (manually for now) on GitHub Pages at
<http://monetas.github.io/protocol-docs>.
