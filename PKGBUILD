# Maintainer:  Wez Furlong <wez at wezfurlong dot org>
# Co-Maintainer: Bernat Gabor <gaborjbernat@gmail.com>

pkgname=("wezterm-git")
pkgdesc="A terminal emulator implemented in Rust, using OpenGL ES 2 for rendering."
pkgver=20210916.085405.e36cf754
pkgrel=3
arch=("x86_64" "i686")
url="https://github.com/wez/wezterm"
license=("MIT")
depends=(
  "dbus"
  "fontconfig"
  "hicolor-icon-theme"
  "libx11"
  "libxkbcommon-x11"
  "wayland"
  "xcb-util-image"
  "xcb-util-keysyms"
  "xcb-util-wm"
)
makedepends=("cargo" "cmake" "git" "pkgconf" "python")
provides=("wezterm" "wezterm-gui" "wezterm-mux-server")
conflicts=("wezterm" "wezterm-bin" "wezterm-nightly-bin")
source=(
  "wezterm::git+https://github.com/wez/wezterm.git"
  "harfbuzz::git+https://github.com/harfbuzz/harfbuzz.git"
  "libpng::git+https://github.com/glennrp/libpng.git"
  "zlib::git+https://github.com/madler/zlib.git"
  "freetype2::git+https://github.com/wez/freetype2.git"
)
sha256sums=("SKIP" "SKIP" "SKIP" "SKIP" "SKIP")

prepare() {
  cd $srcdir/wezterm
  git submodule init
  git config -f .gitmodules "submodule.harfbuzz/harfbuzz.url" $srcdir/harfbuzz
  git config -f .gitmodules "submodule.freetype/libpng.url" $srcdir/libpng
  git config -f .gitmodules "submodule.deps/freetype/zlib.url" $srcdir/zlib
  git config -f .gitmodules "submodule.freetype2.url" $srcdir/freetype2
  git submodule update
  cargo fetch --locked --target "$CARCH-unknown-linux-gnu"
}

pkgver() {
  cd $srcdir/wezterm
  git -c "core.abbrev=8" show -s "--format=%cd-%h" "--date=format:%Y%m%d-%H%M%S" | tr - .
}

build() {
  cd $srcdir/wezterm
  bash ci/check-rust-version.sh
  cargo build --frozen --release
  tic -x -o "$srcdir/terminfo" "$srcdir/wezterm/termwiz/data/wezterm.terminfo"
}

package() {
  cd $srcdir/wezterm

  install -Dsm755 target/release/wezterm $pkgdir/usr/bin/wezterm
  install -Dsm755 target/release/wezterm-gui $pkgdir/usr/bin/wezterm-gui
  install -Dsm755 target/release/wezterm-mux-server $pkgdir/usr/bin/wezterm-mux-server
  install -Dsm755 target/release/strip-ansi-escapes $pkgdir/usr/bin/strip-ansi-escapes

  install -Dm644 assets/icon/terminal.png $pkgdir/usr/share/icons/hicolor/128x128/apps/org.wezfurlong.wezterm.png
  install -Dm644 assets/wezterm.desktop $pkgdir/usr/share/applications/org.wezfurlong.wezterm.desktop
  install -Dm644 assets/wezterm.appdata.xml $pkgdir/usr/share/metainfo/org.wezfurlong.wezterm.appdata.xml
  install -Dm644 ../terminfo/w/wezterm "$pkgdir/usr/share/terminfo/w/wezterm"

  install -Dm644 LICENSE.md -t "${pkgdir}/usr/share/licenses/${pkgname}"
}

