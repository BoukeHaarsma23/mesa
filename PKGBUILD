# Maintainer: Alexander Baldeck <alexander@archlinux.org>
# Contributor: Jan de Groot <jgc@archlinux.org>

pkgname=mesa
pkgver=7.5
pkgrel=1
pkgdesc="Mesa OpenGL library"
arch=(i686 x86_64)
license=('LGPL')
url="http://mesa3d.sourceforge.net"
depends=('libgl' 'libx11>=1.2.1' 'libxt' 'gcc-libs>=4.4.0' 'dri2proto=2.1' 'libdrm>=2.4.12')
makedepends=('pkgconfig' 'glproto>=1.4.9')
conflicts=('mesa-apps')
replaces=('mesa-apps')
source=(http://downloads.sourceforge.net/mesa3d/MesaLib-${pkgver}.tar.bz2
        http://downloads.sourceforge.net/mesa3d/MesaDemos-${pkgver}.tar.bz2
        ftp://ftp.archlinux.org/other/mesa/gl-manpages-1.0.1.tar.bz2)
md5sums=('459f332551f6ebb86f384d21dd15e1f0'
         '398ee8801814a00e47f6c2314e3dfddc'
         '6ae05158e678f4594343f32c2ca50515')

build() {
  cd "${srcdir}/Mesa-${pkgver}"
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
