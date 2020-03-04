# U-Boot: PineTab
# Maintainer: Dan Johansen <strit@manjaro.org>

pkgname=uboot-pinetab
pkgver=2020.01
pkgrel=1
pkgdesc="U-Boot for PineTab"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
makedepends=('bc' 'git' 'python' 'swig' 'dtc')
install=${pkgname}.install
_ATFcommit=63b9b5425f15
source=("https://github.com/u-boot/u-boot/archive/v${pkgver}.tar.gz"
        "git+https://github.com/ARM-software/arm-trusted-firmware.git#commit=${_ATFcommit}"
        '0001-ATF-set-fno-stack-protector.patch'
        'extlinux.conf')
md5sums=('575ee541f59985f8869b7b3137ed597c'
         'SKIP'
         '3213c96ce0713249719dc73d58395620'
         'e0b2b6ed48d6e6ddde84f29650d92570')


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

  mkdir -p "${pkgdir}"/boot/extlinux

  cp u-boot-sunxi-with-spl-pinetab.bin "${pkgdir}"/boot
  cp "${srcdir}"/extlinux.conf "${pkgdir}"/boot/extlinux/
}
