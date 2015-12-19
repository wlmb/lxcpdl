# lxcpdl
lxc container with a full pdl environment

This is a full PDL installation within an lxc container running under Debian/Wheezy. 

This is my first attempt, so things may go wrong. Proceed with caution. It has been tested on a Debian/stretch host.

##Synopsis
```
$ sudo lxc-start pdl
# pdl
pdl> use PDL::Graphics::Gnuplot
pdl> gplot(zeroes(50)->xlinvals(0,15)->sin)
...
```
##Authors
Victoria Dominguez del Angel and W. Luis Mochan <mochan@fis.unam.mx>

##Usage

To run it you need a working lxc. In debian
```
$ sudo install lxc
...
$
```
As root untar the file pdl.tar.xz file under the root directory
```
$ cd /; sudo tar xfJv pdl.tar.xz
...
$
```
This should populate the directory `/var/lib/lxc/pdl` with the container.
To use it I do the following (I don't understand completely, but it has worked so far):
As an ordinary user, from an xterm run the command
```
$ xhost +local:
...
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
If no errors, you will be working within the container as superuser. Don't worry too much, as you cannot damage your host. Anyway, there is an ordinary sudoer user (login: user, initial password: user) if you want to drop privileges.
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
...
pdl> demo 3d
...
pdl> demo prima
...
pdl> <control>-d
$
```
The container is a debian installation running on top of your host, but insulated from it, so you may run other debian programs such as apt-get without greatly affecting the host.
```
$ sudo apt-get install ...
...
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

## Configuration
You may have to modify the network settings `lxc.network.xxx`of the container in the file `/var/lib/lxc/pdl/config` if they are incompatible with your system. I set up my network following <https://wiki.debian.org/LXC/LibVirtDefaultNetwork>.
The line `lxc.mount.entry = /tmp/.X11-unix tmp/.X11-unix none bind,optional,create=dir` allows the container to open X windows in the hosts display.

