# Maintainer: Edward Davis <idlyby@proton.me>
# Contributor: Reza Jahanbakhshi <reza.jahanbakhshi at gmail dot com
# Contributor: Lone_Wolf <lone_wolf@klaas-de-kat.nl>
# Contributor: Armin K. <krejzi at email dot com>
# Contributor: Kristian Klausen <klausenbusk@hotmail.com>
# Contributor: Egon Ashrafinia <e.ashrafinia@gmail.com>
# Contributor: Tavian Barnes <tavianator@gmail.com>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Antti "Tera" Oja <antti.bofh@gmail.com>
# Contributor: Diego Jose <diegoxter1006@gmail.com>

pkgname=lib32-mesa-git
pkgdesc="an open-source implementation of the OpenGL specification, git version"
pkgver=24.3.0_devel.194631.e0e7d8d910e.d41d8cd
pkgrel=1
arch=('x86_64')
makedepends=(
  'git'
  'xorgproto'
  'lib32-libxml2' 
  'lib32-libvdpau'
  'lib32-libva' 
  'lib32-libglvnd'
  'lib32-libxrandr'
  'wayland-protocols' 
  'meson' 
  'python-mako' 
  'python-packaging' 
  'python-pyaml'
  'lib32-llvm'
  'lib32-spirv-llvm-translator'
  'lib32-spirv-tools'  
)
depends=(
  'mesa-git' 
  'lib32-gcc-libs' 
  'lib32-libdrm' 
  'lib32-wayland' 
  'lib32-libxxf86vm' 
  'lib32-libxdamage' 
  'lib32-libxshmfence' 
  'lib32-libelf' 
  'lib32-libunwind' 
  'lib32-lm_sensors' 
  'glslang' 
  'lib32-vulkan-icd-loader' 
  'lib32-zstd' 
  'lib32-libxcb' 
  'lib32-libxfixes' 
  'lib32-expat' 
  'lib32-libxext' 
  'lib32-libx11'
  'lib32-zlib' 
  'lib32-glibc'  
  'lib32-clang'
  'lib32-llvm-libs'
  'lib32-spirv-llvm-translator'
  'lib32-spirv-tools'
)
optdepends=('opengl-man-pages: for the OpenGL API man pages')
provides=(
  'lib32-mesa' 
  'lib32-vulkan-radeon' 
  'lib32-vulkan-mesa-layers' 
  'lib32-libva-mesa-driver' 
  'lib32-mesa-vdpau' 
  'lib32-mesa-libgl' 
  'lib32-opengl-driver' 
  'lib32-vulkan-driver'
)
conflicts=(
  'lib32-mesa' 
  'lib32-vulkan-radeon' 
  'lib32-vulkan-mesa-layers' 
  'lib32-libva-mesa-driver' 
  'lib32-mesa-vdpau' 
  'lib32-mesa-libgl'
)
url="https://www.mesa3d.org"
license=('custom')
source=(
  'mesa::git+https://gitlab.freedesktop.org/mesa/mesa.git#branch=main'
  'LICENSE'
  'llvm32.native')
md5sums=(
  'SKIP'
  '5c65a0fe315dd347e09b1f2826a1df5a'
  '6b4a19068a323d7f90a3d3cd315ed1f9'
)
sha512sums=(
  'SKIP'
  '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
  'c7dbb390ebde291c517a854fcbe5166c24e95206f768cc9458ca896b2253aabd6df12a7becf831998721b2d622d0c02afdd8d519e77dea8e1d6807b35f0166fe'
)

pkgver() {
  cd mesa
  local _ver
  _ver=$(<VERSION)

  local _patchver
  local _patchfile
  for _patchfile in "${source[@]}"; do
    _patchfile="${_patchfile%%::*}"
    _patchfile="${_patchfile##*/}"
    [[ $_patchfile = *.patch ]] || continue
    _patchver="${_patchver}$(md5sum ${srcdir}/${_patchfile} | cut -c1-32)"
  done
  _patchver="$(echo -n $_patchver | md5sum | cut -c1-7)"

  echo ${_ver/-/_}.$(git rev-list --count HEAD).$(git rev-parse --short HEAD).${_patchver}
}

