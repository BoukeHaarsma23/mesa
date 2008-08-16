# Maintainer: Alexander Baldeck <alexander@archlinux.org>
# Contributor: Jan de Groot <jgc@archlinux.org>

pkgname=mesa
pkgver=7.1rc4
pkgrel=1
pkgdesc="Mesa OpenGL library"
arch=(i686 x86_64)
license=('LGPL')
url="http://mesa3d.sourceforge.net"
depends=('libgl' 'libx11>=1.1.4-3' 'libxt' 'glproto>=1.4.9' 'gcc-libs>=4.3.1')
makedepends=('pkgconfig' 'dri2proto>=1.1')
conflicts=('mesa-apps')
replaces=('mesa-apps')
source=(http://www.mesa3d.org/beta/MesaLib-7.1-rc4.tar.gz
        http://www.mesa3d.org/beta/MesaDemos-7.1-rc4.tar.gz
	ftp://ftp.archlinux.org/other/mesa/gl-manpages-1.0.1.tar.bz2)
md5sums=('5844564b90fdf0a462c8a4e601fa3f78'
         '27308db6536ebc934c56a33b30cc0a24'
         '6ae05158e678f4594343f32c2ca50515')

build() {
  cd ${srcdir}/Mesa-7.1-rc4
  ./configure --prefix=/usr --with-dri-drivers=swrast --enable-glx-tls || return 1
  make || return 1
  make DESTDIR=${pkgdir} install || return 1

  install -m755 -d ${pkgdir}/usr/bin
  install -m755 progs/xdemos/glx{gears,info} ${pkgdir}/usr/bin/ || return 1

  rm -f ${pkgdir}/usr/lib/libGL.so*
  rm -rf ${pkgdir}/usr/lib/dri

  cd ${srcdir}/gl-manpages-1.0.1
  ./configure --prefix=/usr || return 1
  make || return 1
  make DESTDIR=${pkgdir} install || return 1
}
