# LXC Tricks

Small bash script that manages LXC containers, performs operations on one
of the containers or all configured containers at once.

## Prerequisites

All active containers should have:

 1. **User with name same as container name** - `sh` command assumes that all
    containers have user with the same name as container name. Sh commands
    are performed as this user.
 2. **Running ssh server** - `cp` command uses `scp` program to perform
    transfers between containers.

## Installation

Simply download the script and make it an executable file.

```bash
BIN_DIR=~/bin # change this if you want
mkdir -p $BIN_DIR
wget https://github.com/muroc/lxc-tricks/raw/master/container \
  -O $BIN_DIR/container
chmod +x $BIN_DIR/container
```

If `BIN_DIR` is no on the `PATH`, modify `PATH` in rc file of your shell.

```bash
RC_FILE=~/.bashrc # change if you are not using bash
echo "PATH=$BIN_DIR:$PATH" >> $RC_FILE
. $RC_FILE
```

## Configuration

```bash
cat > ~/.c/defaults << EOF
ACTIVE_CONTAINERS='browser gamedev' # replace with names of your containers
FORCE_TERMINAL='mate-terminal' # change to terminal program you are using
EOF
```

## Usage examples

The basics:

```bash
container help # prints detailed usage information
container start # starts all active containers
container browser start # starts container of name browser
container browser sh # starts shell in container browser as user browser
container browser # same as above sh is the default action
container browser sudo # starts root shell in container browser
```

### Copying files between containers

Following command copies file /home/browser/Downloads/photo.jpg from container
*browser* to container *gamedev* at location */home/gamedev/photo.jpg*:

```bash
container cp browser:Downloads/photo.jpg gamedev:
```

Transfer is done using `scp` program, so ssh servers running on the containers
are needed to perform the transfer.

### Container symlinks

Name of the container can be read from program name (busybox-like behavior).
Creating symlink for each container shortens each command by 10 characters.

Following script creates symlink for container named *browser*:

```bash
BIN_DIR=~/bin
cd $BIN_DIR
ln -s container browser
```

Now we can operate on this container through symlink:

```bash
browser start
browser firefox https://github.com/muroc/lxc-tricks
browser stop
```

# Contribution

Push requests are very welcome.

## TODO

 * container activation based on existance of configuration file
   `~/.c/$CONTAINER_NAME`
 * default *USERNAME* configuration for `sh` command
 * per-container username configuration for `sh` command
   to remove username prerequisite
 * per-container *FORCE_TERMINAL* configuration
 * `cp` command based on `lxc-usernsexec` to remove ssh prerequisite

