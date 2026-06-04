# arch-linux-repository

> **Work in progress** — builds are still being stabilised; some packages may be missing or broken.

A personal Arch Linux package repository that automatically builds AUR packages daily and publishes them as a single pacman-compatible repository via GitHub Releases.

## Usage

Add to `/etc/pacman.conf`:

```ini
[aur]
SigLevel = Optional TrustAll
Server = https://github.com/its-me/arch-linux-repository/releases/download/latest
```

Then sync and install packages as usual:

```sh
sudo pacman -Sy
sudo pacman -S <package>
```

## Packages

See the [`packages`](packages) file for the full list of included AUR packages.