prepare() {
  # although removing _build folder in build() function feels more natural,
  # that interferes with the spirit of makepkg --noextract
  if [  -d _build ]; then
    rm -rf _build
  fi

  # Ugly hack to allow recompilation using gamescope.path
  if [ -f "mesa/src/egl/wayland/wayland-drm/gamescope-commit-queue-v1.xml" ]; then
    rm -rf "mesa/src/egl/wayland/wayland-drm/gamescope-commit-queue-v1.xml"
  fi

  # Include package release in version string so Chromium invalidates
  # its GPU cache; otherwise it can cause pages to render incorrectly.
  # https://bugs.launchpad.net/ubuntu/+source/chromium-browser/+bug/2020604
  echo "$pkgver-$pkgrel" >VERSION

  local _patchfile
  for _patchfile in "${source[@]}"; do
    _patchfile="${_patchfile%%::*}"
    _patchfile="${_patchfile##*/}"
    [[ $_patchfile = *.patch ]] || continue
    echo "Applying patch $_patchfile..."
    patch --directory=mesa --forward --strip=1 --input="${srcdir}/${_patchfile}"
  done
}

build () {
  local meson_options=(
    --native-file llvm32.native
    -D android-libbacktrace=disabled
    -D b_ndebug=true
    -D b_lto=false
    -D buildtype=plain 
    -D gallium-drivers=radeonsi,softpipe,zink 
    -D egl=enabled 
    -D gallium-extra-hud=true 
    -D gallium-nine=false 
    -D gallium-opencl=disabled 
    -D gallium-rusticl=false
    -D gallium-va=enabled 
    -D gallium-vdpau=enabled 
    -D gallium-xa=disabled 
    -D gbm=enabled 
    -D gles1=disabled 
    -D gles2=enabled 
    -D glvnd=enabled 
    -D glx=dri 
    -D intel-rt=disabled
    -D libunwind=disabled 
    -D llvm=enabled 
    -D lmsensors=enabled
    -D microsoft-clc=disabled 
    -D platforms=x11,wayland 
    -D shared-glapi=enabled 
    -D valgrind=disabled 
    -D video-codecs=all
    -D vulkan-drivers=amd,swrast 
    -D vulkan-layers=device-select,overlay,screenshot
    -D vulkan-beta=true
    -D tools=[] 
    -D zstd=enabled 
    --wrap-mode=nofallback 
    -D prefix=/usr 
    -D sysconfdir=/etc 
    --libdir=/usr/lib32 
  )

  export CC="${CC:-gcc}"
  export CXX="${CXX:-g++}"
  CC="$CC -m32"
  CXX="$CXX -m32"

  export PKG_CONFIG=/usr/bin/i686-pc-linux-gnu-pkg-config
  # Build only minimal debug info to reduce size
  #CFLAGS+=' -g1'
  #CXXFLAGS+=' -g1'

  export BINDGEN_EXTRA_CLANG_ARGS="-m32"

  meson setup mesa _build "${meson_options[@]}"
    
  meson configure --no-pager _build

  ninja $NINJAFLAGS -C _build
}

package() {
  DESTDIR="$pkgdir" ninja $NINJAFLAGS -C _build install

  # remove files provided by mesa-git
  rm -rf "$pkgdir"/etc
  rm -rf "$pkgdir"/usr/include
  rm -rf "$pkgdir"/usr/share/glvnd/
  rm -rf "$pkgdir"/usr/share/drirc.d/
  rm -rf "$pkgdir"/usr/share/vulkan/explicit_layer.d/
  rm -rf "$pkgdir"/usr/share/vulkan/implicit_layer.d/VkLayer_MESA_device_select.json

  # remove script file from /usr/bin
  # https://gitlab.freedesktop.org/mesa/mesa/issues/2230
  find "${pkgdir}/usr/bin" -type f -name "*.py" -exec rm {} \;
  rmdir "${pkgdir}/usr/bin"

  # indirect rendering
  ln -s /usr/lib32/libGLX_mesa.so.0 "${pkgdir}/usr/lib32/libGLX_indirect.so.0"
  install -m644 -Dt  "$pkgdir"/usr/share/licenses/$pkgbase/ "$srcdir"/LICENSE 
}
