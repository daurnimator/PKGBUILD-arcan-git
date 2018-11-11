# Maintainer: Daurnimator <quae@daurnimator.com>
# Contributor: Jens Staal <staal1978@gmail.com>

## Known issue: cmake uses absolute paths which result in binaries containing
## build root via __FILE__ macro

pkgname=('arcan-git'
         'arcan-acfgfs-git'
         'arcan-aclip-git'
         'arcan-aloadimage-git'
         'arcan-leddec-git'
         'arcan-ltui-git'
         'arcan-net-git'
         'arcan-shmmon-git'
         'arcan-waybridge-git'
         'arcan-vrbridge-git')
pkgver=0.5.5.r21.g483e5681
pkgrel=1
pkgdesc='Game Engine meets a Display Server meets a Multimedia Framework'
arch=('x86_64')
url='https://arcan-fe.com'
license=('GPL' 'LGPL' 'BSD')
makedepends=('git'
             'cmake'
             'fuse3'
             'libvncserver'
             'lua51'
             # TODO: vrbridge wants openhmd
             'ruby'
             'wayland')
source=(git+https://github.com/letoram/arcan)
sha256sums=('SKIP')

pkgver() {
  cd arcan

  ( set -o pipefail
    git describe --long 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
  )
}

build() {
  cd arcan

  # Build docs
  ## Needs to happen before cmake runs
  ruby -C doc -Ku docgen.rb mangen

  # Build main library/application
  mkdir -p build
  env -C build cmake \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DDISABLE_JIT=ON \
    -DLUA_INCLUDE_DIR=/usr/include/lua5.1 \
    -DDISTR_TAG=arch \
    -DENGINE_BUILDTAG="$pkgver" \
    -DDISABLE_HIJACK=OFF \
    -DVIDEO_PLATFORM=egl-dri \
    ../src
  make -C build

  # Build misc utils
  for tool in acfgfs aclip aloadimage leddec ltui netproxy shmmon vrbridge waybridge; do
    env -C "src/tools/$tool" \
      cmake --no-warn-unused-cli \
      -DCMAKE_INSTALL_PREFIX=/usr \
      -DARCAN_SHMIF_INCLUDE_DIR=../../shmif \
      -DARCAN_SHMIF_LIBRARY=../../../build/shmif/libarcan_shmif.so \
      -DARCAN_SHMIF_SERVER_LIBRARY=../../../build/shmif/libarcan_shmif_server.so \
      -DARCAN_SHMIF_EXT_INCLUDE_DIR=../../shmif \
      -DARCAN_SHMIF_EXT_LIBRARY=../../../build/shmif/libarcan_shmif_ext.so \
      -DARCAN_TUI_INCLUDE_DIR=../../shmif \
      -DARCAN_TUI_LIBRARY=../../../build/shmif/libarcan_tui.so
    make -C "src/tools/$tool"
  done
}

package_arcan-git() {
  depends=('apr'
           'harfbuzz-icu'
           'libvncserver'
           'lua51'
           'openal'
           'vlc')
  provides=('arcan')
  conflicts=('arcan')

  cd arcan

  make -C build DESTDIR="$pkgdir" install
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/$pkgname/COPYING"
}

package_arcan-acfgfs-git() {
  pkgdesc='Arcan virtual filesystem for working with the format that durden (and others) provide over a domain socket'
  depends=('fuse3')
  provides=('arcan-acfgfs')
  conflicts=('arcan-acfgfs')

  cd arcan

  make -C src/tools/acfgfs DESTDIR="$pkgdir" install
}

package_arcan-aclip-git() {
  pkgdesc='Arcan clipboard integration, similarly to how "xclip" works for Xorg'
  depends=('arcan-git')
  provides=('arcan-aclip')
  conflicts=('arcan-aclip')

  cd arcan

  make -C src/tools/aclip DESTDIR="$pkgdir" install
}

package_arcan-aloadimage-git() {
  pkgdesc='Arcan sandboxed image loader, supporting multi-process privilege separation, playlists and so on - similar to xloadimage'
  depends=('arcan-git')
  provides=('arcan-aloadimage')
  conflicts=('arcan-aloadimage')

  cd arcan

  make -C src/tools/aloadimage DESTDIR="$pkgdir" install
}

package_arcan-net-git() {
  pkgdesc='A development sandbox for the arcan-net bridge used to link single clients over a network'
  depends=('arcan-git')
  provides=('arcan-net')
  conflicts=('arcan-net')

  cd arcan

  make -C src/tools/netproxy DESTDIR="$pkgdir" install
}

package_arcan-shmmon-git() {
  pkgdesc='Simple shmif- debugging aid for Arcan'
  depends=('arcan-git')
  provides=('arcan-shmmon')
  conflicts=('arcan-shmmon')

  cd arcan

  make -C src/tools/shmmon DESTDIR="$pkgdir" install
}

package_arcan-vrbridge-git() {
  pkgdesc='Aggregates samples from VR related SDKs and binds into a single avatar in a way that integrates with the core engine VR path'
  depends=('arcan-git')
  provides=('arcan-vrbridge')
  conflicts=('arcan-vrbridge')

  cd arcan

  make -C src/tools/vrbridge DESTDIR="$pkgdir" install
}

package_arcan-waybridge-git() {
  pkgdesc='Bridges wayland connections with an arcan connection point'
  depends=('arcan-git' 'wayland')
  provides=('arcan-waybridge')
  conflicts=('arcan-waybridge')

  cd arcan

  make -C src/tools/waybridge DESTDIR="$pkgdir" install
}

package_arcan-leddec-git() {
  pkgdesc='A simple skeleton that can be used for interfacing with custom LED controllers using Arcan'
  depends=('arcan-git')
  provides=('arcan-leddec')
  conflicts=('arcan-leddec')

  cd arcan

  make -C src/tools/leddec DESTDIR="$pkgdir" install
}

package_arcan-ltui-git() {
  pkgdesc='A patched version of the Lua interactive CLI that loads in the shmif-tui (text-user interfaces)'
  depends=('arcan-git')
  provides=('arcan-ltui')
  conflicts=('arcan-ltui')

  cd arcan

  make -C src/tools/ltui DESTDIR="$pkgdir" install
  install -Dm755 doc/lua-tui.md "$pkgdir/usr/share/doc/arcan-ltui/lua-tui.md"
}
