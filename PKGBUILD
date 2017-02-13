# Maintainer: Jan de Groot <jgc@archlinux.org>
# Maintainer: Andreas Radke <andyrtr@archlinux.org>

pkgbase=mesa
pkgname=('opencl-mesa' 'vulkan-intel' 'vulkan-radeon' 'libva-mesa-driver' 'mesa-vdpau' 'mesa') # 'mesa-libgl')
pkgver=17.0.0
pkgrel=2
arch=('i686' 'x86_64')
makedepends=('python2-mako' 'libxml2' 'libx11' 'glproto' 'libdrm' 'dri2proto' 'dri3proto' 'presentproto' 
             'libxshmfence' 'libxxf86vm' 'libxdamage' 'libvdpau' 'libva' 'wayland' 'elfutils' 'llvm'
             'libomxil-bellagio' 'libclc' 'clang' 'libglvnd')
url="http://mesa3d.sourceforge.net"
license=('custom')
source=(https://mesa.freedesktop.org/archive/mesa-${pkgver}.tar.xz{,.sig}
        LICENSE
        remove-libpthread-stubs.patch
        0001-EGL-Implement-the-libglvnd-interface-for-EGL-v2.patch
        0001-Fix-linkage-against-shared-glapi.patch
        0001-glapi-Link-with-glapi-when-built-shared.patch
        0001-glx-glvnd-Fix-GLXdispatchIndex-sorting.patch
        0002-fixup-EGL-Implement-the-libglvnd-interface-for-EGL-v.patch
        glvnd-fix-gl-dot-pc.patch)
sha256sums=('39db3d59700159add7f977307d12a7dfe016363e760ad82280ac4168ea668481'
            'SKIP'
            '7fdc119cf53c8ca65396ea73f6d10af641ba41ea1dd2bd44a824726e01c8b3f2'
            '75ab53ad44b95204c788a2988e97a5cb963bdbf6072a5466949a2afb79821c8f'
            '1d3475dc2f4f3e450cf313130d3ce965f933f396058828fa843c0df8115feeb9'
            'c68d1522f9bce4ce31c92aa7a688da49f13043f5bb2254795b76dea8f47130b7'
            '064dcd5a3ab1b7c23383e2cafbd37859e4c353f8839671d9695c6f7c2ef3260b'
            'a6772820a582037ea4583f4c4abe6e406cdbd66361f9c7c76f10d6bb75a11a66'
            '81d0ced62f61677ea0cf5f69a491093409fa1370f2ef045c41106ca8bf9c46f6'
            '64a77944a28026b066c1682c7258d02289d257b24b6f173a9f7580c48beed966')
validpgpkeys=('8703B6700E7EE06D7A39B8D6EDAE37B02CEB490D') # Emil Velikov <emil.l.velikov@gmail.com>

prepare() {
  cd ${srcdir}/mesa-${pkgver}

  # Now mesa checks for libpthread-stubs - so remove the check
  patch -Np1 -i ../remove-libpthread-stubs.patch
  
  # glvnd support patches - from Fedora
  # https://patchwork.freedesktop.org/series/12354/, v3 & v4
  patch -Np1 -i ../0001-EGL-Implement-the-libglvnd-interface-for-EGL-v2.patch
  patch -Np1 -i ../0002-fixup-EGL-Implement-the-libglvnd-interface-for-EGL-v.patch
  # non-upstreamed ones
  patch -Np1 -i ../glvnd-fix-gl-dot-pc.patch
  patch -Np1 -i ../0001-Fix-linkage-against-shared-glapi.patch
  patch -Np1 -i ../0001-glapi-Link-with-glapi-when-built-shared.patch
  # submitted upstream
  patch -Np1 -i ../0001-glx-glvnd-Fix-GLXdispatchIndex-sorting.patch

  autoreconf -fiv
}

build() {
  cd ${srcdir}/mesa-${pkgver}

  ./configure --prefix=/usr \
    --sysconfdir=/etc \
    --with-dri-driverdir=/usr/lib/xorg/modules/dri \
    --with-gallium-drivers=r300,r600,radeonsi,nouveau,svga,swrast,virgl \
    --with-dri-drivers=i915,i965,r200,radeon,nouveau,swrast \
    --with-egl-platforms=x11,drm,wayland \
    --with-vulkan-drivers=intel,radeon \
    --disable-xvmc \
    --enable-gallium-llvm \
    --enable-llvm-shared-libs \
    --enable-shared-glapi \
    --enable-libglvnd \
    --enable-egl \
    --enable-glx \
    --enable-glx-tls \
    --enable-gles1 \
    --enable-gles2 \
    --enable-gbm \
    --enable-dri \
    --enable-osmesa \
    --enable-texture-float \
    --enable-xa \
    --enable-vdpau \
    --enable-omx \
    --enable-nine \
    --enable-opencl \
    --enable-opencl-icd \
    --with-clang-libdir=/usr/lib
    #--with-sha1=libgcrypt \

  make

  # fake installation
  mkdir $srcdir/fakeinstall
  make DESTDIR=${srcdir}/fakeinstall install
}

