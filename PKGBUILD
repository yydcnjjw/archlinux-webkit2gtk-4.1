# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Eric Bélanger <eric@archlinux.org>

pkgbase=webkit2gtk-4.1
pkgname=(
  webkit2gtk-4.1
  webkit2gtk-4.1-docs
)
pkgver=2.40.5
pkgrel=1
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
  use_shared_array_buffer.patch
)
sha256sums=('7de051a263668621d91a61a5eb1c3771d1a7cec900043d4afef06c326c16037f'
            'SKIP'
            '54388092309424fe907ed65a246e80c6f1ff51ff8694c767481ae9c9416bd812')
b2sums=('67b8f429af8b746a256ca36c335c33ab4fef92a4699b09b87c38e81d56e0da7b5fb20d9e0dbbc32016025e60ca2c6e44d9032c300bfbdc931f4097d5608bb62f'        'SKIP'
        'b7bb00b4df939602805c99a19917ede067134eb6747f911c4f8904579dee0378ecf39386970d770aa139c2e1c2a1e0c02e6b2ef0e0e9c3c48aa71a1159438dfd')
validpgpkeys=(
  'D7FCF61CF9A2DEAB31D81BD3F3D322D0EC4582C3'  # Carlos Garcia Campos <cgarcia@igalia.com>
  '5AA3BC334FD7E3369E7C77B291C559DBE4C9123B'  # Adrián Pérez de Castro <aperez@igalia.com>
)

prepare() {
  cd webkitgtk-$pkgver
  patch --forward --strip=1 --input="${srcdir}/use_shared_array_buffer.patch"
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
  cmake --build build -j8
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
