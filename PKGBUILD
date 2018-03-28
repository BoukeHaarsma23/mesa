# Maintainer: Jan de Groot <jgc@archlinux.org>
# Maintainer: Andreas Radke <andyrtr@archlinux.org>

pkgbase=mesa
pkgname=('opencl-mesa' 'vulkan-intel' 'vulkan-radeon' 'libva-mesa-driver' 'mesa-vdpau' 'mesa')
pkgdesc="an open-source implementation of the OpenGL specification"
pkgver=18.0.0
pkgrel=2
arch=('x86_64')
makedepends=('python2-mako' 'libxml2' 'libx11' 'glproto' 'libdrm' 'dri2proto' 'dri3proto' 'presentproto' 
             'libxshmfence' 'libxxf86vm' 'libxdamage' 'libvdpau' 'libva' 'wayland' 'wayland-protocols'
             'elfutils' 'llvm' 'libomxil-bellagio' 'libclc' 'clang' 'libglvnd' 'libunwind' 'lm_sensors' 'meson')
url="https://www.mesa3d.org/"
license=('custom')
source=(https://mesa.freedesktop.org/archive/mesa-${pkgver}.tar.xz{,.sig}
        LICENSE
        0001-glvnd-fix-gl-dot-pc.patch
        fix-install.diff
        fix-versions.diff)
sha512sums=('1494bb09357896a2505b3dcfec772268e28c765804f21e144948a314f1d79d99ff9f21062ef5860eb5a5a568b305a9c954585924a7ac8890fe1ebd8df3bcc396'
            'SKIP'
            '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
            '75849eca72ca9d01c648d5ea4f6371f1b8737ca35b14be179e14c73cc51dca0739c333343cdc228a6d464135f4791bcdc21734e2debecd29d57023c8c088b028'
            'da32ac3b025282c584bfc962723151b6e11887e59e35086c616a987cb3a471051d60f2b303a91f37106ebb75621cbd9b3f560036f5beb88518cfe9d75c45ee03'
            '7ab28153780c5fa0f443873a627201e7d434991e5ce18ef2a05f678aa4561e172b2ad9a56fb01b72a8482bba650e26a08df10b4d4d6bb696c523f73196a47e39')
validpgpkeys=('8703B6700E7EE06D7A39B8D6EDAE37B02CEB490D') # Emil Velikov <emil.l.velikov@gmail.com>
validpgpkeys+=('946D09B5E4C9845E63075FF1D961C596A7203456') # Andres Gomez <tanty@igalia.com>
validpgpkeys+=('E3E8F480C52ADD73B278EE78E1ECBE07D7D70895') # Juan Antonio Suárez Romero (Igalia, S.L.) <jasuarez@igalia.com>"
 
prepare() {
  cd ${srcdir}/mesa-${pkgver}

  # glvnd support patches - from Fedora
  # non-upstreamed ones
  patch -Np1 -i ../0001-glvnd-fix-gl-dot-pc.patch

  # fix symlinks created for vdpau drivers to be relative
  # this is upstreamable
  patch -Np1 -i ../fix-install.diff

  # fix library versioning
  # this is upstreamable
  patch -Np1 -i ../fix-versions.diff
  
#  autoreconf -fiv
}

build() {
#  cd ${srcdir}/mesa-${pkgver}

 # ./configure --prefix=/usr \
 #   --sysconfdir=/etc \
 #   --with-gallium-drivers=r300,r600,radeonsi,nouveau,svga,swrast,virgl,swr \
 #   --with-dri-drivers=i915,i965,r200,radeon,nouveau,swrast \
 #   --with-platforms=x11,drm,wayland \
 #   --with-vulkan-drivers=intel,radeon \
 #   --disable-xvmc \
 #   --enable-llvm \
 #   --enable-llvm-shared-libs \
 #   --enable-shared-glapi \
 #   --enable-libglvnd \
 #   --enable-libunwind \
 #   --enable-lmsensors \
 #   --enable-egl \
 #   --enable-glx \
 #   --enable-glx-tls \
 #   --enable-gles1 \
 #   --enable-gles2 \
 #   --enable-gbm \
 #   --enable-dri \
 #   --enable-gallium-osmesa \
 #   --enable-gallium-extra-hud \
 #   --enable-texture-float \
 #   --enable-xa \
 #   --enable-vdpau \
 #   --enable-omx-bellagio \
 #   --enable-nine \
 #   --enable-opencl \
 #   --enable-opencl-icd \
 #   --with-clang-libdir=/usr/lib

 # make

  arch-meson mesa-$pkgver build \
    -D b_lto=false \
    -D platforms=x11,wayland,drm,surfaceless \
    -D dri-drivers=i915,i965,r100,r200,nouveau \
    -D gallium-drivers=r300,r600,radeonsi,nouveau,virgl,svga,swrast,swr \
    -D vulkan-drivers=amd,intel \
    -D swr-arches=avx,avx2 \
    -D dri3=true \
    -D egl=true \
    -D gallium-extra-hud=true \
    -D gallium-nine=true \
    -D gallium-omx=true \
    -D gallium-opencl=icd \
    -D gallium-va=true \
    -D gallium-vdpau=true \
    -D gallium-xa=true \
    -D gallium-xvmc=false \
    -D gbm=true \
    -D gles1=true \
    -D gles2=true \
    -D glvnd=true \
    -D glx=dri \
    -D libunwind=true \
    -D llvm=true \
    -D lmsensors=true \
    -D osmesa=gallium \
    -D shared-glapi=true \
    -D texture-float=true \
    -D valgrind=false

  ninja -C build

  # fake installation
  mkdir $srcdir/fakeinstall
#  make DESTDIR=${srcdir}/fakeinstall install
  DESTDIR=${srcdir}/fakeinstall ninja -C build install
}

