# Pygraphviz wheels

1. start docker service: `sudo systemctl start docker`
2. See if you have the manylinux container you want: `sudo docker images`
2a. If not, download: `sudo docker pull quay.io/pypa/manylinux2014_x86_64`
3. Start the container interactively: `sudo docker run -i -t quay.io/pypa/manylinux2014_x86_64`

Then...

```bash=
yum list installed | grep graphviz  # Verify graphviz isn't already installed
mkdir graphviz_src && cd graphviz_src  # Directory for downloaded source
# Download latest graphviz source, e.g.
curl -o graphviz-5.0.1.tar.xz https://gitlab.com/api/v4/projects/4207231/packages/generic/graphviz-releases/5.0.1/graphviz-5.0.1.tar.xz
xz --uncompress graphviz-5.0.1.tar.xz
tar -xf graphviz-5.0.1.tar
cd graphviz-5.0.1

### NOTE: Problem with fast-math and latest glibc
# workaround for now: remove the `-ffast-math` flag from the `configure`
# script before running
### NOTE 2: Don't modify configure.ac - aclocal is not easily installable in
# manylinux2014 container

# Build
./configure
make -j8
make install
# Verify that stuff is installed
neato -V  # Should have graphviz v3.0.0
# Get pygraphviz source
cd  # move to "home" directory
git clone https://github.com/rossbar/pygraphviz.git && cd pygraphviz
git checkout --track origin/cgraph-lib-interface  # Checkout libbed version
# Add setup dirs and library dirs to setup.py
# include_dirs = ["/usr/local/include/graphviz"]
# library_dirs = ["/usr/local/lib"]
python3.10 -m pip install --upgrade pip
python3.10 -m pip install --upgrade build
git clean -xdf
python3.10 -m build
# See what auditwheel has to say
auditwheel show dist/pygraphviz-1.10rc1.dev0-cp310-cp310-linux_x86_64.whl
auditwheel repair dist/pygraphviz-1.10rc1.dev0-cp310-cp310-linux_x86_64.whl
```

Testing the wheel:

Note re: testing wheels - as a first step, try on local system. Note however
that this is not necessarily a reliable test, as I almost always have graphviz
installed on the system. The next step after this local test would be to test
the wheel in *another* "clean" container that doesn't have graphviz installed.

In a new terminal:

10. Copy wheel out of docker container
   * Get name of running container: `sudo docker ps` (in my case it was `sweet_wiles`)
   * cp file out to host system: `sudo docker cp sweet_wiles:/root/pygraphviz/wheelhouse /tmp/pgv-wheels`
   * Cleanup: `sudo chown -hR ross:ross /tmp/pgv-wheels`

11. Basic test:
   * Create clean venv: `mkvirtualenv blah`
   * Verify it's empty: `pip list`
   * Install local wheel: `pip install /tmp/pgv-wheels/pygra<tab>`
     - Success, so that's a good sign :)
   * `python -c "import pygraphviz as pgv; A = pgv.AGraph(); A.layout()"`
