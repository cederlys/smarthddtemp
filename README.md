# smarthddtemp - Reading HDD and SSD temperature

This is `smarthddtemp`, a utility that can display the temperature of
many storage devices.  It uses `smartctl` to read the temperature, so
any device that `smartctl` supports can be used.  This includes most
HDD, SDD and NVMe drives currently in use, and possibly other types of
storage devices as well.

## Examples

To scan for available drives:

```
$ smarthddtemp --scan
/dev/nvme0
/dev/sda
```

To display the temperature:

```
$ smarthddtemp --sudo /dev/nvme0
46
```

To run it as a Nagios plugin:

```
$ smarthddtemp --nagios --sudo --good 20-60 --acceptable 15-85 \
    --min 0 --max 100 /dev/nvme0
OK - Current temperature: 46°C | temp=46°C;20:60;15:85;0;100
```

## Sudo configuration

To read the temperature, `smartctl` needs root privileges.  You can
either run `smarthddtemp` as `root` (not recommended), or use the
`--sudo` option to tell `smarthddtemp` to use `sudo` when running
`smartctl`.  If you store the following snippet in
`/etc/sudoers.d/smarthddtemp` it will make it possible for the nagios
user to use `smarthddtemp` on the `/dev/nvme0` and `/dev/sda` disks
without providing a password:

```
nagios ALL = (ALL) NOPASSWD: /usr/sbin/smartctl -j -x /dev/nvme0
nagios ALL = (ALL) NOPASSWD: /usr/sbin/smartctl -j -x /dev/sda
```

## Installation

The `smarthddtemp` program is a single self-contained shellscript.
You can use `make install` to install it in
`/usr/local/bin/smarthddtemp`, but you can also just copy it wherever
you want.

## Dependencies

You need `smartctl`, `jq` and `mktemp`.  Unless you are root, you may
also need `sudo`.  On a Debian-derived system, you can install all
dependencies like this:

```
apt install smartmontools jq coreutils sudo
```

This script assumes you are using a modern Linux distribution where
all programs reside in `/usr`.  If for instance `mktemp` is only
available as `/in/mktemp` you will have to make some edits.  But
really, you probably should upgrade your system instead...
