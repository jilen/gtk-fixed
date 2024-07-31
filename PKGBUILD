# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Ionut Biru <ibiru@archlinux.org>

pkgbase=gtk3
pkgname=(
  gtk3
  gtk3-demos
  gtk3-docs
)
pkgver=3.24.43
pkgrel=100
epoch=1
pkgdesc="GObject-based multi-platform GUI toolkit"
url="https://www.gtk.org/"
arch=(x86_64)
license=(LGPL-2.1-or-later)
depends=(
  adwaita-icon-theme
  at-spi2-core
  cairo
  cantarell-fonts
  dconf
  desktop-file-utils
  fontconfig
  fribidi
  gdk-pixbuf2
  glib2
  glibc
  harfbuzz
  iso-codes
  libcloudproviders
  libcolord
  libcups
  libegl
  libepoxy
  libgl
  librsvg
  libx11
  libxcomposite
  libxcursor
  libxdamage
  libxext
  libxfixes
  libxi
  libxinerama
  libxkbcommon
  libxrandr
  libxrender
  pango
  shared-mime-info
  tracker3
  wayland
)
makedepends=(
  git
  glib2-devel
  gobject-introspection
  gtk-doc
  hicolor-icon-theme
  meson
  sassc
  wayland-protocols
)
source=(
  "git+https://gitlab.gnome.org/GNOME/gtk.git#tag=$pkgver"
  gtk-query-immodules-3.0.hook
  0001-Allow-disabling-legacy-Tracker-search.patch
  cairo_leak_fix.patch
)
b2sums=('fdda77eebdc0b8e378f0258cb241eda4412b868d59ea1fd90815f459e925e6433f94c22a088d695b72fab99ecca827b370942bea47043debef4fab78e0e03dca'
        '8e6a3906126749c6d853f582e3802254cdbba099c6af7190ad576eff6ea5425404a72b1b36950a87e3afdac82295cfe246003172c3e0341a73bd931a36f3b407'
        '7da1746e7702e4bf397f59dd1019e2c8fa8951b2bcc6bf64ec05f322de6dcec6fe5552848d6b389818f625988a3fb2211501d7f72ae97d2c49fbad1e5fe9cd6a'
        '20ae155210772694fa2903dcac3914d051194aaa9be57f7659efb4c0ddbe91599f12d4003deb3361d8d76f099c8f1da1feceb60d082e9ea0827b743c4a4259ef')

prepare() {
  cd gtk
  git apply ../cairo_leak_fix.patch
  git apply -3 ../0001-Allow-disabling-legacy-Tracker-search.patch
}

build() {
  local meson_options=(
    -D broadway_backend=true
    -D cloudproviders=true
    -D colord=yes
    -D gtk_doc=true
    -D introspection=true
    -D man=true
    -D tracker=false
    -D tracker3=true
  )

  CFLAGS+=" -DG_DISABLE_CAST_CHECKS"
  arch-meson gtk build "${meson_options[@]}"
  meson compile -C build
}

_pick() {
  local p="$1" f d; shift
  for f; do
    d="$srcdir/$p/${f#$pkgdir/}"
    mkdir -p "$(dirname "$d")"
    mv "$f" "$d"
    rmdir -p --ignore-fail-on-non-empty "$(dirname "$f")"
  done
}

package_gtk3() {
  depends+=(gtk-update-icon-cache)
  optdepends=('evince: Default print preview command')
  provides=(
    gtk3-print-backends
    libgailutil-3.so
    libgdk-3.so
    libgtk-3.so
  )
  conflicts=(gtk3-print-backends)
  replaces=("gtk3-print-backends<=3.22.26-1")
  install=gtk3.install

  meson install -C build --destdir "$pkgdir"

  install -Dm644 /dev/stdin "$pkgdir/usr/share/gtk-3.0/settings.ini" <<END
[Settings]
gtk-icon-theme-name = Adwaita
gtk-theme-name = Adwaita
gtk-font-name = Cantarell 11
END

  install -Dt "$pkgdir/usr/share/libalpm/hooks" -m644 gtk-query-immodules-3.0.hook

  cd "$pkgdir"

  _pick demo usr/bin/gtk3-{demo,demo-application,icon-browser,widget-factory}
  _pick demo usr/share/applications/gtk3-{demo,icon-browser,widget-factory}.desktop
  _pick demo usr/share/glib-2.0/schemas/org.gtk.{Demo,exampleapp}.gschema.xml
  _pick demo usr/share/icons/hicolor/*/apps/gtk3-{demo,widget-factory}[-.]*
  _pick demo usr/share/man/man1/gtk3-{demo,demo-application,icon-browser,widget-factory}.1

  _pick docs usr/share/gtk-doc

  # Built by GTK 4, shared with GTK 3
  rm usr/bin/gtk-update-icon-cache
  rm usr/share/man/man1/gtk-update-icon-cache.1
}

package_gtk3-demos() {
  pkgdesc+=" (demo applications)"
  depends=(
    at-spi2-core
    cairo
    dconf
    gdk-pixbuf2
    glib2
    glibc
    gtk3
    harfbuzz
    hicolor-icon-theme
    libepoxy
    pango
  )
  mv demo/* "$pkgdir"
}

package_gtk3-docs() {
  pkgdesc+=" (documentation)"
  depends=()
  mv docs/* "$pkgdir"
}

# vim:set sw=2 sts=-1 et:
