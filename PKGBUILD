# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Eric Bélanger <eric@archlinux.org>

pkgbase=webkit2gtk-4.1
pkgname=(
  webkit2gtk-4.1
  webkit2gtk-4.1-docs
)
pkgver=2.40.2
pkgrel=3
pkgdesc="Web content engine for GTK"
url="https://webkitgtk.org"
arch=(x86_64)
license=(custom)
depends=(
  at-spi2-core
  atk
  bubblewrap
  cairo
  enchant
  fontconfig
  freetype2
  glib2
  gst-plugins-bad-libs
  gst-plugins-base-libs
  gstreamer
  gtk3
  harfbuzz
  harfbuzz-icu
  hyphen
  icu
  libavif
  libdrm
  libegl
  libepoxy
  libgcrypt
  libgl
  libgles
  libjpeg
  libmanette
  libpng
  libseccomp
  libsecret
  libsoup3
  libsystemd
  libtasn1
  libwebp
  libwpe
  libx11
  libxcomposite
  libxml2
  libxslt
  libxt
  mesa
  openjpeg2
  sqlite
  wayland
  woff2
  wpebackend-fdo
  xdg-dbus-proxy
  zlib
)
makedepends=(
  cmake
  gi-docgen
  gobject-introspection
  gperf
  gst-plugins-bad
  ninja
  python
  ruby
  systemd
  unifdef
  wayland-protocols
)
source=(
  $url/releases/webkitgtk-$pkgver.tar.xz{,.asc}
  https://github.com/WebKit/WebKit/commit/d501d8dce059d7c3307c022e51e1853e348439ac.patch
)
sha256sums=('96898870d994da406ee7a632816dcde9a3bb395ee5f344fcb3f3b8cc8a77e000'
            'SKIP'
            '9ae8f66d8d09e9ec094ba2280a3f26053244d225bd077a9e67bc9f7c6a5228b5')
b2sums=('177ac87de1c17768c50761f4146f3c8dc218abacb9de0c811ec4acf2ea7117cd62e256cb09345c55ee8da7479c8df3faf3c1b09d0876be33dd740bf6541af27d'
        'SKIP'
        '7cb7404cc1c306a3cca16077c71c595211e6c66e56f0f0ee466260e8897aafa9f2c9a468866aeb50586288928a5312ace7fc44dc550dfe0c268b42cf209e41de')
validpgpkeys=(
  'D7FCF61CF9A2DEAB31D81BD3F3D322D0EC4582C3'  # Carlos Garcia Campos <cgarcia@igalia.com>
  '5AA3BC334FD7E3369E7C77B291C559DBE4C9123B'  # Adrián Pérez de Castro <aperez@igalia.com>
)

prepare() {
  cd webkitgtk-$pkgver

  # https://bugs.archlinux.org/task/78836
  # https://gitlab.gnome.org/GNOME/epiphany/-/issues/2101
  patch -Np1 -i ../d501d8dce059d7c3307c022e51e1853e348439ac.patch
}

build() {
  local cmake_options=(
    -DPORT=GTK
    -DCMAKE_BUILD_TYPE=Release
    -DCMAKE_INSTALL_PREFIX=/usr
    -DCMAKE_INSTALL_LIBDIR=lib
    -DCMAKE_INSTALL_LIBEXECDIR=lib
    -DCMAKE_SKIP_RPATH=ON
    -DUSE_AVIF=ON
    -DUSE_SOUP2=OFF
    -DENABLE_DOCUMENTATION=ON
    -DENABLE_MINIBROWSER=ON
  )

  # Produce minimal debug info: 4.3 GB of debug data makes the
  # build too slow and is too much to package for debuginfod
  CFLAGS+=' -g1'
  CXXFLAGS+=' -g1'

  cmake -S webkitgtk-$pkgver -B build -G Ninja "${cmake_options[@]}"
  cmake --build build
}

package_webkit2gtk-4.1() {
  depends+=(
    libWPEBackend-fdo-1.0.so
    libwpe-1.0.so
  )
  provides+=(
    libjavascriptcoregtk-4.1.so
    libwebkit2gtk-4.1.so
  )
  optdepends=(
    'geoclue: Geolocation support'
    'gst-libav: nonfree media decoding'
    'gst-plugins-bad: media decoding'
    'gst-plugins-good: media decoding'
  )

  DESTDIR="$pkgdir" cmake --install build

  rm -r "$pkgdir/usr/bin"

  mkdir -p doc/usr/share
  mv {"$pkgdir",doc}/usr/share/gtk-doc

  cd webkitgtk-$pkgver
  find Source -name 'COPYING*' -or -name 'LICENSE*' -print0 | sort -z |
    while IFS= read -d $'\0' -r _f; do
      echo "### $_f ###"
      cat "$_f"
      echo
    done |
    install -Dm644 /dev/stdin "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}

package_webkit2gtk-4.1-docs() {
  pkgdesc+=" (documentation)"
  depends=()

  mv doc/* "$pkgdir"
}

# vim:set sw=2 sts=-1 et:
