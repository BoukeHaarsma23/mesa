# Maintainer: Jan de Groot <jgc@archlinux.org>
# Maintainer: Andreas Radke <andyrtr@archlinux.org>

pkgbase=mesa
pkgname=('mesa' 'libgl' 'osmesa' 'libglapi' 'libgbm' 'libgles' 'libegl' 'khrplatform-devel' 'ati-dri' 'intel-dri' 'svga-dri' 'nouveau-dri')

_git=true
_gitdate=20120907
#_git=false

if [ "${_git}" = "true" ]; then
    pkgver=8.99.git_$_gitdate
  else
    pkgver=8.0.4
fi
pkgrel=1
arch=('i686' 'x86_64')
makedepends=('glproto>=1.4.16' 'libdrm>=2.4.39' 'libxxf86vm>=1.1.2' 'libxdamage>=1.1.3' 'expat>=2.1.0' 'libx11>=1.5.0' 'libxt>=1.1.3' 
             'gcc-libs>=4.7.1-6' 'dri2proto>=2.8' 'python2' 'libxml2' 'imake' 'llvm' 'systemd' 'libvdpau>=0.5')
url="http://mesa3d.sourceforge.net"
license=('custom')
source=(LICENSE
        mesa-radeon-parallel-make.diff)
if [ "${_git}" = "true" ]; then
	# mesa git shot from 9.0 branch - see for state: http://cgit.freedesktop.org/mesa/mesa/log/?h=9.0
	#source=(${source[@]} 'ftp://ftp.archlinux.org/other/mesa/mesa-41d14eaf193c6b1eb87fe1998808a887f1c6c698.tar.gz')
	source=(${source[@]} "MesaLib-git${_gitdate}.zip"::"http://cgit.freedesktop.org/mesa/mesa/snapshot/mesa-41d14eaf193c6b1eb87fe1998808a887f1c6c698.tar.gz")
  else
	source=(${source[@]} "ftp://ftp.freedesktop.org/pub/mesa/${pkgver}/MesaLib-${pkgver}.tar.bz2"
	#source=(${source[@]} "ftp://ftp.freedesktop.org/pub/mesa/8.0/MesaLib-8.0-rc2.tar.bz2"
)
fi
md5sums=('5c65a0fe315dd347e09b1f2826a1df5a'
         '5dca6edbe6b636c59c2e64f77271f12a'
         '0cb1a5fe39b1766eb72fa5f77553241d')

build() {
    cd ${srcdir}/?esa-*

    # fix parallel build - http://cgit.freedesktop.org/mesa/mesa/commit/src/gallium/drivers/radeon/Makefile?id=cebbdd4ac23725963207bf6f8fc7101150e6065f
    # and the next commit - http://cgit.freedesktop.org/mesa/mesa/commit/?id=2baaa5c7eb21517f0197bfd91154e9b4886fbb1b
    patch -Np1 -i ${srcdir}/mesa-radeon-parallel-make.diff

    COMMONOPTS="--prefix=/usr \
    --with-dri-driverdir=/usr/lib/xorg/modules/dri \
    --with-gallium-drivers=r300,r600,radeonsi,nouveau,svga,swrast \
    --with-dri-drivers=i915,i965,r200,radeon,swrast \
    --enable-gallium-llvm \
    --enable-egl \
    --enable-gallium-egl \
    --with-egl-platforms=x11,drm \
    --enable-shared-glapi \
    --enable-gbm \
    --enable-glx-tls \
    --enable-dri \
    --enable-glx \
    --enable-osmesa \
    --enable-gles1 \
    --enable-gles2 \
    --enable-texture-float \
    --enable-xa \
    --enable-vdpau"

if [ "${_git}" = "true" ]; then
    ./autogen.sh \
      $COMMONOPTS
  else
    autoreconf -vfi
    ./configure \
      $COMMONOPTS
fi

    make
}

package_libgl() {
  depends=('libdrm>=2.4.39' 'libxxf86vm>=1.1.2' 'libxdamage>=1.1.3' 'expat>=2.1.0' 'libglapi' 'gcc-libs')
  pkgdesc="Mesa 3-D graphics library and DRI software rasterizer"

  cd ${srcdir}/?esa-*
  install -m755 -d "${pkgdir}/usr/lib"
  install -m755 -d "${pkgdir}/usr/lib/xorg/modules/extensions"

  bin/minstall lib/libGL.so* "${pkgdir}/usr/lib/"
#  bin/minstall lib/libdricore.so* "${pkgdir}/usr/lib/"
#  bin/minstall lib/libglsl.so* "${pkgdir}/usr/lib/"

  cd src/mesa/drivers/dri
  make -C ${srcdir}/?esa-*/src/gallium/targets/dri-swrast DESTDIR="${pkgdir}" install

  ln -s libglx.xorg "${pkgdir}/usr/lib/xorg/modules/extensions/libglx.so"

  install -m755 -d "${pkgdir}/usr/share/licenses/libgl"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/libgl/"
}

