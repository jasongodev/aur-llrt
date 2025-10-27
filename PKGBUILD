# Maintainer: Jason Go <jasongo@jasongo.net>

pkgbase=llrt
pkgname=(
  'llrt'
  'llrt-full-sdk'
  'llrt-no-sdk'
  'llrt-all'
  'llrt-lambda'
  'llrt-container'
)
pkgver=0.7.0beta
pkgrel=1
arch=('x86_64' 'aarch64')
url='https://github.com/awslabs/llrt'
license=('Apache-2.0')
makedepends=('cmake' 'rustup' 'yarn' 'zig' 'zip')
optdepends=(
  'typescript: transpiler for TypeScript code with type checking support'
  'esbuild: fast compiler and bundler for JavaScript and TypeScript'
  'swc-js-bin: drop-in replacement for Babel with compilation and polyfill support'
  'bun-bin: fast runtime, compiler, and bundler for JavaScript and TypeScript'
)
source=("git+$url.git#tag=v${pkgver//beta/-beta}")
sha256sums=('20058c4d89e6f1675885b835aef2d3215b85fa0d55bba62d07de5481aa847e5d')
options=(!buildflags !makeflags)
_CARCH="$( [ "$CARCH" == "aarch64" ] && echo "arm64" || echo "x64" )"

prepare() {
  cd llrt
  git submodule update --init --checkout
  rustup default nightly
  yarn
}

build() {
  cd llrt
  make stdlib && make libs

  case "$pkgname" in
    llrt)
      make release
      ;;

    llrt-full-sdk)
      make release-full-sdk
      ;;

    llrt-no-sdk)
      make release-no-sdk
      ;;

    llrt-all)
      make release
      make release-full-sdk
      make release-no-sdk
      ;;

    llrt-lambda)
      make llrt-lambda-"${_CARCH}".zip
      make llrt-lambda-"${_CARCH}"-no-sdk.zip
      make llrt-lambda-"${_CARCH}"-full-sdk.zip
      ;;

    llrt-container)
      make llrt-container-"${_CARCH}"
      make llrt-container-"${_CARCH}"-no-sdk
      make llrt-container-"${_CARCH}"-full-sdk
      ;;
  esac
}

_install_llrt() {
  bsdtar -xf "${llrt-linux-$_CARCH-$1.zip//-std-sdk/}" -C "$srcdir/llrt"
  install -Dm755 "$srcdir/llrt/llrt" "$pkgdir/usr/bin/llrt$([[ "$2" == true ]] && echo "-$1")"
}

_install_llrt_bootstrap() {
  bsdtar -xf "${llrt-lambda-$_CARCH-$1.zip//-std-sdk/}" -C "$srcdir/llrt"
  install -Dm755 "$srcdir/llrt/bootstrap" "$pkgdir/usr/share/llrt/lambda/$1/bootstrap"
}

_install_licenses() {
  install -Dm644 -t "$pkgdir/usr/share/licenses/$pkgname" "$srcdir/llrt/"{LICENSE,THIRD_PARTY_LICENSE,NOTICE}
}

package_llrt() {
  provides=('llrt')
  conflicts=('llrt')
  pkgdesc='Lightweight JavaScript runtime, compiler, REPL, and test runner (STANDARD @aws-sdk bundled)'
  _install_llrt 'std-sdk'
  _install_licenses
}

package_llrt-full-sdk() {
  provides=('llrt')
  conflicts=('llrt')
  pkgdesc='Lightweight JavaScript runtime, compiler, REPL, and test runner (FULL @aws-sdk bundled)'
  _install_llrt 'full-sdk'
  _install_licenses
}

package_llrt-no-sdk() {
  provides=('llrt')
  conflicts=('llrt')
  pkgdesc='Lightweight JavaScript runtime, compiler, REPL, and test runner (NO @aws-sdk bundled)'
  _install_llrt 'no-sdk'
  _install_licenses
}

package_llrt-all() {
  provides=('llrt')
  conflicts=('llrt')
  pkgdesc='Lightweight JavaScript runtime, compiler, REPL, and test runner (All bundle types included with suffix: llrt, llrt-full-sdk, llrt-no-sdk)'
  _install_llrt 'std-sdk'
  _install_llrt 'std-sdk' true
  _install_llrt 'full-sdk' true
  _install_llrt 'no-sdk' true
  _install_licenses
}

package_llrt-lambda() {
  optdepends+=(
    'llrt: LLRT CLI runtime, compiler, REPL, and test runner'
    'aws-sam-cli: CLI tool to build, test, debug, and deploy Serverless applications using AWS SAM'
    'aws-cdk: AWS CDK Toolkit'
  )
  provides=('llrt-lambda')
  conflicts=('llrt-lambda')
  pkgdesc='Lightweight JavaScript runtime (BOOTSTRAP/LAYER binary for AWS Lambda, AWS SAM, and AWS CDK)'
  _install_llrt_bootstrap 'std-sdk'
  _install_llrt_bootstrap 'full-sdk'
  _install_llrt_bootstrap 'no-sdk'
  _install_licenses
}

package_llrt-container() {
  optdepends+=(
    'llrt: LLRT CLI runtime, compiler, REPL, and test runner'
    'aws-sam-cli: CLI tool to build, test, debug, and deploy Serverless applications using AWS SAM'
  )
  provides=('llrt-container')
  conflicts=('llrt-container')
  pkgdesc='Lightweight JavaScript runtime (CONTAINER binary to be packaged with container images)'
  install -Dm755 -t "$pkgdir/usr/share/llrt/container/" "$srcdir/llrt/llrt-container-$_CARCH*"
  _install_licenses
}
