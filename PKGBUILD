# Maintainer: Alexander Baldeck <alexander@archlinux.org>
# Contributor: Jan de Groot <jgc@archlinux.org>

pkgname=mesa
pkgver=7.4.1
pkgrel=1
pkgdesc="Mesa OpenGL library"
arch=(i686 x86_64)
license=('LGPL')
url="http://mesa3d.sourceforge.net"
depends=('libgl' 'libx11>=1.2' 'libxt' 'gcc-libs>=4.3.3' 'dri2proto>=2.0' 'libdrm>=2.4.9')
makedepends=('pkgconfig' 'glproto>=1.4.9')
conflicts=('mesa-apps')
replaces=('mesa-apps')
source=(http://downloads.sourceforge.net/mesa3d/MesaLib-${pkgver}.tar.bz2
	http://downloads.sourceforge.net/mesa3d/MesaDemos-${pkgver}.tar.bz2
	ftp://ftp.archlinux.org/other/mesa/gl-manpages-1.0.1.tar.bz2
	7.4-branch.patch)
md5sums=('423260578b653818ba66c2fcbde6d7ad'
         '1e169fb6abc2b45613f1c98a82dfe690'
         '6ae05158e678f4594343f32c2ca50515'
         'ffe0e265ea6207943256b2912601d026')

build() {
  cd "${srcdir}/Mesa-${pkgver}"
  patch -Np1 -i "${srcdir}/7.4-branch.patch" || return 1
  ./configure --prefix=/usr \
    --with-dri-driverdir=/usr/lib/xorg/modules/dri \
    --with-dri-drivers=swrast \
    --enable-glx-tls \
    --with-driver=dri \
    --enable-xcb \
    --disable-glut || return 1
  make || return 1
  make DESTDIR="${pkgdir}" install || return 1

  install -m755 -d "${pkgdir}/usr/bin"
  install -m755 progs/xdemos/glx{gears,info} "${pkgdir}/usr/bin/" || return 1

  rm -f "${pkgdir}/usr/lib/libGL.so"*
  rm -rf "${pkgdir}/usr/lib/xorg"

  cd "${srcdir}/gl-manpages-1.0.1"
  ./configure --prefix=/usr || return 1
  make || return 1
  make DESTDIR="${pkgdir}" install || return 1
}
