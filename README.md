# Graphviz sandbox - pygraphviz wheelhouse

Mirror of the source code for the graphviz 5.0.1 release from:
https://gitlab.com/graphviz/graphviz/-/releases/5.0.1

This repo is intended for experimenting with wrapping graphviz to create
pygraphviz wheels, shortcutting the graphviz release process and
version-controlling wrapping attempts.

DO NOT USE THIS REPO TO BUILD/INSTALL GRAPHVIZ! Please refer to the
official [graphviz repo](https://gitlab.com/graphviz/graphviz).

This repo was initialized with the following procedure:

```bash
curl -o graphviz-5.0.1.tar.xz https://gitlab.com/api/v4/projects/4207231/packages/generic/graphviz-releases/5.0.1/graphviz-5.0.1.tar.xz
xz --uncompress graphviz-5.0.1.tar.xz
tar -xf graphviz-5.0.1.tar
cd graphviz-5.0.1
git add .
git commit
```
