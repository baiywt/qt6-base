# Maintainer: Antonio Rojas <arojas@archlinux.org>
# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Andrea Scarpino <andrea@archlinux.org>

pkgname=qt6-base
_qtver=6.2.1
pkgver=${_qtver/-/}
pkgrel=3
arch=(x86_64)
url='https://www.qt.io'
license=(GPL3 LGPL3 FDL custom)
pkgdesc='A cross-platform application and UI framework'
depends=(libjpeg-turbo xcb-util-keysyms xcb-util-cursor libgl fontconfig xdg-utils
         shared-mime-info xcb-util-wm libxrender libxi sqlite mesa vulkan-headers
         tslib libinput libxkbcommon-x11 libproxy libcups double-conversion brotli libb2 md4c)
makedepends=(cmake ninja libfbclient mariadb-libs unixodbc postgresql alsa-lib gst-plugins-base-libs
             gtk3 libpulse cups freetds xmlstarlet)
optdepends=('postgresql-libs: PostgreSQL driver'
            'mariadb-libs: MariaDB driver'
            'unixodbc: ODBC driver'
            'libfbclient: Firebird/iBase driver'
            'freetds: MS SQL driver'
            'gtk3: GTK platform plugin'
            'perl: for syncqt')
groups=(qt6)
_pkgfn="${pkgname/6-/}-everywhere-src-$_qtver"
source=(https://download.qt.io/official_releases/qt/${pkgver%.*}/$_qtver/submodules/$_pkgfn.tar.xz
        qt6-base-cflags.patch
        qt6-base-nostrip.patch
        qtbug-97811.patch::https://code.qt.io/cgit/qt/qtbase.git/patch/?id=ffc93236)
sha256sums=('2c5f07b5c3ea27d3fc1a46686ea3fb6724f94dddf1fb007de3eb0bdb87429079'
            'cf707cd970650f8b60f8897692b36708ded9ba116723ec8fcd885576783fe85c'
            '4b93f6a79039e676a56f9d6990a324a64a36f143916065973ded89adc621e094'
            '98b6871dabbf2cc6ed1742bed7b53248e1051340015d7f0974f6f1bf92a7aa9b')

prepare() {
  patch -d $_pkgfn -p1 < qt6-base-cflags.patch # Use system CFLAGS
  patch -d $_pkgfn -p1 < qt6-base-nostrip.patch # Don't strip binaries with qmake
  patch -d $_pkgfn -p1 < qtbug-97811.patch # Fix performance regression in scrollbars
}

build() {
  cmake -B build -S $_pkgfn -G Ninja \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DINSTALL_BINDIR=lib/qt6/bin \
    -DINSTALL_PUBLICBINDIR=usr/bin \
    -DINSTALL_LIBEXECDIR=lib/qt6 \
    -DINSTALL_DOCDIR=share/doc/qt6 \
    -DINSTALL_ARCHDATADIR=lib/qt6 \
    -DINSTALL_DATADIR=share/qt6 \
    -DINSTALL_INCLUDEDIR=include/qt6 \
    -DINSTALL_MKSPECSDIR=lib/qt6/mkspecs \
    -DINSTALL_EXAMPLESDIR=share/doc/qt6/examples \
    -DQT_FEATURE_journald=ON \
    -DQT_FEATURE_openssl_linked=ON \
    -DQT_FEATURE_system_sqlite=ON
  cmake --build build
}

package() {
  DESTDIR="$pkgdir" cmake --install build

  install -Dm644 $_pkgfn/LICENSE* -t "$pkgdir"/usr/share/licenses/$pkgbase

# Install symlinks for user-facing tools
  cd "$pkgdir"
  mkdir usr/bin
  while read _line; do
    ln -s $_line
  done < "$srcdir"/build/user_facing_tool_links.txt
}
