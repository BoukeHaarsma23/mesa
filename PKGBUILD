# Maintainer: Laurent Carlier <lordheavym@gmail.com>
# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>

pkgbase=mesa
pkgname=(
  mesa
  libva-mesa-driver
  mesa-vdpau
  opencl-clover-mesa
  opencl-rusticl-mesa
  vulkan-intel
  vulkan-mesa-layers
  vulkan-nouveau
  vulkan-radeon
  vulkan-swrast
  vulkan-virtio
)
pkgver=24.1.5
pkgrel=1
epoch=1
pkgdesc="Open-source OpenGL drivers"
url="https://www.mesa3d.org/"
arch=(x86_64)
license=("MIT AND BSD-3-Clause AND SGI-B-2.0")
makedepends=(
  clang
  expat
  gcc-libs
  glibc
  libdrm
  libelf
  libglvnd
  libva
  libvdpau
  libx11
  libxcb
  libxext
  libxfixes
  libxml2
  libxrandr
  libxshmfence
  libxxf86vm
  llvm
  llvm-libs
  lm_sensors
  rust
  spirv-llvm-translator
  spirv-tools
  systemd-libs
  vulkan-icd-loader
  wayland
  xcb-util-keysyms
  zlib
  zstd

  # shared between mesa and lib32-mesa
  cbindgen
  clang
  cmake
  elfutils
  glslang
  libclc
  meson
  python-mako
  python-packaging
  python-ply
  rust-bindgen
  wayland-protocols
  xorgproto

  # valgrind deps
  valgrind

  # d3d12 deps
  directx-headers

  # gallium-omx deps
  libomxil-bellagio
)
options=(
  # GCC 14 LTO causes segfault in LLVM under si_llvm_optimize_module
  # https://gitlab.freedesktop.org/mesa/mesa/-/issues/11140
  #
  # In general, upstream considers LTO to be broken until explicit notice.
  !lto
)
source=(
  "https://mesa.freedesktop.org/archive/mesa-$pkgver.tar.xz"{,.sig}
)
validpgpkeys=(
  946D09B5E4C9845E63075FF1D961C596A7203456 # Andres Gomez <tanty@igalia.com>
  71C4B75620BC75708B4BDB254C95FAAB3EB073EC # Dylan Baker <dylan@pnwbakers.com>
  8703B6700E7EE06D7A39B8D6EDAE37B02CEB490D # Emil Velikov <emil.l.velikov@gmail.com>
  57551DE15B968F6341C248F68D8E31AFC32428A6 # Eric Engestrom <eric@engestrom.ch>
  A5CC9FEC93F2F837CB044912336909B6B25FADFA # Juan A. Suarez Romero <jasuarez@igalia.com>
  E3E8F480C52ADD73B278EE78E1ECBE07D7D70895 # Juan Antonio Suárez Romero (Igalia, S.L.) <jasuarez@igalia.com>
)

# Rust crates for NVK, used as Meson subprojects
declare -A _crates=(
   paste          1.0.14
   proc-macro2    1.0.70
   quote          1.0.33
   syn            2.0.39
   unicode-ident  1.0.12
)

for _crate in "${!_crates[@]}"; do
  _ver="${_crates[$_crate]}"
  source+=(
    "$_crate-$_ver.tar.gz::https://crates.io/api/v1/crates/$_crate/$_ver/download"
  )
done

b2sums=('ea29b3b8b6f178dd21d7ee3042ea829755e398f8876dfac6c744de77c08d85abb2736add2141204e6d3343de46040f49d96e1ad455714880b4be46d7b36581f1'
        'SKIP'
        'fff0dec06b21e391783cc136790238acb783780eaedcf14875a350e7ceb46fdc100c8b9e3f09fb7f4c2196c25d4c6b61e574c0dad762d94533b628faab68cf5c'
        '4cede03c08758ccd6bf53a0d0057d7542dfdd0c93d342e89f3b90460be85518a9fd24958d8b1da2b5a09b5ddbee8a4263982194158e171c2bba3e394d88d6dac'
        '77c4b166f1200e1ee2ab94a5014acd334c1fe4b7d72851d73768d491c56c6779a0882a304c1f30c88732a6168351f0f786b10516ae537cff993892a749175848'
        '35e8548611c51ee75f4d04926149e5e54870d7073d9b635d550a6fa0f85891f57f326bdbcff3dd8618cf40f8e08cf903ef87d9c034d5921d8b91e1db842cdd7c'
        '2cff6626624d03f70f1662af45a8644c28a9f92e2dfe38999bef3ba4a4c1ce825ae598277e9cb7abd5585eebfb17b239effc8d0bbf1c6ac196499f0d288e5e01')

