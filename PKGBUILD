pkgname=lib32-mingw-w64-graphite
pkgver=1.2.1
pkgrel=1
pkgdesc="reimplementation of the SIL Graphite text processing engine (mingw-w64, 32-bit)"
arch=(any)
url="http://graphite.sil.org"
license=("custom_SIL Dual license")
makedepends=(mingw-w64-gcc cmake)
depends=(mingw-w64-crt mingw-w64-freetype)
options=(!libtool !strip !buildflags)
source=("http://downloads.sourceforge.net/project/silgraphite/graphite2/graphite2-${pkgver}.tgz"
"Toolchain-i686-w64-mingw32.cmake")
md5sums=('edf70ab2f7d789be219f47b1df00d67e'
         'da10ad00a161800e0b011a77c3efbf76')

_architectures="i686-w64-mingw32"

_optimal_make_jobs() {
  if [ -r /proc/cpuinfo ]; then
    local core_count=$(grep -Fc processor /proc/cpuinfo)
  else
    local core_count=0
  fi
  if [ $core_count -gt 1 ]; then
    echo -n $[$core_count-1]
  else
    echo -n 1
  fi
}

build() {
  cd "$srcdir/graphite2-$pkgver"
  sed -i "s,Windows.h,windows.h,g" "src/gr_logging.cpp"
  unset LDFLAGS
  for _arch in ${_architectures}; do
    mkdir "build-${_arch}" && pushd "build-${_arch}"
    PATH="/usr/${_arch}/bin:$PATH" cmake \
      -DCMAKE_INSTALL_PREFIX:PATH=/usr/${_arch} \
      -DCMAKE_TOOLCHAIN_FILE=${srcdir}/Toolchain-${_arch}.cmake \
      ..
    make -j$(_optimal_make_jobs)
    popd
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "$srcdir/graphite2-$pkgver/build-${_arch}"
    make DESTDIR="$pkgdir" install
    find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*.bat' -o -name '*.def' -o -name '*.exp' -o -name '*.manifest' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip -x
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
  done
}
