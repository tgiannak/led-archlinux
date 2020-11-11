# Maintainer: Theophilos Giannakopoulos <theo@abstractnonsense.org>

pkgname=LDtk
pkgver="v0.5.2_beta"
pkgrel=1
pkgdesc="Level Designer Toolkit: a 2D level editor"
arch=('any')
url="https://deepnight.net/tools/ldtk-2d-level-editor/"
license=('MIT')
depends=('electron')
makedepends=('npm' 'haxe' 'icoutils')
replaces=('LEd')
source=("$pkgname::git+https://github.com/deepnight/ldtk.git#tag=${pkgver//_/-}"
        "samples-dir.patch"
        "ldtk"
        "LDtk.desktop")
sha256sums=('SKIP'
            '65ed7fc633226511197ff4efa181c6a2e00d484ee66d2e8dd1f0785e1917a305'
            'da01d21b815c928cf0451cc9e7dce5af6fae2f6b9d4be0975d44229b9d4fc29b'
            '38de6761f94e57561eac96311c8b3d19b89568e28fb3fb9bf1e4679c109ca5ce')

prepare() {
  git -C "$srcdir/$pkgname" apply "$srcdir/samples-dir.patch"
}

build() {
  cd "$srcdir/$pkgname"

  haxelib newrepo
  haxelib --never git heaps https://github.com/HeapsIO/heaps.git
  haxelib --never git hxnodejs https://github.com/HaxeFoundation/hxnodejs.git
  haxelib --never git electron https://github.com/tong/hxelectron.git
  haxelib --never git deepnightLibs https://github.com/deepnight/deepnightLibs.git
  haxelib --never git ldtk-haxe-api https://github.com/deepnight/ldtk-haxe-api.git
  haxelib --never git castle https://github.com/ncannasse/castle.git

  (
    cd app
    npm install
  )

  haxe main.hxml
  haxe renderer.hxml

  (
    cd app
    npm run compile
    npx electron-builder build --linux \
        --publish never \
        --dir \
        -c.electronDist=/usr/lib/electron \
        -c.electronVersion="$(</usr/lib/electron/version)"
  )

  icotool --extract --width=256 "app/buildAssets/icon.ico" -o "icon.png"
}

package() {
  install -Dm644 -t "${pkgdir}/usr/share/applications/" "LDtk.desktop"
  install -Dm755 -t "${pkgdir}/usr/bin/" "ldtk"

  install -Dm644 \
          -t "${pkgdir}/usr/share/ldtk/" \
          "${pkgname}/app/redist/linux-unpacked/resources/app.asar"

  install -Dm644 \
          -t "${pkgdir}/usr/share/ldtk/" \
          "${pkgname}/icon.png"

  install -Dm644 \
          -t "${pkgdir}/usr/share/licenses/ldtk/" \
          "${pkgname}/LICENSE"
  # The READMEs include required attributions for included components.
  install -Dm644 \
          -t "${pkgdir}/usr/share/licenses/ldtk/" \
          "${pkgname}/README.md"
  install -Dm644 \
          -t "${pkgdir}/usr/share/licenses/ldtk/samples/" \
          "${pkgname}/app/samples/README.md"

  (
    cd "${pkgname}/app/redist/linux-unpacked/samples"
    for file in $(find . -type f); do
      install -Dm644 \
              "${file}" \
              "${pkgdir}/usr/share/ldtk/samples/${file}"
    done
  )
}
