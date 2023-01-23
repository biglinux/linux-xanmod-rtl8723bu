# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux credits:
# Massimiliano Torromeo <massimiliano.torromeo@gmail.com>
# Bob Fanger < bfanger(at)gmail >
# Filip <fila pruda com>, Det < nimetonmaili(at)gmail >

_linuxprefix=linux-xanmod
_extramodules=$(find /usr/lib/modules -type d -iname 6.1.7*xanmod* | rev | cut -d "/" -f1 | rev)
pkgname=$_linuxprefix-rtl8723bu
_pkgname=rtl8723bu
_libname=8723bu
_commit=d79a676a8d3f0bb6ac8af126689c6ac6869cb6f2
pkgver=470.161.03
pkgrel=6171
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
        "blacklist-rtl8xxxu.conf"
        'linux61.patch')
sha256sums=(SKIP SKIP SKIP)


install=rtl8723bu.install

prepare() {
    cd "$_pkgname-$_commit"
    patch -p1 -i ../linux61.patch
}
build() {
    _kernver=$(find /usr/lib/modules -type d -iname 6.1.7*xanmod* | rev | cut -d "/" -f1 | rev)
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

    install -D -m644 "$_pkgname-$_commit/$_libname.ko" "$pkgdir/usr/lib/modules/$_extramodules/$_libname.ko"

    # set the kernel we've built for inside the install script
    sed -i -e "s/EXTRAMODULES=.*/EXTRAMODULES=${_extramodules}/g" "${startdir}/${install}"

    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;
}
