# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux credits:
# Massimiliano Torromeo <massimiliano.torromeo@gmail.com>
# Bob Fanger < bfanger(at)gmail >
# Filip <fila pruda com>, Det < nimetonmaili(at)gmail >

_linuxprefix=linux-xanmod
_kernver="$(cat /usr/src/${_linuxprefix}/version)"
pkgname=$_linuxprefix-rtl8723bu
_pkgname=rtl8723bu
_libname=8723bu
_commit=49cb04d796d44debe965c1597f397f34ae277dbc
pkgver=20231204
pkgrel=67510
pkgdesc="A kernel module for Realtek 8723bu network cards"
url="http://www.realtek.com.tw"
license=("GPL")
arch=('x86_64')
depends=("$_linuxprefix")
makedepends=("$_linuxprefix-headers")
builddepends=("$_linuxprefix-headers")
provides=("$_pkgname=$pkgver")
groups=("$_linuxprefix-extramodules")
source=("${_pkgname}-${pkgver}.zip::https://github.com/lwfinger/rtl8723bu/archive/$_commit.zip"
        "blacklist-rtl8xxxu.conf")
sha256sums=('83e2c19c7ad546b01a946805aeb5c0d9795d7a41b5b49b61f358d168346ddad1'
            '7c726ad04083c8e620bc11c837e5f51d3e9e2a5c3e19c333b2968eb39f1ef07e')

prepare() {
    cd "$_pkgname-$_commit"

    local src
    for src in "${source[@]}"; do
        src="${src%%::*}"
        src="${src##*/}"
        [[ $src = *.patch ]] || continue
        msg2 "Applying patch: $src..."
        patch -Np1 < "../$src"
    done
}

build() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"
    cd "$_pkgname-$_commit"
    # do not compile with CONCURRENT_MODE
    sed -i 's/EXTRA_CFLAGS += -DCONFIG_CONCURRENT_MODE/#EXTRA_CFLAGS += -DCONFIG_CONCURRENT_MODE/g' Makefile

    # avoid using the Makefile directly -- it doesn't understand
    # any kernel but the current.
    make clean
    make -C /usr/lib/modules/$_kernver/build M="$srcdir/$_pkgname-$_commit" modules
}

package() {
    install -D -m 644 "blacklist-rtl8xxxu.conf" "${pkgdir}/etc/modprobe.d/${_linuxprefix}-blacklist-rtl8xxxu.conf"

    install -D -m 644 "$_pkgname-$_commit/$_libname.ko" "$pkgdir/usr/lib/modules/${_kernver}/extramodules/$_libname.ko"

    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;
}
