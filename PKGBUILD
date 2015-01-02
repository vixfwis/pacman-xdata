# vim: set ts=2 sw=2 et:
# Maintainer: Dan McGee <dan@archlinux.org>
# Maintainer: Dave Reisner <dreisner@archlinux.org>

pkgname=pacman
pkgver=4.2.0
pkgrel=5
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base' 'base-devel')
depends=('bash' 'glibc' 'libarchive>=3.1.2' 'curl>=7.39.0'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
checkdepends=('python2' 'fakechroot')
provides=('pacman-contrib')
conflicts=('pacman-contrib')
replaces=('pacman-contrib')
backup=(etc/pacman.conf etc/makepkg.conf)
options=('strip' 'debug')
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        pacman.conf.i686
        pacman.conf.x86_64
        makepkg.conf
        pacman-4.2.0-roundup.patch)
md5sums=('184ce14f1f326fede72012cca51bba51'
         'SKIP'
         '2db6c94709bb30cc614a176ecf8badb1'
         'de74a13618347f08ae4a9637f74471c4'
         '0d07f2a750ba20d114c3adc799d371de'
         '8b8e478e3b6f785f6fb0029f792cea38')
validpgpkeys=('6645B0A8C7005E78DB1D7864F99FFE0FEAE999BD')  # Allan McRae <allan@archlinux.org>

prepare() {
  cd "$pkgname-$pkgver"
  
  # v4.2.0-13-gacc639a
  patch -p1 -i $srcdir/pacman-4.2.0-roundup.patch
}

build() {
  cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc \
    --with-scriptlet-shell=/usr/bin/bash \
    --with-ldconfig=/usr/bin/ldconfig
  make
  make -C contrib
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd "$pkgname-$pkgver"

  make DESTDIR="$pkgdir" install
  make DESTDIR="$pkgdir" -C contrib install

  # install Arch specific stuff
  install -dm755 "$pkgdir/etc"
  install -m644 "$srcdir/pacman.conf.$CARCH" "$pkgdir/etc/pacman.conf"

  case $CARCH in
    i686)
      mycarch="i686"
      mychost="i686-pc-linux-gnu"
      myflags="-march=i686"
      ;;
    x86_64)
      mycarch="x86_64"
      mychost="x86_64-unknown-linux-gnu"
      myflags="-march=x86-64"
      ;;
  esac

  # set things correctly in the default conf file
  install -m644 "$srcdir/makepkg.conf" "$pkgdir/etc"
  sed -i "$pkgdir/etc/makepkg.conf" \
    -e "s|@CARCH[@]|$mycarch|g" \
    -e "s|@CHOST[@]|$mychost|g" \
    -e "s|@CARCHFLAGS[@]|$myflags|g"

  # put bash_completion in the right location
  install -dm755 "$pkgdir/usr/share/bash-completion/completions"
  mv "$pkgdir/etc/bash_completion.d/pacman" "$pkgdir/usr/share/bash-completion/completions"
  rmdir "$pkgdir/etc/bash_completion.d"

  for f in makepkg pacman-key; do
    ln -s pacman "$pkgdir/usr/share/bash-completion/completions/$f"
  done

  install -Dm644 contrib/PKGBUILD.vim "$pkgdir/usr/share/vim/vimfiles/syntax/PKGBUILD.vim"
}
