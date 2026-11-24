pkgbase=linux-zblade
pkgver=7.3.6
pkgrel=1
pkgdesc='Performance-focused Linux kernel for x86_64 Arch systems'
arch=('x86_64')
url='https://www.kernel.org/'
license=('GPL-2.0-only')
makedepends=('bc' 'bison' 'pahole' 'flex' 'gcc' 'kmod' 'libelf' 'make' 'openssl' 'perl' 'python' 'rsync' 'tar' 'xz' 'zstd')
options=('!debug' '!strip')
source=("https://cdn.kernel.org/pub/linux/kernel/v7.x/linux-${pkgver}.tar.xz"
        'config.fragment')
sha256sums=('8183d475a76a0faffa92d6cd500af421f589dfd0300a0f1060bb875e11d45723'
            '79a64bdb159a28f8426bfc81d70efa303931ad3b9da8fb249a5cade93f5ff3d4')

build() {
  cd "${srcdir}/linux-${pkgver}"
  local march=${ZBLADE_MARCH:-x86-64-v3}
  local jobs=${ZBLADE_JOBS:-$(nproc)}
  make O="${srcdir}/build" x86_64_defconfig
  scripts/kconfig/merge_config.sh -O "${srcdir}/build" \
    "${srcdir}/build/.config" "${srcdir}/config.fragment"
  make O="${srcdir}/build" olddefconfig
  make O="${srcdir}/build" LOCALVERSION="-zblade" \
    KBUILD_BUILD_HOST=arch KBUILD_BUILD_USER=builder \
    KCFLAGS="-march=${march}" -j"${jobs}"
}

package_linux-zblade() {
  depends=('coreutils' 'kmod')
  optdepends=('linux-firmware: firmware for many devices')
  provides=('kernel')
  install -d "${pkgdir}/boot" "${pkgdir}/usr/lib/modules"
  make -C "${srcdir}/linux-${pkgver}" O="${srcdir}/build" \
    LOCALVERSION="-zblade" INSTALL_MOD_PATH="${pkgdir}/usr" \
    INSTALL_MOD_STRIP=1 modules_install
  install -m644 "${srcdir}/build/arch/x86/boot/bzImage" \
    "${pkgdir}/boot/vmlinuz-linux-zblade"
  install -m644 "${srcdir}/build/System.map" \
    "${pkgdir}/boot/System.map-linux-zblade"
  install -m644 "${srcdir}/build/.config" \
    "${pkgdir}/usr/lib/modules/$(make -s -C "${srcdir}/linux-${pkgver}" O="${srcdir}/build" LOCALVERSION="-zblade" kernelrelease)/build-config"
}

package_linux-zblade-headers() {
  pkgdesc='Headers and build files for linux-zblade'
  depends=('linux-zblade')
  provides=('linux-headers')
  local kver
  kver=$(make -s -C "${srcdir}/linux-${pkgver}" O="${srcdir}/build" LOCALVERSION="-zblade" kernelrelease)
  install -d "${pkgdir}/usr/lib/modules/${kver}/build"
  cp -a "${srcdir}/build"/. "${pkgdir}/usr/lib/modules/${kver}/build/"
  cp -a "${srcdir}/linux-${pkgver}"/. "${pkgdir}/usr/lib/modules/${kver}/build/"
  rm -rf "${pkgdir}/usr/lib/modules/${kver}/build/.git"
}

pkgname=('linux-zblade' 'linux-zblade-headers')
