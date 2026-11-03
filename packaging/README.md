# Packaging

All packages build the Python wheel from `pyproject.toml` and then run
`make install-data` for the systemd user unit, D-Bus activation file, desktop
entry, icon and the optional GNOME Shell / KWin focus helpers.

- **Arch / AUR:** `packaging/arch/PKGBUILD` (expects a `brio-control-0.1.0.tar.gz` source tarball:
  `git archive --prefix=brio-control-0.1.0/ -o brio-control-0.1.0.tar.gz HEAD`).
- **Fedora / COPR:** `packaging/fedora/brio-control.spec` (same tarball).
- **Debian / Ubuntu:** copy `packaging/debian` to `debian/` at the source root and run
  `dpkg-buildpackage -us -uc -b`.

## CI and releases

`.github/workflows/packages.yml` builds the RPM (in `fedora:latest`) and the DEB (in
`debian:trixie`) on every push and pull request, and uploads them as workflow artifacts.
Pushing a `v*` tag also publishes a GitHub release with the packages attached.

To cut a release, bump the version in all three places (the workflow fails if they differ
or if they don't match the tag):

- `pyproject.toml` → `version`
- `packaging/fedora/brio-control.spec` → `Version` (and add a `%changelog` entry)
- `packaging/debian/changelog` → add a new entry (`dch -v X.Y.Z-1`)

Then tag and push:

    git tag v0.1.0 && git push origin v0.1.0

## After installing

Enable the daemon for your user:

    systemctl --user enable --now briod.service

The GUI also starts it on demand through D-Bus activation.
