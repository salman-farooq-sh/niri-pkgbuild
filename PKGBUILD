# Maintainer: cosmo <aur@dawnson.is>
# Maintainer: FreeFull <jazz2rulez@gmail.com>
# Contributor: Julius Michaelis <gitter@liftm.de.de>
# Contributor: Nebulosa  <nebulosa2007-at-yandex-dot-ru>
 
## The following variable can be customized at build time.
## Use env or export to change at your wish
##
##   Example: env _sccache=y makepkg -sc
##
## Default is: None => not use sccache
##
## More info: https://github.com/mozilla/sccache
: ${_sccache:=}

pkgname=niri-salman
pkgver=541a2e7dd300dae1673afb8134b34e37b4cfcfb7
pkgrel=1
pkgdesc="Scrollable-tiling Wayland compositor"
arch=(x86_64 aarch64)
url="https://github.com/salman-farooq-sh/${pkgname%-salman}"
license=(GPL-3.0-or-later)
depends=(cairo glib2 libdisplay-info libinput libpipewire libxkbcommon mesa pango pixman seatd)
makedepends=(clang rust git makepkg-git-lfs-proto)
[[ -n ${_sccache} ]] && makedepends+=(sccache)
optdepends=('fuzzel: application launcher similar to rofi drun mode'
            'waybar: highly customizable Wayland bar'
            'alacritty: a cross-platform OpenGL terminal emulator'
            'mako: notification daemon for Wayland'
            'swaybg: wallpaper tool for Wayland compositors'
            'swaylock: screen locker for Wayland'
            'xdg-desktop-portal-gtk: implements most of the basic functionality'
            'xdg-desktop-portal-gnome: screencasting support'
            'gnome-keyring: implements the secret portal, for certain apps to work'
            'polkit-gnome: when apps need to ask for root permissions')
provides=(${pkgname%-salman}=${pkgver})
conflicts=(${pkgname%-salman}-bin ${pkgname%-salman}-git ${pkgname%-git})
options=(!debug !lto !strip)
source=(${pkgname%-salman}::git-lfs+$url.git)
b2sums=('SKIP')

pkgver() {
  cd ${pkgname%-salman}
  git log -n 1 --pretty=format:"%H"
}

prepare() {
  cd ${pkgname%-salman}
  # Tuning cargo
  export CARGO_HOME=${srcdir}/${pkgname%-salman}/.cargo    # Download all to src directory, not in ~/.cargo

  cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
  cd ${pkgname%-salman}

  # Tuning rust compiler
  export RUSTFLAGS="--remap-path-prefix=${srcdir}=/"    # Prevent warning: 'Package contains reference to $srcdir'
  [[ -n ${_sccache} ]] && export RUSTC_WRAPPER=sccache  # If $_sccache not empty, build using binary cache

  # Tuning cargo
  export CARGO_HOME=${srcdir}/${pkgname%-salman}/.cargo # Use downloaded earlier from src directory, not from ~/.cargo
  export CARGO_TARGET_DIR=target                        # Place the output in target relative to the current directory

  cargo build --frozen --release
}

package() {
  cd ${pkgname%-salman}
  install -Dm755 target/release/${pkgname%-salman}                       -t ${pkgdir}/usr/bin/
  install -Dm755 resources/${pkgname%-salman}-session                    -t ${pkgdir}/usr/bin/
  install -Dm644 resources/default-config.kdl                            -t ${pkgdir}/usr/share/doc/niri
  install -Dm644 resources/${pkgname%-salman}.desktop                    -t ${pkgdir}/usr/share/wayland-sessions/
  install -Dm644 resources/${pkgname%-salman}-portals.conf               -t ${pkgdir}/usr/share/xdg-desktop-portal/
  install -Dm644 resources/${pkgname%-salman}{.service,-shutdown.target} -t ${pkgdir}/usr/lib/systemd/user/
}
