# U-Boot: PineTab
# Maintainer: Dan Johansen <strit@manjaro.org>

pkgname=uboot-pinetab
pkgver=2019.07
pkgrel=1
pkgdesc="U-Boot for PineTab"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
makedepends=('bc' 'git' 'python' 'swig' 'dtc' 'python2-pyelftools')
install=${pkgname}.install
_ATFcommit=63b9b5425f15
source=("https://github.com/u-boot/u-boot/archive/v${pkgver}.tar.gz"
        "git+https://github.com/ARM-software/arm-trusted-firmware.git#commit=${_ATFcommit}"
	'0001-ATF-set-fno-stack-protector.patch'
        'boot.txt'
        'mkscr')
md5sums=('0af8c8867b9557d53fe0e2da01fb4b65'
         'SKIP'
         '3213c96ce0713249719dc73d58395620'
         '869b2c35ef24b130114d8bba50d89e8f'
         '021623a04afd29ac3f368977140cfbfd')

#pkgver() {
#  cd u-boot
#  git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
#}

prepare() {
  cd u-boot-${pkgver}

  cd ../arm-trusted-firmware
  git apply ../0001-ATF-set-fno-stack-protector.patch
}

build() {
  unset CFLAGS CXXFLAGS CPPFLAGS LDFLAGS

  cd arm-trusted-firmware

  make PLAT=sun50i_a64 DEBUG=1 bl31
  cp build/sun50i_a64/debug/bl31.bin ../u-boot-${pkgver}

  cd ../u-boot-${pkgver}

  make distclean
  #make sopine_baseboard_defconfig
  make pine64-lts_defconfig
  echo 'CONFIG_IDENT_STRING=" Manjaro ARM"' >> .config
  make EXTRAVERSION=-${pkgrel}
  cat spl/sunxi-spl.bin u-boot.itb > u-boot-sunxi-with-spl-pinetab.bin
}

package() {
  cd u-boot-${pkgver}

  mkdir -p "${pkgdir}"/boot

  cp u-boot-sunxi-with-spl-pinetab.bin "${pkgdir}"/boot
  cp ../boot.txt ../mkscr "${pkgdir}"/boot
  tools/mkimage -A arm -O linux -T script -C none -n "U-Boot boot script" -d "${pkgdir}"/boot/boot.txt "${pkgdir}"/boot/boot.scr
}
