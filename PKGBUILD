# U-Boot: PineTab, based on Pine64 PKGBUILD
# Maintainer: Dan Johansen <strit@manjaro.org>

pkgname=uboot-pinetab
pkgver=2021.07
pkgrel=1
pkgdesc="U-Boot for Pine64 PineTab"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
makedepends=('bc' 'python-setuptools' 'swig' 'dtc')
provides=('uboot')
conflicts=('uboot')
install=${pkgname}.install
_tfaver=2.5
source=("ftp://ftp.denx.de/pub/u-boot/u-boot-${pkgver/rc/-rc}.tar.bz2"
        "https://git.trustedfirmware.org/TF-A/trusted-firmware-a.git/snapshot/trusted-firmware-a-$_tfaver.tar.gz")
md5sums=('7afbe0ef070dc0e8e970c57a08e3f336'
         'cd0455f0dcd4161201074bacb93446b1')

build() {
  unset CFLAGS CXXFLAGS CPPFLAGS LDFLAGS

  cd trusted-firmware-a-${_tfaver}

  make PLAT=sun50i_a64 DEBUG=1 bl31
  cp build/sun50i_a64/debug/bl31.bin ../u-boot-${pkgver/rc/-rc}

  cd ../u-boot-${pkgver/rc/-rc}

  # This builds the PineTab uboot.
  echo "Building U-Boot for PineTab..."
  make distclean
  make sopine_baseboard_defconfig
  echo 'CONFIG_IDENT_STRING=" Manjaro ARM"' >> .config
  make SCP=/dev/null EXTRAVERSION=-${pkgrel}
  cat u-boot-sunxi-with-spl.bin > u-boot-sunxi-with-spl-pinetab.bin
}   

package() {
  cd u-boot-${pkgver/rc/-rc}
  mkdir -p "${pkgdir}"/boot/extlinux
  cp u-boot-sunxi-with-spl-pinetab.bin "${pkgdir}"/boot
 }
