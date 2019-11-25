# Maintainer: Daurnimator <quae@daurnimator.com>
# Contributor: Jens Staal <staal1978@gmail.com>

## Known issue: cmake uses absolute paths which result in binaries containing
## build root via __FILE__ macro

pkgbase='arcan-git'
pkgname=('arcan-git'
         'arcan-acfgfs-git'
         'arcan-aclip-git'
         'arcan-adbginject-git'
         'arcan-aloadimage-git'
         'arcan-shmmon-git'
         'arcan-trayicon-git'
         'arcan-vrbridge-git'
         'arcan-waybridge-git')
pkgver=0.5.5.r333.ge37928dd
pkgrel=1
pkgdesc='Game Engine meets a Display Server meets a Multimedia Framework'
arch=('x86_64')
url='https://arcan-fe.com'
license=('GPL' 'LGPL' 'BSD')
makedepends=('cmake'
             'git'
             'fuse3'
             'libvncserver'
             'lua51'
             'meson'
             # TODO: vrbridge wants openhmd
             'ruby'
             'wayland')
source=("${pkgbase}::git+https://github.com/letoram/arcan.git")
sha256sums=('SKIP')

pkgver () {
  cd "${pkgbase}"
  (
    set -o pipefail
    git describe --long 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
  )
}

build() {
  cd "${pkgbase}"

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
  for tool in acfgfs aclip aloadimage shmmon vrbridge waybridge; do
    mkdir -p "build-$tool"
    env -C "build-$tool" cmake \
      -DCMAKE_INSTALL_PREFIX=/usr \
      --no-warn-unused-cli \
      -DARCAN_SHMIF_INCLUDE_DIR=../src/shmif \
      -DARCAN_SHMIF_LIBRARY=../build/shmif/libarcan_shmif.so \
      -DARCAN_SHMIF_SERVER_LIBRARY=../build/shmif/libarcan_shmif_server.so \
      -DARCAN_SHMIF_EXT_LIBRARY=../build/shmif/libarcan_shmif_ext.so \
      -DARCAN_TUI_INCLUDE_DIR=../src/shmif \
      -DARCAN_TUI_LIBRARY=../build/shmif/libarcan_tui.so \
      "../src/tools/$tool"
    make -C "build-$tool"
  done

  for tool in adbginject trayicon; do
    meson --prefix=/usr --buildtype=plain "src/tools/$tool" "build-$tool"
    ninja -C "build-$tool"
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

  cd "${pkgbase}"

  make -C build DESTDIR="$pkgdir" install
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/$pkgname/COPYING"
}

package_arcan-acfgfs-git() {
  pkgdesc='Arcan virtual filesystem for working with the format that durden (and others) provide over a domain socket'
  depends=('fuse3')
  provides=('arcan-acfgfs')
  conflicts=('arcan-acfgfs')

  cd "${pkgbase}"

  make -C build-acfgfs DESTDIR="$pkgdir" install
}

package_arcan-aclip-git() {
  pkgdesc='Arcan clipboard integration, similarly to how "xclip" works for Xorg'
  depends=('arcan-git')
  provides=('arcan-aclip')
  conflicts=('arcan-aclip')

  cd "${pkgbase}"

  make -C build-aclip DESTDIR="$pkgdir" install
}

package_arcan-aloadimage-git() {
  pkgdesc='Arcan sandboxed image loader, supporting multi-process privilege separation, playlists and so on - similar to xloadimage'
  depends=('arcan-git')
  provides=('arcan-aloadimage')
  conflicts=('arcan-aloadimage')

  cd "${pkgbase}"

  make -C build-aloadimage DESTDIR="$pkgdir" install
}

package_arcan-shmmon-git() {
  pkgdesc='Simple shmif- debugging aid for Arcan'
  depends=('arcan-git')
  provides=('arcan-shmmon')
  conflicts=('arcan-shmmon')

  cd "${pkgbase}"

  make -C build-shmmon DESTDIR="$pkgdir" install
}

package_arcan-vrbridge-git() {
  pkgdesc='Aggregates samples from VR related SDKs and binds into a single avatar in a way that integrates with the core engine VR path'
  depends=('arcan-git')
  provides=('arcan-vrbridge')
  conflicts=('arcan-vrbridge')

  cd "${pkgbase}"

  make -C build-vrbridge DESTDIR="$pkgdir" install
}

package_arcan-waybridge-git() {
  pkgdesc='Bridges wayland connections with an arcan connection point'
  depends=('arcan-git' 'wayland')
  provides=('arcan-waybridge')
  conflicts=('arcan-waybridge')

  cd "${pkgbase}"

  make -C build-waybridge DESTDIR="$pkgdir" install
}

package_arcan-adbginject-git() {
  pkgdesc='A trivial dynamic interposition library for providing some arcan UI interfacing'
  depends=('arcan-git')
  provides=('arcan-adbginject')
  conflicts=('arcan-adbginject')

  cd "${pkgbase}"

  env DESTDIR="$pkgdir" ninja -C build-adbginject install
}

package_arcan-trayicon-git() {
  pkgdesc='A simple tool that connects to arcan as an ICON'
  depends=('arcan-git')
  provides=('arcan-trayicon')
  conflicts=('arcan-trayicon')

  cd "${pkgbase}"

  env DESTDIR="$pkgdir" ninja -C build-trayicon install
}
