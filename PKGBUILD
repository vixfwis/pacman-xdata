# vim: set ts=2 sw=2 et:
# Maintainer: Dan McGee <dan@archlinux.org>
# Maintainer: Dave Reisner <dreisner@archlinux.org>

pkgname=pacman
pkgver=5.1.2
pkgrel=2
pkgdesc="A library-based package manager with dependency support"
arch=('x86_64')
url="https://www.archlinux.org/pacman/"
license=('GPL')
groups=('base' 'base-devel')
depends=('bash' 'glibc' 'libarchive' 'curl'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
makedepends=('asciidoc')
checkdepends=('python2' 'fakechroot')
optdepends=('perl-locale-gettext: translation support in makepkg-template'
            'xdelta3: delta support in repo-add')
backup=(etc/pacman.conf
        etc/makepkg.conf)
options=('strip' 'debug')
validpgpkeys=('6645B0A8C7005E78DB1D7864F99FFE0FEAE999BD'  # Allan McRae <allan@archlinux.org>
              'B8151B117037781095514CA7BBDFFC92306B1121') # Andrew Gregory (pacman) <andrew@archlinux.org>
source=(https://sources.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        pacman.conf
        makepkg.conf)
sha256sums=('ce4eef1585fe64fd1c65c269e263577261edd7535fe2278240103012d74b6ef6'
            'SKIP'
            'b82e9c1073292766c2f49b5c771dddf37383f1151117ef9cd5e0c11047b4f3eb'
            '8442bbb3d1d83f09610e9d687f9121037d97c6acaa06c4e907fc6e9eb0b127ec')

build() {
  cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc \
    --with-scriptlet-shell=/usr/bin/bash \
    --with-ldconfig=/usr/bin/ldconfig
  make V=1
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd "$pkgname-$pkgver"

  make DESTDIR="$pkgdir" install

  # install Arch specific stuff
  install -dm755 "$pkgdir/etc"
  install -m644 "$srcdir/pacman.conf" "$pkgdir/etc"
  install -m644 "$srcdir/makepkg.conf" "$pkgdir/etc"

  # put bash_completion in the right location
  install -dm755 "$pkgdir/usr/share/bash-completion/completions"
  mv "$pkgdir/etc/bash_completion.d/pacman" "$pkgdir/usr/share/bash-completion/completions"
  rmdir "$pkgdir/etc/bash_completion.d"

  for f in makepkg pacman-key; do
    ln -s pacman "$pkgdir/usr/share/bash-completion/completions/$f"
  done
}
