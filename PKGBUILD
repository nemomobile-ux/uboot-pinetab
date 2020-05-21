# U-Boot: PineTab
# Maintainer: Dan Johansen <strit@manjaro.org>

pkgname=uboot-pinetab
pkgver=2020.04
pkgrel=1
_tfaver=2.3
pkgdesc="U-Boot for PineTab"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
makedepends=('bc' 'git' 'python' 'swig' 'dtc')
backup=('boot/extlinux/extlinux.conf')
install=${pkgname}.install
source=("ftp://ftp.denx.de/pub/u-boot/u-boot-${pkgver/rc/-rc}.tar.bz2"
        "https://git.trustedfirmware.org/TF-A/trusted-firmware-a.git/snapshot/trusted-firmware-a-$_tfaver.tar.gz"
        'extlinux.conf')
md5sums=('51113d2288c55110e33a895c65ab9f60'
         '628a32a3c3b3f0c567d1ea6ee5582807'
         '8bf6e72352f58d0b7cbb5b5c15ae0a0a')

build() {
  unset CFLAGS CXXFLAGS CPPFLAGS LDFLAGS

  cd trusted-firmware-a-$_tfaver

  make PLAT=sun50i_a64 DEBUG=1 bl31
  cp build/sun50i_a64/debug/bl31.bin ../u-boot-${pkgver/rc/-rc}

  cd ../u-boot-${pkgver/rc/-rc}

  make distclean
  #make sopine_baseboard_defconfig
  make pine64-lts_defconfig
  echo 'CONFIG_IDENT_STRING=" Manjaro ARM"' >> .config
  make EXTRAVERSION=-${pkgrel}
  cat spl/sunxi-spl.bin u-boot.itb > u-boot-sunxi-with-spl-pinetab.bin
}

package() {
  cd u-boot-${pkgver/rc/-rc}

  mkdir -p "${pkgdir}"/boot/extlinux

  cp u-boot-sunxi-with-spl-pinetab.bin "${pkgdir}"/boot
  cp "${srcdir}"/extlinux.conf "${pkgdir}"/boot/extlinux/
}