package_opencl-mesa() {
  pkgdesc="OpenCL support for AMD/ATI Radeon mesa drivers"
  depends=('expat' 'libdrm' 'libelf' 'libclc' 'clang')
  optdepends=('opencl-headers: headers necessary for OpenCL development')
  provides=('opencl-driver')

  install -m755 -d ${pkgdir}/etc
  cp -rv ${srcdir}/fakeinstall/etc/OpenCL ${pkgdir}/etc/
  
  install -m755 -d ${pkgdir}/usr/lib/gallium-pipe
  cp -rv ${srcdir}/fakeinstall/usr/lib/lib*OpenCL* ${pkgdir}/usr/lib/
  cp -rv ${srcdir}/fakeinstall/usr/lib/gallium-pipe/pipe_{r600,radeonsi}.so ${pkgdir}/usr/lib/gallium-pipe/

  install -m755 -d "${pkgdir}/usr/share/licenses/opencl-mesa"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/opencl-mesa/"
}

package_vulkan-intel() {
  pkgdesc="Intel's Vulkan mesa driver"
  depends=('wayland' 'libx11' 'libxshmfence')
  provides=('vulkan-driver')

  install -m755 -d ${pkgdir}/usr/share/vulkan/icd.d
  mv -v ${srcdir}/fakeinstall/usr/share/vulkan/icd.d/intel_icd*.json ${pkgdir}/usr/share/vulkan/icd.d/

  install -m755 -d ${pkgdir}/usr/{include/vulkan,lib}
  mv -v ${srcdir}/fakeinstall/usr/lib/libvulkan_intel.so ${pkgdir}/usr/lib/
  mv -v ${srcdir}/fakeinstall/usr/include/vulkan/vulkan_intel.h ${pkgdir}/usr/include/vulkan

  install -m755 -d "${pkgdir}/usr/share/licenses/vulkan-intel"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/vulkan-intel/"
}

package_vulkan-radeon() {
  pkgdesc="Radeon's Vulkan mesa driver"
  depends=('wayland' 'libx11' 'libxshmfence' 'libelf' 'libdrm' 'llvm-libs')
  provides=('vulkan-driver')
 
  install -m755 -d ${pkgdir}/usr/share/vulkan/icd.d
  mv -v ${srcdir}/fakeinstall/usr/share/vulkan/icd.d/radeon_icd*.json ${pkgdir}/usr/share/vulkan/icd.d/

  install -m755 -d ${pkgdir}/usr/lib
  mv -v ${srcdir}/fakeinstall/usr/lib/libvulkan_radeon.so ${pkgdir}/usr/lib/

  install -m755 -d "${pkgdir}/usr/share/licenses/vulkan-radeon"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/vulkan-radeon/"
}

package_libva-mesa-driver() {
  pkgdesc="VA-API implementation for gallium"
  depends=('libdrm' 'libx11' 'llvm-libs' 'expat' 'libelf' 'libxshmfence')

  install -m755 -d ${pkgdir}/usr/lib
  cp -rv ${srcdir}/fakeinstall/usr/lib/dri ${pkgdir}/usr/lib
   
  install -m755 -d "${pkgdir}/usr/share/licenses/libva-mesa-driver"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/libva-mesa-driver/"
}

package_mesa-vdpau() {
  pkgdesc="Mesa VDPAU drivers"
  depends=('libdrm' 'libx11' 'llvm-libs' 'expat' 'libelf' 'libxshmfence')

  install -m755 -d ${pkgdir}/usr/lib
  cp -rv ${srcdir}/fakeinstall/usr/lib/vdpau ${pkgdir}/usr/lib
   
  install -m755 -d "${pkgdir}/usr/share/licenses/mesa-vdpau"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/mesa-vdpau/"
}

package_mesa() {
  pkgdesc="an open-source implementation of the OpenGL specification"
  depends=('libdrm' 'wayland' 'libxxf86vm' 'libxdamage' 'libxshmfence' 'libelf' 
           'libomxil-bellagio' 'libtxc_dxtn' 'llvm-libs' 'libglvnd')
  optdepends=('opengl-man-pages: for the OpenGL API man pages'
              'mesa-vdpau: for accelerated video playback'
              'libva-mesa-driver: for accelerated video playback')
  provides=('ati-dri' 'intel-dri' 'nouveau-dri' 'svga-dri' 'mesa-dri' 'mesa-libgl' 'opengl-driver')
  conflicts=('ati-dri' 'intel-dri' 'nouveau-dri' 'svga-dri' 'mesa-dri' 'mesa-libgl')
  replaces=('ati-dri' 'intel-dri' 'nouveau-dri' 'svga-dri' 'mesa-dri') #'mesa-libgl')

  install -m755 -d ${pkgdir}/etc
  cp -rv ${srcdir}/fakeinstall/etc/drirc ${pkgdir}/etc
  
  install -m755 -d ${pkgdir}/usr/share/glvnd/egl_vendor.d
  cp -rv ${srcdir}/fakeinstall/usr/share/glvnd/egl_vendor.d/50_mesa.json ${pkgdir}/usr/share/glvnd/egl_vendor.d/

  install -m755 -d ${pkgdir}/usr/lib/xorg/modules/dri
  # ati-dri, nouveau-dri, intel-dri, svga-dri, swrast
  cp -av ${srcdir}/fakeinstall/usr/lib/xorg/modules/dri/* ${pkgdir}/usr/lib/xorg/modules/dri
   
  cp -rv ${srcdir}/fakeinstall/usr/lib/bellagio  ${pkgdir}/usr/lib
  cp -rv ${srcdir}/fakeinstall/usr/lib/d3d  ${pkgdir}/usr/lib
  cp -rv ${srcdir}/fakeinstall/usr/lib/lib{gbm,glapi}.so* ${pkgdir}/usr/lib/
  cp -rv ${srcdir}/fakeinstall/usr/lib/libOSMesa.so* ${pkgdir}/usr/lib/
  cp -rv ${srcdir}/fakeinstall/usr/lib/libwayland*.so* ${pkgdir}/usr/lib/
  cp -rv ${srcdir}/fakeinstall/usr/lib/libxatracker.so* ${pkgdir}/usr/lib/

  cp -rv ${srcdir}/fakeinstall/usr/include ${pkgdir}/usr
  cp -rv ${srcdir}/fakeinstall/usr/lib/pkgconfig ${pkgdir}/usr/lib/
  
  # remove vulkan headers
  rm -rf ${pkgdir}/usr/include/vulkan

  #install -m755 -d ${pkgdir}/usr/lib/mesa
  # move libgl/EGL/glesv*.so to not conflict with blobs - may break .pc files ?
  #cp -rv ${srcdir}/fakeinstall/usr/lib/libGL.so* 	${pkgdir}/usr/lib/mesa/
  #cp -rv ${srcdir}/fakeinstall/usr/lib/libEGL.so* 	${pkgdir}/usr/lib/mesa/
  #cp -rv ${srcdir}/fakeinstall/usr/lib/libGLES*.so*	${pkgdir}/usr/lib/mesa/

  # libglvnd support
  cp -rv ${srcdir}/fakeinstall/usr/lib/libGLX_mesa.so* ${pkgdir}/usr/lib/
  cp -rv ${srcdir}/fakeinstall/usr/lib/libEGL_mesa.so* ${pkgdir}/usr/lib/

  install -m755 -d "${pkgdir}/usr/share/licenses/mesa"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/mesa/"
}

# package_mesa-libgl() {
#   pkgdesc="Mesa 3-D graphics library"
#   depends=('mesa')
#   provides=('libgl' 'libgles' 'libegl')
#   conflicts=('libgl' 'libgles' 'libegl')
# 
#   install -m755 -d "${pkgdir}/usr/lib/"
#  
#   ln -s /usr/lib/mesa/libGL.so.1.2.0 ${pkgdir}/usr/lib/libGL.so.1.2.0
#   ln -s libGL.so.1.2.0	             ${pkgdir}/usr/lib/libGL.so.1
#   ln -s libGL.so.1.2.0               ${pkgdir}/usr/lib/libGL.so
# 
#   ln -s /usr/lib/mesa/libEGL.so.1.0.0 ${pkgdir}/usr/lib/libEGL.so.1.0.0
#   ln -s libEGL.so.1.0.0	              ${pkgdir}/usr/lib/libEGL.so.1
#   ln -s libEGL.so.1.0.0               ${pkgdir}/usr/lib/libEGL.so
# 
#   ln -s /usr/lib/mesa/libGLESv1_CM.so.1.1.0 ${pkgdir}/usr/lib/libGLESv1_CM.so.1.1.0
#   ln -s libGLESv1_CM.so.1.1.0	            ${pkgdir}/usr/lib/libGLESv1_CM.so.1
#   ln -s libGLESv1_CM.so.1.1.0               ${pkgdir}/usr/lib/libGLESv1_CM.so
# 
#   ln -s /usr/lib/mesa/libGLESv2.so.2.0.0 ${pkgdir}/usr/lib/libGLESv2.so.2.0.0
#   ln -s libGLESv2.so.2.0.0               ${pkgdir}/usr/lib/libGLESv2.so.2
#   ln -s libGLESv2.so.2.0.0               ${pkgdir}/usr/lib/libGLESv2.so
# 
#   install -m755 -d "${pkgdir}/usr/share/licenses/mesa-libgl"
#   install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/mesa-libgl/"
# }
