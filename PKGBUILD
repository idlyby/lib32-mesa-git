# Maintainer: Lone_Wolf <lonewolf at xs4all dot nl>
# Contributor: Armin K. <krejzi at email dot com>
# Contributor: Kristian Klausen <klausenbusk@hotmail.com>
# Contributor: Egon Ashrafinia <e.ashrafinia@gmail.com>
# Contributor: Tavian Barnes <tavianator@gmail.com>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Antti "Tera" Oja <antti.bofh@gmail.com>
# Contributor: Diego Jose <diegoxter1006@gmail.com>

pkgbase=lib32-mesa-git
pkgname=('lib32-mesa-git')
pkgdesc="an open-source implementation of the OpenGL specification, git version"
pkgver=18.3.0_devel.103997.c3325097be
pkgrel=1
arch=('x86_64')
makedepends=('python2-mako' 'lib32-libxml2' 'lib32-libx11' 'xorgproto'
             'lib32-gcc-libs' 'lib32-libvdpau' 'lib32-libelf' 'lib32-llvm-svn' 'git' 'lib32-libgcrypt' 'lib32-systemd'
             'mesa-git' 'lib32-llvm-libs-svn' 'lib32-libglvnd' 'wayland-protocols' 'lib32-wayland' 'meson')
depends=('mesa-git' 'lib32-gcc-libs' 'lib32-libdrm' 'lib32-wayland' 'lib32-libxxf86vm' 'lib32-libxdamage' 'lib32-libxshmfence' 'lib32-elfutils'
           'lib32-llvm-libs-svn' 'lib32-libunwind' 'lib32-lm_sensors')
optdepends=('opengl-man-pages: for the OpenGL API man pages')
provides=('lib32-mesa' 'lib32-opencl-mesa' 'lib32-vulkan-intel' 'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau' 'lib32-opengl-driver')
conflicts=('lib32-mesa' 'lib32-opencl-mesa' 'lib32-vulkan-intel' 'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau')
url="http://mesa3d.sourceforge.net"
license=('custom')
source=('mesa::git://anongit.freedesktop.org/mesa/mesa'
        'LICENSE'
)

sha512sums=('SKIP'
            '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
)


pkgver() {
    cd mesa
    read -r _ver <VERSION
    echo ${_ver/-/_}.$(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

build () {
  export CC="gcc -m32"
  export CXX="g++ -m32"
  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"
  export LLVM_CONFIG=/usr/bin/llvm-config32

      if [  -d _build ]; then
        rm -rf _build
    fi
    meson setup mesa _build \
       -D b_ndebug=true \
       -D buildtype=plain \
       --wrap-mode=nofallback \
       -D prefix=/usr \
       -D sysconfdir=/etc \
       --libdir=/usr/lib32 \
       -D platforms=x11,wayland,drm,surfaceless \
       -D dri-drivers=i915,i965,r200,r100,nouveau \
       -D gallium-drivers=r300,r600,radeonsi,nouveau,svga,swrast,virgl \
       -D vulkan-drivers=amd,intel \
       -D dri3=true \
       -D egl=true \
       -D gallium-extra-hud=true \
       -D gallium-nine=true \
       -D gallium-omx=disabled \
       -D gallium-opencl=disabled \
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
       -D valgrind=false \
       -D tools=[]
    meson configure _build
    ninja -C _build
}


package_lib32-mesa-git () {

  DESTDIR="$pkgdir" ninja -C _build install

  # https://bugs.freedesktop.org/show_bug.cgi?id=107487
  # removing those files and the /usr/bin/ folder
  rm "$pkgdir"/usr/bin/intel_dump_gpu "$pkgdir"/usr/bin/intel_sanitize_gpu  
  rmdir "$pkgdir"/usr/bin
  
  # remove files provided by mesa-git
  rm -rf "$pkgdir"/etc
  rm -rf "$pkgdir"/usr/include
  rm -rf "$pkgdir"/usr/share/glvnd/

  # remove files present in lib32-libglvnd
  rm "$pkgdir"/usr/lib32/libGLESv{1_CM,2}.so*
    
  # indirect rendering
  ln -s /usr/lib32/libGLX_mesa.so.0 "${pkgdir}/usr/lib32/libGLX_indirect.so.0"
  install -Dt  "$pkgdir"/usr/share/licenses/$pkgbase/ -m644 "$srcdir"/LICENSE 
}
