# U-Boot: PineTab, based on Pine64 PKGBUILD
# Maintainer: Dan Johansen <strit@manjaro.org>

pkgname=uboot-pinetab
pkgver=2021.10
pkgrel=2
pkgdesc="U-Boot for Pine64 PineTab"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
makedepends=('bc' 'python-setuptools' 'swig' 'dtc')
provides=('uboot')
conflicts=('uboot')
install=${pkgname}.install
_tfaver=2.5
source=("https://ftp.denx.de/pub/u-boot/u-boot-${pkgver/rc/-rc}.tar.bz2"
        "https://git.trustedfirmware.org/TF-A/trusted-firmware-a.git/snapshot/trusted-firmware-a-$_tfaver.tar.gz"
        "pinetab_defconfig")
md5sums=('f1392080facf59dd2c34096a5fd95d4c'
         'cd0455f0dcd4161201074bacb93446b1'
         '0c29da32ba01d2e2567f0aa7b98b6a22')

build() {
  # Avoid build warnings by editing a .config option in place instead of
  # appending an option to .config, if an option is already present
  update_config() {
    if ! grep -q "^$1=$2$" .config; then
      if grep -q "^# $1 is not set$" .config; then
        sed -i -e "s/^# $1 is not set$/$1=$2/g" .config
      elif grep -q "^$1=" .config; then
        sed -i -e "s/^$1=.*/$1=$2/g" .config
      else
        echo "$1=$2" >> .config
      fi
    fi
  }

  unset CFLAGS CXXFLAGS CPPFLAGS LDFLAGS

  cd trusted-firmware-a-${_tfaver}
  
  echo -e "\nBuilding TF-A for Pine64 PineTab...\n"
  make PLAT=sun50i_a64 DEBUG=1 bl31
  cp build/sun50i_a64/debug/bl31.bin ../u-boot-${pkgver/rc/-rc}

  cd ../u-boot-${pkgver/rc/-rc}

  # Copy pinetab_defconfig into uboot source tree
  cp ${srcdir}/pinetab_defconfig configs/pinetab_defconfig

  # This builds the PineTab uboot.
  echo -e "\nBuilding U-Boot for Pine64 PineTab...\n"
  make pinetab_defconfig
  
  update_config 'CONFIG_IDENT_STRING' '" Manjaro Linux ARM"'
  make SCP=/dev/null EXTRAVERSION=-${pkgrel}
  cat u-boot-sunxi-with-spl.bin > u-boot-sunxi-with-spl-pinetab.bin
}

package() {
  cd u-boot-${pkgver/rc/-rc}
  mkdir -p "${pkgdir}"/boot/extlinux
  
  install -D -m 0644 u-boot-sunxi-with-spl-pinetab.bin -t "${pkgdir}"/boot
 }