# https://docs.mesa3d.org/relnotes.html
sha256sums=('02761ffd965dd64b95421ebfca1191d73724aba00f30034009237564f34cf976'
            'SKIP'
            '39278fbbf5fb4f646ce651690877f89d1c5811a3d4acb27700c1cb3cdb78fd3b'
            '3354b9ac3fae1ff6755cb6db53683adb661634f67557942dea4facebec0fee4b'
            '5267fca4496028628a95160fc423a33e8b2e6af8a5302579e322e4b520293cae'
            'de3145af08024dea9fa9914f381a17b8fc6034dfb00f3a84013f7ff43f29ed4c'
            '23e78b90f2fcf45d3e842032ce32e3f2d1545ba6636271dcbf24fa306d87be7a')

prepare() {
  cd mesa-$pkgver

  # Include package release in version string so Chromium invalidates
  # its GPU cache; otherwise it can cause pages to render incorrectly.
  # https://bugs.launchpad.net/ubuntu/+source/chromium-browser/+bug/2020604
  echo "$pkgver-arch$epoch.$pkgrel" >VERSION
}

build() {
  local meson_options=(
    -D android-libbacktrace=disabled
    -D b_ndebug=true
    -D gallium-drivers=r300,r600,radeonsi,nouveau,virgl,svga,swrast,i915,iris,crocus,zink,d3d12
    -D gallium-extra-hud=true
    -D gallium-nine=true
    -D gallium-omx=bellagio
    -D gallium-opencl=icd
    -D gallium-rusticl=true
    -D gles1=disabled
    -D glx=dri
    -D intel-clc=enabled
    -D intel-rt=enabled
    -D libunwind=disabled
    -D microsoft-clc=disabled
    -D osmesa=true
    -D platforms=x11,wayland
    -D valgrind=enabled
    -D video-codecs=all
    -D vulkan-drivers=amd,intel,intel_hasvk,swrast,virtio,nouveau
    -D vulkan-layers=device-select,intel-nullhw,overlay
  )

  # Build only minimal debug info to reduce size
  CFLAGS+=" -g1"
  CXXFLAGS+=" -g1"

  # Inject subproject packages
  export MESON_PACKAGE_CACHE_DIR="$srcdir"

  arch-meson mesa-$pkgver build "${meson_options[@]}"
  meson compile -C build
}

_pick() {
  local p="$1" f d; shift
  for f; do
    d="$srcdir/$p/${f#$pkgdir/}"
    mkdir -p "$(dirname "$d")"
    mv -v "$f" "$d"
    rmdir -p --ignore-fail-on-non-empty "$(dirname "$f")"
  done
}