package_opencl-mesa() {
  pkgdesc="OpenCL support for AMD/ATI Radeon mesa drivers"
  depends=('expat' 'libdrm' 'libelf' 'libclc' 'clang')
  optdepends=('opencl-headers: headers necessary for OpenCL development')
  provides=('opencl-driver')

  install -m755 -d ${pkgdir}/etc
  mv -v ${srcdir}/fakeinstall/etc/OpenCL ${pkgdir}/etc/
  
  install -m755 -d ${pkgdir}/usr/lib
  mv -v ${srcdir}/fakeinstall/usr/lib/lib*OpenCL* ${pkgdir}/usr/lib/
  mv -v ${srcdir}/fakeinstall/usr/lib/gallium-pipe ${pkgdir}/usr/lib/

  install -m755 -d "${pkgdir}/usr/share/licenses/opencl-mesa"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/opencl-mesa/"
}

package_vulkan-intel() {
  pkgdesc="Intel's Vulkan mesa driver"
  depends=('wayland' 'libx11' 'libxshmfence' 'libdrm')
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

  install -m755 -d ${pkgdir}/usr/lib/dri
  mv -v ${srcdir}/fakeinstall/usr/lib/dri/*_drv_video.so ${pkgdir}/usr/lib/dri
   
  install -m755 -d "${pkgdir}/usr/share/licenses/libva-mesa-driver"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/libva-mesa-driver/"
}

package_mesa-vdpau() {
  pkgdesc="Mesa VDPAU drivers"
  depends=('libdrm' 'libx11' 'llvm-libs' 'expat' 'libelf' 'libxshmfence')

  install -m755 -d ${pkgdir}/usr/lib
  mv -v ${srcdir}/fakeinstall/usr/lib/vdpau ${pkgdir}/usr/lib
   
  install -m755 -d "${pkgdir}/usr/share/licenses/mesa-vdpau"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/mesa-vdpau/"
}

package_mesa() {
  depends=('libdrm' 'wayland' 'libxxf86vm' 'libxdamage' 'libxshmfence' 'libelf' 
           'libomxil-bellagio' 'libunwind' 'llvm-libs' 'lm_sensors' 'libglvnd')
  optdepends=('opengl-man-pages: for the OpenGL API man pages'
              'mesa-vdpau: for accelerated video playback'
              'libva-mesa-driver: for accelerated video playback')
  provides=('ati-dri' 'intel-dri' 'nouveau-dri' 'svga-dri' 'mesa-dri' 'mesa-libgl' 'opengl-driver')
  conflicts=('ati-dri' 'intel-dri' 'nouveau-dri' 'svga-dri' 'mesa-dri' 'mesa-libgl')
  replaces=('ati-dri' 'intel-dri' 'nouveau-dri' 'svga-dri' 'mesa-dri' 'mesa-libgl')
  backup=('etc/drirc')

  install -m755 -d ${pkgdir}/etc
  mv -v ${srcdir}/fakeinstall/etc/drirc ${pkgdir}/etc
  
  install -m755 -d ${pkgdir}/usr/share/glvnd/egl_vendor.d
  mv -v ${srcdir}/fakeinstall/usr/share/glvnd/egl_vendor.d/50_mesa.json ${pkgdir}/usr/share/glvnd/egl_vendor.d/

  install -m755 -d ${pkgdir}/usr/lib/dri
  # ati-dri, nouveau-dri, intel-dri, svga-dri, swrast
  mv -v ${srcdir}/fakeinstall/usr/lib/dri/*_dri.so ${pkgdir}/usr/lib/dri
   
  mv -v ${srcdir}/fakeinstall/usr/lib/bellagio  ${pkgdir}/usr/lib
  mv -v ${srcdir}/fakeinstall/usr/lib/d3d  ${pkgdir}/usr/lib
  mv -v ${srcdir}/fakeinstall/usr/lib/lib{gbm,glapi}.so* ${pkgdir}/usr/lib/
  mv -v ${srcdir}/fakeinstall/usr/lib/libOSMesa.so* ${pkgdir}/usr/lib/
  mv -v ${srcdir}/fakeinstall/usr/lib/libwayland*.so* ${pkgdir}/usr/lib/
  mv -v ${srcdir}/fakeinstall/usr/lib/libxatracker.so* ${pkgdir}/usr/lib/
  mv -v ${srcdir}/fakeinstall/usr/lib/libswrAVX*.so* ${pkgdir}/usr/lib/

  # in libglvnd
  rm -v ${srcdir}/fakeinstall/usr/lib/libGLESv{1_CM,2}.so*

  mv -v ${srcdir}/fakeinstall/usr/include ${pkgdir}/usr
  mv -v ${srcdir}/fakeinstall/usr/lib/pkgconfig ${pkgdir}/usr/lib/
  
  # remove vulkan headers
  rm -r ${pkgdir}/usr/include/vulkan

  # libglvnd support
  mv -v ${srcdir}/fakeinstall/usr/lib/libGLX_mesa.so* ${pkgdir}/usr/lib/
  mv -v ${srcdir}/fakeinstall/usr/lib/libEGL_mesa.so* ${pkgdir}/usr/lib/
  # indirect rendering
  ln -s /usr/lib/libGLX_mesa.so.0 ${pkgdir}/usr/lib/libGLX_indirect.so.0

  # no files should be left: safety check
  find "${srcdir}/fakeinstall" -depth -print0 | xargs -0 rmdir

  install -m755 -d "${pkgdir}/usr/share/licenses/mesa"
  install -m644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/mesa/"
}
