# Maintainer: Danct12 <danct12@disroot.org>
# Maintainer: Furkan K. <furkan@fkardame.com>
# TODO: Find a way to build crust inside this PKGBUILD, right now it is prebuilt.

pkgname=uboot-pinetab-crust
pkgver=2020.04
pkgrel=2
pkgdesc="U-Boot for Pine64 PineTab - CRUST EDITION"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
makedepends=('bc' 'python' 'swig' 'dtc')
backup=('boot/extlinux/extlinux.conf')
install=${pkgname}.install
_commit_uboot="f0b298c504daae9da171bac950c036432cc00201"
_commit_atf="a4b8b9dba3a32013d9eac6dc1517ea25386568f7"

source=("u-boot-$_commit_uboot.tar.gz::https://gitlab.com/pine64-org/u-boot/-/archive/${_commit_uboot}/u-boot-${_commit_uboot}.tar.gz"
        "arm-trusted-firmware-$_commit_atf.tar.gz::https://github.com/crust-firmware/arm-trusted-firmware/archive/${_commit_atf}.tar.gz"
        'fix-atf-bug.patch'
        '6e19a238bc4743964029a8ff36b878d3295e7a28.patch'
        'scp.bin'
        'extlinux.conf')
md5sums=('c04fe9cb85b9e5f6dc20eed79e0f6149'
         '0bfdff354fe2ac22be638718f92f4752'
         'e2fb72a6a47dc3876585dc674935e9d0'
         'c3191f1cc90b12d482fd48b1e134a34c'
         'ccd556736d2e7cbabd458f7c339c95c7'
         '8bf6e72352f58d0b7cbb5b5c15ae0a0a')

prepare() {
  cd arm-trusted-firmware-${_commit_atf}

  # This patch is reverted to fix touch screen
  patch -N -p1 -R <../6e19a238bc4743964029a8ff36b878d3295e7a28.patch

  cd ../u-boot-${_commit_uboot}
  patch -N -p1 < ../fix-atf-bug.patch

  cp ../scp.bin .
}

build() {
  unset CFLAGS CXXFLAGS CPPFLAGS LDFLAGS

  cd arm-trusted-firmware-${_commit_atf}

  make PLAT=sun50i_a64 DEBUG=1 bl31
  cp build/sun50i_a64/debug/bl31.bin ../u-boot-${_commit_uboot}

  cd ../u-boot-${_commit_uboot}

  # This builds the PineTab uboot.
  echo "Building U-Boot for PineTab..."
  make pinephone_defconfig
  echo 'CONFIG_IDENT_STRING=" Manjaro Linux ARM"' >> .config
  echo 'CONFIG_SERIAL_PRESENT=y' >> .config
  echo 'CONFIG_GZIP=y' >> .config
  echo 'CONFIG_CMD_UNZIP=y' >> .config
  echo 'CONFIG_CMD_EXT4=y' >> .config
  echo 'CONFIG_SUPPORT_RAW_INITRD=y' >> .config
  echo 'CONFIG_CMD_EXT4_WRITE=n' >> .config
  echo 'CONFIG_EXT4_WRITE=n' >> .config
  make EXTRAVERSION=-${pkgrel}
  cat spl/sunxi-spl.bin u-boot.itb > u-boot-sunxi-with-spl-pinetab.bin
}   

package() {
  cd u-boot-${_commit_uboot}

  mkdir -p "${pkgdir}"/boot/extlinux

  cp u-boot-sunxi-with-spl-pinetab.bin "${pkgdir}"/boot
    cp "${srcdir}"/extlinux.conf "${pkgdir}"/boot/extlinux/
 }