package_mesa() {
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libelf
    libglvnd
    libx11
    libxcb
    libxext
    libxfixes
    libxshmfence
    libxxf86vm
    llvm-libs
    lm_sensors
    wayland
    zlib
    zstd

    libomxil-bellagio
  )
  optdepends=("opengl-man-pages: for the OpenGL API man pages")
  provides=(
    mesa-libgl
    opengl-driver
  )
  conflicts=(mesa-libgl)
  replaces=(mesa-libgl)

  meson install -C build --destdir "$pkgdir"

  (
    local libdir=usr/lib icddir=usr/share/vulkan/icd.d

    cd "$pkgdir"

    _pick libva $libdir/dri/*_drv_video.so

    _pick vdpau $libdir/vdpau

    _pick clover $libdir/gallium-pipe
    _pick clover $libdir/libMesaOpenCL*
    _pick clover etc/OpenCL/vendors/mesa.icd

    _pick clrust $libdir/libRusticlOpenCL*
    _pick clrust etc/OpenCL/vendors/rusticl.icd

    _pick vkintel $icddir/intel_*.json
    _pick vkintel $libdir/libvulkan_intel*.so

    _pick vklayer $libdir/libVkLayer_*.so
    _pick vklayer usr/bin/mesa-overlay-control.py
    _pick vklayer usr/share/vulkan/{ex,im}plicit_layer.d

    _pick vknvidia $icddir/nouveau_*.json
    _pick vknvidia $libdir/libvulkan_nouveau*.so

    _pick vkradeon $icddir/radeon_icd*.json
    _pick vkradeon $libdir/libvulkan_radeon.so
    _pick vkradeon usr/share/drirc.d/00-radv-defaults.conf

    _pick vkswrast $icddir/lvp_icd*.json
    _pick vkswrast $libdir/libvulkan_lvp.so

    _pick vkvirtio $icddir/virtio_icd*.json
    _pick vkvirtio $libdir/libvulkan_virtio.so

    # indirect rendering
    ln -sr $libdir/libGLX_{mesa,indirect}.so.0
  )

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_libva-mesa-driver() {
  pkgdesc="Open-source VA-API drivers"
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libelf
    libx11
    libxcb
    libxshmfence
    llvm-libs
    zlib
    zstd
  )
  provides=(libva-driver)

  mv libva/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_mesa-vdpau() {
  pkgdesc="Open-source VDPAU drivers"
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libelf
    libx11
    libxcb
    libxshmfence
    llvm-libs
    zlib
    zstd
  )
  provides=(vdpau-driver)

  mv vdpau/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_opencl-clover-mesa() {
  pkgdesc="Open-source OpenCL drivers - Clover variant"
  depends=(
    clang
    expat
    gcc-libs
    glibc
    libdrm
    libelf
    llvm-libs
    spirv-llvm-translator
    spirv-tools
    zlib
    zstd

    libclc
  )
  optdepends=("opencl-headers: headers necessary for OpenCL development")
  provides=(opencl-driver)
  replaces=("opencl-mesa<=23.1.4-1")
  conflicts=(opencl-mesa)

  mv clover/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_opencl-rusticl-mesa() {
  pkgdesc="Open-source OpenCL drivers - RustICL variant"
  depends=(
    clang
    expat
    gcc-libs
    glibc
    libdrm
    libelf
    llvm-libs
    spirv-llvm-translator
    spirv-tools
    zlib
    zstd

    libclc
  )
  optdepends=("opencl-headers: headers necessary for OpenCL development")
  provides=(opencl-driver)
  replaces=("opencl-mesa<=23.1.4-1")
  conflicts=(opencl-mesa)

  mv clrust/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_vulkan-intel() {
  pkgdesc="Open-source Vulkan driver for Intel GPUs"
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libx11
    libxcb
    libxshmfence
    systemd-libs
    vulkan-icd-loader
    wayland
    xcb-util-keysyms
    zlib
    zstd
  )
  optdepends=("vulkan-mesa-layers: additional vulkan layers")
  provides=(vulkan-driver)

  mv vkintel/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_vulkan-mesa-layers() {
  pkgdesc="Mesa's Vulkan layers"
  depends=(
    gcc-libs
    glibc
    libdrm
    libxcb
    wayland

    python
  )
  conflicts=(vulkan-mesa-layer)
  replaces=(vulkan-mesa-layer)

  mv vklayer/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_vulkan-nouveau() {
  pkgdesc="Open-source Vulkan driver for Nvidia GPUs"
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libx11
    libxcb
    libxshmfence
    systemd-libs
    vulkan-icd-loader
    wayland
    xcb-util-keysyms
    zlib
    zstd
  )
  optdepends=("vulkan-mesa-layers: additional vulkan layers")
  provides=(vulkan-driver)

  mv vknvidia/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_vulkan-radeon() {
  pkgdesc="Open-source Vulkan driver for AMD GPUs"
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libelf
    libx11
    libxcb
    libxshmfence
    llvm-libs
    systemd-libs
    vulkan-icd-loader
    wayland
    xcb-util-keysyms
    zlib
    zstd
  )
  optdepends=("vulkan-mesa-layers: additional vulkan layers")
  provides=(vulkan-driver)

  mv vkradeon/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_vulkan-swrast() {
  pkgdesc="Open-source Vulkan driver for CPUs (Software Rasterizer)"
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libx11
    libxcb
    libxshmfence
    llvm-libs
    systemd-libs
    vulkan-icd-loader
    wayland
    xcb-util-keysyms
    zlib
    zstd
  )
  optdepends=("vulkan-mesa-layers: additional vulkan layers")
  conflicts=(vulkan-mesa)
  replaces=(vulkan-mesa)
  provides=(vulkan-driver)

  mv vkswrast/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

package_vulkan-virtio() {
  pkgdesc="Open-source Vulkan driver for Virtio-GPU (Venus)"
  depends=(
    expat
    gcc-libs
    glibc
    libdrm
    libx11
    libxcb
    libxshmfence
    systemd-libs
    vulkan-icd-loader
    wayland
    xcb-util-keysyms
    zlib
    zstd
  )
  optdepends=("vulkan-mesa-layers: additional vulkan layers")
  provides=(vulkan-driver)

  mv vkvirtio/* "$pkgdir"

  install -Dm644 mesa-$pkgver/docs/license.rst -t "$pkgdir/usr/share/licenses/$pkgname"
}

# vim:set sw=2 sts=-1 et:
