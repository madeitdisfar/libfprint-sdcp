# libfprint-sdcp

An Arch package recipe for libfprint with SDCP support, for Egis match-on-chip fingerprint readers such as the one in the Samsung Galaxy Book4 Pro (`1c7a:05a1`).

## The problem

Newer Egis firmware only stores an enrolled fingerprint when the computer talks to it over SDCP (Secure Device Connection Protocol). libfprint 1.94.100 doesn't support SDCP, so enrolling looks successful but the print is never kept. The first check fails with "Print was not found on the devices storage", and fprintd then deletes the finger.

## The fix

This builds libfprint from the branch of upstream merge request [!547](https://gitlab.freedesktop.org/libfprint/libfprint/-/merge_requests/547) ("Implement SDCP v2"), pinned to the commit its author tested (`2d7c5277`). The recipe is based on Arch's libfprint PKGBUILD.

- The branch was last synced with upstream in April 2026, so it's older than libfprint 1.94.100 and lacks some of that release's later `egismoc` hardening.
- `check()` skips the `metainfo-validate` test, which fetches a GitLab URL that now returns 404 to anonymous clients.
- The package `provides` and `conflicts` with `libfprint`, so normal updates don't replace it. fprintd 1.94.5 works with it unchanged.

## Build and install

```
makepkg
sudo pacman -U libfprint-sdcp-*.pkg.tar.zst
sudo systemctl stop fprintd
```

Then enroll a finger, run `fprintd-verify`, and check that `fprintd-list "$USER"` still lists the finger.

## Switching back

Once a libfprint release includes SDCP support:

```
sudo pacman -S libfprint
```
