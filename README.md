# MusicPDs – My mpds

This repository contains scripts and notes to automate most of my
music box installations.

There are a few raspberry pis with [mpd](https://musicpd.org)
installed and [a nice web-based
frontend](https://eikek.github.io/mpc4s/) to mpd. This creates a
multi-room audio setup.

## Basics

The RPIs run [DietPi](https://github.com/Fourdee/dietpi) linux
distribution (which is based on raspbian).

Mpd is installed on all of them. There is one “master” mpd that builds
the database and that runs the mpc4s frontend. All other mpd use the
[proxy plugin](https://www.musicpd.org/doc/html/user.html#proxy) to
connect to this master mpd instance. They use the same music directory
to play files from, mounted via cifs.

Since they have different sound cards, different `mpd.conf` files are
deployed. The music directory is mounted via samba from the local NAS
to all mpds.


## Installation

A new Pi needs to first install dietpi. Insert the SD Card and run:

```
scripts/flash.sh /dev/<sdcard>
```

It will download the dietpi image and copy it onto the card. When
sshing into the machine the first time, you will be guided through the
setup.

I choose the following Options:

- SSH Server: OpenSSH (needed for scp)
- Webserver: Nginx (only for the master-mpd)

Go to Dietpi-Config and configure the soundcard and other hardware
(wifi etc).

Choose mpd from „Optimized Software”.

Run „Install”.

## Customize

Go to `config/global` and check the settings. Every machine must be
configured with a name and an IP address. The name of the "master mpd"
machine must be specified since this machine gets more stuff
installed. When everything looks ok, run

```
./mpds setup <name>
```

This will run necessary commands on the machine configured with
`<name>`. Use special name `all` to run setup for all machines.

## Travelpi

This branch contains some changes to create a pi for travelling. An
external disk is attached via USB that contains all the music. The
wifi is configured to hotspot mode and
[mpc4s](https://eikek.github.io/mpc4s/) is installed.

The outcome is a self-contained music player that can be hooked up
easily to any stereo (using RCA cables) or it can be used with head
phones.

Steps:

1. Configure the Pi to act as a hotspot
   - Run `dietpi-software`
   - goto `Optimized Software` and choos `WiFi Hotspot` from the list
   - Run `Install Software`
   - Note: it might be necessary to reinstall it, if `dietpi-config`
     doesn't recognize the hotspot
2. Get the IP of the hotspot
   - Log into your pi using the network cable and run `ip a`
   - Memorize the IP address from your wifi device, it may be
     `192.168.42.1`
3. Configure MPC4S webplayer
   - Go to `config/global` and add this ip to the `hotspotip`
     variable.
   - Make sure the `hostip[travelpi]` variable points to the correct
     ip you use to connect via SSH.
4. Check `config/global` again for correct settings. The USB disk
   should be in `fstab`
5. Run `mpds setup travelpi`
6. try to connect to the new hotpsot and browse to the music player
   using http://192.168.42.1:9600
