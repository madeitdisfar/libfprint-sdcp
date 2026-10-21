# Maintainer: Minjun Kim <mjkim5216@gmail.com>
# Contributor: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Tom Gundersen <teg@jklm.no>
# Contributor: Thomas Baechler <thomas@archlinux.org>

# Local build of libfprint with SDCP v2 support for Egis match-on-chip
# readers (Galaxy Book4 Pro: 1c7a:05a1), from upstream MR !547:
# https://gitlab.freedesktop.org/libfprint/libfprint/-/merge_requests/547
#
# Based on Arch's libfprint PKGBUILD. Replace with the official package
# once SDCP lands in a libfprint release: sudo pacman -S libfprint

pkgname=libfprint-sdcp
_commit=2d7c5277de08c6b29b3fac7447f17a516fbc4d1c # feature/sdcp-v2, 2026-04-07
pkgver=1.94.10.r27.g2d7c527
pkgrel=1
pkgdesc="Library for fingerprint readers (with SDCP support from MR !547)"
url="https://fprint.freedesktop.org/"
arch=(x86_64)
license=(LGPL-2.1-or-later)
depends=(
  libgcc
  glib2
  glibc
  libgudev
  libgusb
  openssl
  pixman
)
makedepends=(
  git
  glib2-devel
  gobject-introspection
  meson
  python-cairo
  python-gobject
  systemd
)
checkdepends=(
  cairo
  umockdev
)
provides=(libfprint libfprint-2.so)
conflicts=(libfprint)
source=("libfprint::git+https://gitlab.freedesktop.org/libfprint/libfprint.git#commit=$_commit")
b2sums=('SKIP')

build() {
  local meson_options=(
    -D drivers=all
    -D doc=false
    -D installed-tests=false
  )

  arch-meson libfprint build "${meson_options[@]}"
  meson compile -C build
}

check() {
  # Skip metainfo-validate (in the "data" suite): it fetches the GitLab
  # issues URL, which returns 404 to anonymous clients
  meson test -C build --print-errorlogs --no-suite data
  meson test -C build --print-errorlogs udev-hwdb
}

package() {
  meson install -C build --destdir "$pkgdir"
}
