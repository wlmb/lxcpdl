# lxcpdl
[lxc](https://linuxcontainers.org/lxc/introduction/) container with a
full [pdl](http://pdl.perl.org/) environment 

This is a full PDL installation within an LXC container running under
Debian/Wheezy that can be used within a host running a mostly
arbitrary linux flavor.  The idea is that within the container, PDL
and all the packages required for its use are already installed,
saving time and effort and allowing new users to test a full pdl
installation with little effort. 

This is my first attempt, so things may go wrong. Proceed with
caution. It has been tested on a Debian/stretch host. 

##Synopsis
```
$ sudo lxc-start pdl
# pdl
pdl> use PDL::Graphics::Gnuplot
pdl> gplot(zeroes(50)->xlinvals(0,15)->sin)
pdl> q
$ perl -MPDL -E 'say zeroes(10)->xvals'
[0 1 2 3 4 5 6 7 8 9]
$ 
```

##Installation

To run it you need a working lxc. In debian
```
$ sudo install lxc
$
```
You would have to clone the repository
```
$ git clone https://github.com/wlmb/lxcpdl.git
$
```
(it will take a long time; it's size is about 300MB+).
As github doesn't allow very large files, and I don't know how to
commit files owned by root, I made a tar file and then split it in
pieces. First join the pieces of the tar file:
```
$ cd lxcpdl
$ cat pieces-* >pdl.tar.xz
$ 
```
As root untar the file pdl.tar.xz file under the lxc directory
```
$ cd /var/lib/lxc
$ sudo tar xfJv  /path/to/lxcpdl/pdl.tar.xz --numeric-owner
$
```
It seems the `--numeric-owner` option is important.
This should populate the directory `/var/lib/lxc/pdl` with the container.

##Usage
To use it I do the following (I don't understand completely, but it
has worked so far): 
As an ordinary user running X, from an xterm run the command
```
$ xhost +local:
$
```
to allow the container to use your X display. Then fire your container as a daemon
```
$ sudo lxc-start -n pdl -d
$
```
and connect to it
```
$ sudo lxc-attach -n pdl
#
```
If no errors, that command will take you to the container and you will
be working within the container as superuser. Don't worry too much, as
you cannot damage your host. Anyway, there is an ordinary sudoer user
(login: user, initial password: user) if you want to drop privileges. 
```
# su user
$
```
You have to initializa the DISPLAY environment variable, e.g.
```
$ export DISPLAY=:0
```
You can run pdl and try out the demos
```
$ pdl
pdl> demo 3d
pdl> demo prima
pdl> <control>-d
$
```
or run PDL in any other way you want
```
$ perl -MPDL -E 'say zeroes(10)->xvals'
[0 1 2 3 4 5 6 7 8 9]
$ 
```
The container is a debian installation running on top of your host,
but insulated from it, so you may run other debian programs such as
apt-get mostly without affecting the host.  
```
$ sudo apt-get install ...
$
```
When done, you may leave your user and your root sessions, which takes you back to your host
```
$ exit
# exit
$
```
where you can stop the container
```
$ sudo lxc-stop -n pdl
$
```
If you don't like it, you may remove it with
```
$ sudo lxc-delete -n pdl
$
```

## Configuration
You may have to modify the network settings `lxc.network.xxx` of the
container in the file `/var/lib/lxc/pdl/config` if they are
incompatible with your system. I set up my network following
<https://wiki.debian.org/LXC/LibVirtDefaultNetwork>. 

The line
`lxc.mount.entry = /tmp/.X11-unix tmp/.X11-unix none
bind,optional,create=dir` allows the container to open X windows in 
the hosts display. 

##Package contents

Debian/Wheezy + deb-multimedia.org sources

Debian packages:

   apt-utils
   aptitude
   cmake
   cpanminus
   deb-multimedia-keyring
   dialog
   freeglut3-dev
   gfortran
   gnuplot-qt
   lapack-dev
   less
   libcfitsio3-dev
   libfridi-dev
   libgd2-xpm-dev
   libgif-dev
   libgsl0-dev
   libgtk-3-dev
   libhdf4-dev
   libhdf5-dev
   libnetpbm10
   libproj-dev
   libterm-readline-perl-perl
   libtiff4-dev
   libxft-dev
   libxi-dev
   libxmu-dev
   libxrender-dev
   make
   nano
   netpbm
   openssh-server
   tmux
   pgplot5
   sharutils
   sudo
   wget
   xterm

CPAN packages:

   Astro::FITS::Header
   Astro::FITS::CFITSIO
   Convert::UU
   Devel::CheckLib
   Data::Dumper
   Devel::REPL
   ExtUtils::F77
   ExtUtils::MakeMaker
   File::Map
   File::Spec
   File::Temp
   Filter::Simple
   Filter::Util::Call
   Inline
   Inline::C
   Module::Compile
   OpenGL
   PGPLOT
   Pod::Parser
   Pod::Select
   Prima
   Storable
   Test::Exception
   Text::Balanced
   Text::Bidi

   PDL
   PDL::Graphics::Gnuplot
   PDL::Stats
   PDL::LinearAlgebra
   PDL::Graphics::Prima
   PDL::IO::CSV
   PDL::IO::DBI
   

and their dependencies.

 

##Authors
Victoria Domínguez del Angel and W. Luis Mochán <mochan@fis.unam.mx>

##License
GPL v2.0

