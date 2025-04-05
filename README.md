# Mac OS 9 Toolbox

This is a collection of various useful tidbits I've collected to make using an old Mac OS 9 machine a bit easier.

## Connecting from Mac OS 9 to a modern machine for file sharing
This uses [Netatalk](https://github.com/Netatalk/netatalk) and Docker, via the [servercontainers/netatalk](https://hub.docker.com/r/servercontainers/netatalk) image, to allow for connecting via AFP from the Chooser inside Mac OS 9 to your modern machine of choice.

1. `cd` into the `netatalk` directory in this repository
2. Create a file called `.env` with the following contents: `SHARED_DIR=/absolute/path/to/a/directory/to/share`
3. Start up the container with `docker-compose up`
4. On the Mac OS 9 machine, go into the Chooser and click "Server IP address" then put in the IP or hostname of your machine running the Netatalk image

### Permission issues
I've found permissions to be a bit of a pain with this unfortunately. Reading just works, but writing has some horrendous intersection of the host machine, Docker running on the host machine, and Netatalk that I haven't been able to satisfactorily untangle. In my case where the host machine is macOS and I'm running Docker for Mac, running this command to set the extended attributes for the Docker ownership of the shared directory worked well enough:

```
xattr -w com.docker.grpcfuse.ownership '{"UID":65534,"GID":65534}' /path/to/the/shared/directory
```

The UID and GID there are the user and group IDs of the `nobody` user that you connect to Netatalk with as a guest. This seems to require a `docker-compose down && docker-compose up` to take effect if it's already running.

## Browsing the internet with an old web browser
This uses [Webone](https://github.com/atauenis/webone), via Docker and the [u306060/webone](https://hub.docker.com/r/u306060/webone) image, to act as a proxy to allow old machines to talk to modern HTTPS websites.

1. `cd` into the `webone` directory in this repository
2. Start up the container with `docker-compose up`
3. Open up your browser of choice on the old computer and find the proxy settings in its preferences
4. Set the proxy address to the IP address or hostname of the machine running the Docker container, and set the port to 8080

When entering URLs in the browser itself, make sure you use `http://` and *not* `https://`.

## Converting Mac OS 9's PICT format screenshots
Install [ImageMagick](https://imagemagick.org) (under macOS, use [Homebrew](https://brew.sh) and install it with `brew install imagemagick`), then use the `convert` utility.

Screenshots are saved without any file extensions, so you'll need to either manually add `.pict` to the end of the filename, or add the `pict:` prefix to the file to tell ImageMagick what the specific intput file type is:

```
convert pict:"Picture 1" "Picture 1.png"
```

## Fun things
* [Macstodon](https://github.com/smallsco/macstodon) — A Mastodon client for Mac OS 9 and earlier
* [Escape Velocity: Override](https://www.macintoshrepository.org/9213-escape-velocity-override) — A space combat/trading game. Needs a serial number which can be generated using [ARRT](https://github.com/spolsley/ARRT)
* [hotline](https://github.com/mierau/hotline) — A modern [Hotline](https://en.wikipedia.org/wiki/Hotline_Communications) client written in SwiftUI
* [mobius](https://github.com/jhalter/mobius) — A cross-platform CLI-only Hotline server written in Go
