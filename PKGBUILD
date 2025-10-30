# Maintainer: Jason Go <jasongo@jasongo.net>

pkgname=llrt
pkgver=0.7.0beta
pkgrel=14
pkgdesc='Lightweight JavaScript runtime, compiler, REPL, and test runner (STANDARD @aws-sdk bundled)'
arch=('x86_64' 'aarch64')
url='https://github.com/awslabs/llrt'
license=('Apache-2.0')
makedepends=('cmake' 'corepack' 'rustup' 'zig' 'zip')
optdepends=(
  'typescript: transpiler for TypeScript code with type checking support'
  'esbuild: fast compiler and bundler for JavaScript and TypeScript'
  'swc-js-bin: drop-in replacement for Babel with compilation and polyfill support'
  'bun-bin: fast runtime, compiler, and bundler for JavaScript and TypeScript'
)
provides=('llrt')
conflicts=('llrt')
options=(!buildflags !makeflags)
source=("git+$url.git#tag=v${pkgver//beta/-beta}")
sha256sums=('20058c4d89e6f1675885b835aef2d3215b85fa0d55bba62d07de5481aa847e5d')

_CARCH="$( [ "$CARCH" == "aarch64" ] && echo "arm64" || echo "x64" )"

prepare() {
  cd llrt
  # Use Rust's nightly version from the date of the upstream release to prevent regression issues
  sed -i "s/RUST_VERSION = nightly/RUST_VERSION = nightly-2025-09-23/g" Makefile
  rustup install nightly-2025-09-23 &
  git submodule update --init --checkout &
  [ ! -x /usr/bin/yarn ] && (
    corepack enable yarn &&
    corepack install
  ); yarn &
  wait
}

build() {
  cd llrt
  make "stdlib-$_CARCH" && make "libs-$_CARCH"
  make llrt-linux-$_CARCH.zip
}

package() {
  local llrt="$srcdir/llrt/target/$CARCH-unknown-linux-musl/release/llrt"
  install -Dm755 "$llrt" "$pkgdir/usr/bin/llrt"
  install -Dm755 "$llrt" "$pkgdir/usr/bin/llrt-std-sdk"
  install -Dm644 -t "$pkgdir/usr/share/licenses/$pkgname" "$srcdir/llrt/"{LICENSE,THIRD_PARTY_LICENSES,NOTICE}
}
