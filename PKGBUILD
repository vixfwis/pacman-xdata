# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Morten Linderud <foxboron@archlinux.org>

pkgname=pacman
pkgver=6.1.0
pkgrel=1
pkgdesc="A library-based package manager with dependency support"
arch=('x86_64')
url="https://www.archlinux.org/pacman/"
license=('GPL-2.0-or-later')
depends=('bash' 'glibc' 'libarchive' 'curl' 'gpgme' 'pacman-mirrorlist'
         'gettext' 'gawk' 'coreutils' 'gnupg' 'grep')
makedepends=('meson' 'asciidoc' 'doxygen')
checkdepends=('python' 'fakechroot')
optdepends=('perl-locale-gettext: translation support in makepkg-template')
provides=('libalpm.so')
backup=(etc/pacman.conf
        etc/makepkg.conf)
options=('strip')
validpgpkeys=('6645B0A8C7005E78DB1D7864F99FFE0FEAE999BD'  # Allan McRae <allan@archlinux.org>
              'B8151B117037781095514CA7BBDFFC92306B1121') # Andrew Gregory (pacman) <andrew@archlinux.org>
source=(https://gitlab.archlinux.org/pacman/pacman/-/releases/v$pkgver/downloads/pacman-$pkgver.tar.xz{,.sig}
        makepkg-remove-libdepends-and-libprovides.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/354a300cd26bb1c7e6551473596be5ecced921de.patch
        pacman.conf
        makepkg.conf)
sha256sums=('5a60ac6e6bf995ba6140c7d038c34448df1f3daa4ae7141d2cad88eeb5f1f9d9'
            'SKIP'
            'b3bce9d662e189e8e49013b818f255d08494a57e13fc264625f852f087d3def2'
            '656c4d4cb8cb12adbf178fc8cb2fd25f8c285d6572bbdbb24d865d00e0d5a85a'
            '9a7a4be6174b4e43c0070ee6291fea281fe172d7975fd3c1506cb451d2d761c4')

prepare() {
  cd "$pkgname-$pkgver"

  # revert libdepends and libprovides removal until we're ready
  patch -RNp1 < ../makepkg-remove-libdepends-and-libprovides.patch

  # apply potential patches/backports
  local -a patches
  patches=($(printf '%s\n' "${source[@]}" | grep '.patch'))
  patches=("${patches[@]%%::*}")
  patches=("${patches[@]##*/}")

  # WARN: adjust/remove in the future
  # remove reversed patch from array
  patches=("${patches[@]:1}")

  if (( ${#patches[@]} != 0 )); then
    for patch in "${patches[@]}"; do
      msg2 "Applying patch $patch..."
      patch -Np1 < "../$patch"
    done
  fi
}

build() {
  cd "$pkgname-$pkgver"

  meson --prefix=/usr \
        --buildtype=plain \
        -Ddoc=enabled \
        -Ddoxygen=enabled \
        -Dscriptlet-shell=/usr/bin/bash \
        -Dldconfig=/usr/bin/ldconfig \
        build

  meson compile -C build
}

check() {
  cd "$pkgname-$pkgver"

  meson test -C build
}

package() {
  cd "$pkgname-$pkgver"

  DESTDIR="$pkgdir" meson install -C build

  # install Arch specific stuff
  install -dm755 "$pkgdir/etc"
  install -m644 "$srcdir/pacman.conf" "$pkgdir/etc"
  install -m644 "$srcdir/makepkg.conf" "$pkgdir/etc"

  local wantsdir="$pkgdir/usr/lib/systemd/system/sockets.target.wants"
  install -dm755 "$wantsdir"

  local unit
  for unit in dirmngr gpg-agent gpg-agent-{browser,extra,ssh} keyboxd; do
    ln -s "../${unit}@.socket" "$wantsdir/${unit}@etc-pacman.d-gnupg.socket"
  done
}

# vim: set ts=2 sw=2 et:
