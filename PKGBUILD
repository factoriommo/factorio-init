# Maintainer: Maikel Wever <maikelwever@gmail.com>

# This is a WIP and doesnt work.

exit 0

pkgname=factorio-init
pkgver=1.0.0
pkgrel=1
pkgdesc="Factorio init script."
arch=('x86_64')
url="https://github.com/lxc/lxd"
license=('APACHE')
depends=()
options=('!strip' '!emptydirs')
source=(
    "dnsmasq-lxd.conf"
    "dnsmasq@lxd.service"
)

md5sums=('SKIP'
         'SKIP')

build() {
  mkdir -p $srcdir/src/${_gourl}
  cp -r --preserve=timestamps $srcdir/$pkgname-$pkgname-$pkgver/* $srcdir/src/${_gourl}/
  cd $srcdir/src/${_gourl}
  GOPATH="$srcdir" go get
  GOPATH="$srcdir" go build
  GOPATH="$srcdir" make
}

package() {
  install=lxd.install

  mkdir -p "$pkgdir/usr/bin"
  mkdir -p "$pkgdir/usr/lib/lxd/"

  install -p -m755 "$srcdir/bin/"* "$pkgdir/usr/bin"
  mv "$pkgdir/usr/bin/lxd-bridge-proxy" "$pkgdir/usr/lib/lxd/"

  install -p -m755 "$srcdir/src/$_gourl/lxd-bridge/lxd-bridge" "$pkgdir/usr/lib/lxd/"

  # Package license (if available)
  for f in LICENSE COPYING LICENSE.* COPYING.*; do
    if [ -e "$srcdir/src/$_gourl/$f" ]; then
      install -Dm644 "$srcdir/src/$_gourl/$f" \
        "$pkgdir/usr/share/licenses/$pkgname/$f"
    fi
  done

  install -D -m644 "${srcdir}/lxd.service" \
      "${pkgdir}/usr/lib/systemd/system/lxd.service"


  # Example configuration files.
  mkdir -p "$pkgdir/usr/share/lxd/"
  mkdir -p "$pkgdir/usr/share/lxd/systemd/system/"
  mkdir -p "$pkgdir/usr/share/lxd/netctl/"
  mkdir -p "$pkgdir/usr/share/lxd/dbus-1/system.d/"
  mkdir -p "$pkgdir/usr/share/lxd/NetworkManager/dnsmasq.d/"

  install -Dm644 "${srcdir}/dnsmasq-lxd.conf" "${pkgdir}/usr/share/lxd/dnsmasq-lxd.conf"
  install -Dm644 "${srcdir}/dnsmasq@lxd.service" "${pkgdir}/usr/share/lxd/systemd/system/dnsmasq@lxd.service"
  install -Dm644 "${srcdir}/lxd.netctl" "${pkgdir}/usr/share/lxd/netctl/lxd"
  install -Dm644 "${srcdir}/dbus-dnsmasq-lxd.conf" "${pkgdir}/usr/share/lxd/dbus-1/system.d/dnsmasq-lxd.conf"
  install -Dm644 "${srcdir}/networkmanager-dnsmasq-lxd.conf" "${pkgdir}/usr/share/lxd/NetworkManager/dnsmasq.d/lxd.conf"

}

# vim:set ts=2 sw=2 et:
