# Maintainer: Minjun Kim <mjkim5216@gmail.com>
# Contributor: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Tom Gundersen <teg@jklm.no>
# Contributor: Thomas Baechler <thomas@archlinux.org>

# Local build of libfprint with SDCP v2 support for Egis match-on-chip
# readers (Galaxy Book4 Pro: 1c7a:05a1), from upstream MR !547:
# https://gitlab.freedesktop.org/libfprint/libfprint/-/merge_requests/547
#
# Local patches on top:
# - fix-too-hot-error-message.patch: the MR's new UNTRUSTED case took over
#   TOO_HOT's break, so "too hot" errors said "cannot be trusted".
# - egismoc-longer-thermal-limit.patch: libfprint's overheating guard
#   allows 10 min of continuous use for egismoc instead of 3, and cools
#   with a 15 min setting instead of 9 (usable again after about 6 min of
#   rest), so a lock screen that keeps listening doesn't trip it in normal use.
#
# Based on Arch's libfprint PKGBUILD. Replace with the official package
# once SDCP lands in a libfprint release: sudo pacman -S libfprint

pkgname=libfprint-sdcp
_commit=2d7c5277de08c6b29b3fac7447f17a516fbc4d1c # feature/sdcp-v2, 2026-04-07
pkgver=1.94.10.r27.g2d7c527
pkgrel=2
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
source=(
  "libfprint::git+https://gitlab.freedesktop.org/libfprint/libfprint.git#commit=$_commit"
  fix-too-hot-error-message.patch
  egismoc-longer-thermal-limit.patch
)
b2sums=('SKIP'
        'ae909cbd8db2181ff35ae0b331efb9171a73345ed362d138fc8de08de362122d6c1ecc4213fe8f0dd28a294d73e01748f08646a46ef4a109eff72c15b23bfe03'
        'b60795a43ebf1c79f47ccd424587edfbd16c9a27684edcfd04a6e91012a4f8eadaa1fd9c8772dbe3878cda24904bd668f46ebe5175320cf56d368f8d0f01b076')

prepare() {
  cd libfprint
  patch -Np1 -i ../fix-too-hot-error-message.patch
  patch -Np1 -i ../egismoc-longer-thermal-limit.patch
}

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