package_osmesa() {
  depends=('mesa')
  optdepends=('opengl-man-pages: for the OpenGL API man pages')
  pkgdesc="Mesa 3D off-screen rendering library"
  
  make -C ${srcdir}/?esa-*/src/mesa DESTDIR="${pkgdir}" install-osmesa
}

package_libglapi() {
  depends=('glibc')
  pkgdesc="free implementation of the GL API -- shared library. The Mesa GL API module is responsible for dispatching all the gl* functions"

  cd ${srcdir}/?esa-*   
  install -m755 -d "${pkgdir}/usr/lib"
  bin/minstall lib/libglapi.so* "${pkgdir}/usr/lib/"

  install -m755 -d "${pkgdir}/usr/share/licenses/libglapi"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/libglapi/"
}

package_libgbm() {
  depends=('systemd-tools')
  pkgdesc="Mesa gbm library"

  cd ${srcdir}/?esa-*   
  install -m755 -d ${pkgdir}/usr/{include,lib/gbm}
  bin/minstall lib/libgbm.so* "${pkgdir}/usr/lib/"
  bin/minstall src/gbm/main/gbm.h "${pkgdir}/usr/include/"
  bin/minstall lib/libgbm.so* "${pkgdir}/usr/lib/"
  bin/minstall lib/gbm/gbm_gallium_drm.so* "${pkgdir}/usr/lib/gbm/"
  install -m755 -d "${pkgdir}/usr/lib/pkgconfig"
  bin/minstall src/gbm/main/gbm.pc "${pkgdir}/usr/lib/pkgconfig/"

  install -m755 -d "${pkgdir}/usr/share/licenses/libgbm"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/libgbm/"
}

package_libgles() {
  depends=('libglapi' 'khrplatform-devel')
  pkgdesc="Mesa GLES libraries and headers"

  cd ${srcdir}/?esa-*   
  install -m755 -d "${pkgdir}/usr/lib"
  install -m755 -d "${pkgdir}/usr/lib/pkgconfig"
  install -m755 -d "${pkgdir}/usr/include"
  install -m755 -d "${pkgdir}/usr/include/GLES"
  install -m755 -d "${pkgdir}/usr/include/GLES2"
  bin/minstall lib/libGLESv* "${pkgdir}/usr/lib/"
  bin/minstall src/mapi/es1api/glesv1_cm.pc "${pkgdir}/usr/lib/pkgconfig/"
  bin/minstall src/mapi/es2api/glesv2.pc "${pkgdir}/usr/lib/pkgconfig/"
  bin/minstall include/GLES/* "${pkgdir}/usr/include/GLES/"
  bin/minstall include/GLES2/* "${pkgdir}/usr/include/GLES2/"
  bin/minstall include/GLES2/* "${pkgdir}/usr/include/GLES2/"

  install -m755 -d "${pkgdir}/usr/share/licenses/libgles"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/libgles/"
}

package_libegl() {
  depends=('libglapi' 'libdrm' 'libxext' 'libxfixes' 'libgbm' 'khrplatform-devel')
  pkgdesc="Mesa EGL libraries and headers"

  cd ${srcdir}/?esa-*   
  make -C src/gallium/targets/egl-static DESTDIR="${pkgdir}" install
  install -m755 -d "${pkgdir}/usr/lib"
  install -m755 -d "${pkgdir}/usr/lib/pkgconfig"
  install -m755 -d "${pkgdir}/usr/include"
  install -m755 -d "${pkgdir}/usr/include/"
  install -m755 -d "${pkgdir}/usr/include/EGL"
  install -m755 -d "${pkgdir}/usr/share"
  install -m755 -d "${pkgdir}/usr/share/doc"
  install -m755 -d "${pkgdir}/usr/share/doc/libegl"
  bin/minstall lib/libEGL.so* "${pkgdir}/usr/lib/"
  install -m755 -d "${pkgdir}/usr/lib/egl"
  bin/minstall lib/egl/* "${pkgdir}/usr/lib/egl/"
  bin/minstall src/egl/main/egl.pc "${pkgdir}/usr/lib/pkgconfig/"
  bin/minstall include/EGL/* "${pkgdir}/usr/include/EGL/"
  bin/minstall docs/egl.html "${pkgdir}/usr/share/doc/libegl/"

  install -m755 -d "${pkgdir}/usr/share/licenses/libegl"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/libegl/"
}

package_khrplatform-devel() {
  #depends=('')
  pkgdesc="Khronos platform development package"

  cd ${srcdir}/?esa-*
  install -m755 -d "${pkgdir}/usr/include/KHR"
  bin/minstall include/KHR/khrplatform.h "${pkgdir}/usr/include/KHR/" 

  install -m755 -d "${pkgdir}/usr/share/licenses/khrplatform-devel"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/khrplatform-devel/"
}

package_mesa() {
  depends=('libgl' 'libx11>=1.5.0' 'libxt>=1.1.3' 'gcc-libs>=4.7.1-6' 'dri2proto>=2.8' 'glproto>=1.4.16') #dri2proto + glproto needed for gl.pc
  optdepends=('opengl-man-pages: for the OpenGL API man pages')
  pkgdesc="Mesa 3-D graphics libraries and include files"

  cd ${srcdir}/?esa-*   
  make DESTDIR="${pkgdir}" install

  rm -f "${pkgdir}/usr/lib/libGL.so"*
  rm -f "${pkgdir}/usr/lib/libglapi.so"*
  rm -f "${pkgdir}/usr/lib/libgbm.so"*
  rm -f "${pkgdir}/usr/lib/libGLESv"*
  rm -f "${pkgdir}/usr/lib/libEGL"*
  rm -rf "${pkgdir}/usr/lib/egl"
  rm -f "${pkgdir}/usr/lib/libOSMesa"*
  rm -rf "${pkgdir}/usr/lib/gbm"
  rm -f ${pkgdir}/usr/lib/pkgconfig/{glesv1_cm.pc,glesv2.pc,egl.pc,osmesa.pc,gbm.pc}
  rm -rf "${pkgdir}/usr/lib/xorg"
  rm -f "${pkgdir}/usr/include/GL/glew.h"
  rm -f "${pkgdir}/usr/include/GL/glxew.h"
  rm -f "${pkgdir}/usr/include/GL/wglew.h"
  rm -f "${pkgdir}/usr/include/GL/glut.h"
  rm -f "${pkgdir}/usr/include/gbm.h"
  rm -rf ${pkgdir}/usr/include/{GLES,GLES2,EGL,KHR}

  install -m755 -d "${pkgdir}/usr/share/licenses/mesa"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/mesa/"
}

package_ati-dri() {
  depends=("libgl=${pkgver}" 'libffi')
  pkgdesc="Mesa DRI radeon/r200 + Gallium3D r300,r600 drivers for AMD/ATI Radeon"
  conflicts=('xf86-video-ati<6.9.0-6')

  # classic mesa drivers for radeon,r200
  make -C ${srcdir}/?esa-*/src/mesa/drivers/dri/radeon DESTDIR="${pkgdir}" install
  make -C ${srcdir}/?esa-*/src/mesa/drivers/dri/r200 DESTDIR="${pkgdir}" install
  # gallium3D driver for r300,r600
  make -C ${srcdir}/?esa-*/src/gallium/targets/dri-r300 DESTDIR="${pkgdir}" install
  make -C ${srcdir}/?esa-*/src/gallium/targets/dri-r600 DESTDIR="${pkgdir}" install
  # gallium egl drivers
  cd ${srcdir}/?esa-*
  install -m755 -d "${pkgdir}/usr/lib/gbm"
  bin/minstall lib/gbm/pipe_r{300,600}.so* "${pkgdir}/usr/lib/gbm/"

}

package_intel-dri() {
  depends=("libgl=${pkgver}")
  pkgdesc="Mesa DRI drivers for Intel"

  make -C ${srcdir}/?esa-*/src/mesa/drivers/dri/i915 DESTDIR="${pkgdir}" install
  make -C ${srcdir}/?esa-*/src/mesa/drivers/dri/i965 DESTDIR="${pkgdir}" install
}

package_nouveau-dri() {
  depends=("libgl=${pkgver}")
  pkgdesc="Mesa classic DRI + Gallium3D drivers for Nouveau"

  # classic mesa driver for nv10 , nv20 nouveau_vieux_dri.so
  make -C ${srcdir}/?esa-*/src/mesa/drivers/dri/nouveau DESTDIR="${pkgdir}" install
  # gallium3D driver for nv30 - nv40 - nv50 nouveau_dri.so
  make -C ${srcdir}/?esa-*/src/gallium/targets/dri-nouveau DESTDIR="${pkgdir}" install
}

package_svga-dri() {
  depends=("mesa=${pkgver}" 'libffi')
  pkgdesc="Gallium3D VMware guest GL driver"

  make -C ${srcdir}/?esa-*/src/gallium/targets/dri-vmwgfx DESTDIR="${pkgdir}" install
  # gallium egl driver
  cd ${srcdir}/?esa-*
  install -m755 -d "${pkgdir}/usr/lib/gbm"
  bin/minstall lib/gbm/pipe_vmwgfx.so* "${pkgdir}/usr/lib/gbm/"
}
