# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Pellegrino Prevete <pellegrinoprevete@gmail.com>
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Jose Riha <jose1711 gmail com>
# Contributor: archdaemon
# Contributor: sekret
# Contributor: qubidt

_pkgname="untrunc"
pkgname="${_pkgname}"
pkgver=r184.8ae3c90
pkgrel=1
libav_version=12.3
pkgdesc="Restore a damaged (truncated) mp4, m4v, mov, 3gp video"
arch=(
  'x86_64'
  'i686'
  'arm'
  'aarch64'
  'armv7l'
)
url="https://github.com/ponchio/untrunc"
license=(
  'GPL2'
)
depends=(
  'libvdpau'
  'zlib'
  'bzip2'
)
makedepends=(
  'yasm'
  'git'
  'libx11'
)
_os="$( \
    uname \
      -o)"
[[ "${_os}" == "Android" ]] && \
  makedepends+=(
    clang
  )
[[ "${_os}" == "GNU/Linux" ]] && \
  makedepends+=(
    gcc
  )
_libav="https://github.com/libav/libav"
_ffmpeg="https://git.ffmpeg.org/gitweb/ffmpeg"
_commit="5f2fa79d25228633d7429fc1fa41f0789ad0d726"
source=(
  "${_pkgname}::git+${url}.git#commit=${_commit}"
  'framealloc.patch'
  "libav-${libav_version}.tar.gz::${_libav}/archive/refs/tags/v${libav_version}.tar.gz"
  "mathops.patch::${_ffmpeg}.git/commitdiff_plain/effadce6c756247ea8bae32dc13bb3e6f464f0eb"
)
md5sums=(
  'SKIP'
  '7f6cf1762cd93f4ce84cb218ab856ea3'
  '78f791a4f595a67abd3a7d0c976524c5'
  'adf42203d59a3c98a863216113946661'
)
noextract=(
  "libav-${libav_version}.tar.gz"
)

prepare() {
  cd \
    "${srcdir}/${pkgname}"
  tar \
    xf \
    "../libav-${libav_version}.tar.gz"
  cd \
    "libav-${libav_version}"
  patch \
    -p1 \
    -i \
      ../../mathops.patch
}

build() {
  local \
    _cflags=() \
    _cxxflags=() \
    _extra_flags=() \
    _cc="g++" \
    _prefix=""
  _cflags=(
    "${CFLAGS}"
  )
  _cxxflags=(
    "${CXXFLAGS}"
  )
  if [[ "${_os}" == "Android" ]]; then
    _cc="clang"
    _cxx="clang++"
    _prefix="error-"
    _extra_flags=(
      # I do wonder why clang/llvm people
      # chose to have incompatible error
      # flags with gcc
      -Wno-"${_prefix}"incompatible-function-pointer-types
      # -Wno-error-incompatible-function-pointer-types
      -Wno-"${_prefix}"register
      -Wno-"${_prefix}"deprecated-register
    )
    _cflags+=(
      "${_extra_flags[@]}"
    )
    _cxxflags+=(
      "${_extra_flags[@]}"
    )
  fi
  cd \
    "${srcdir}/${_pkgname}"
  git \
    submodule \
      init
  git \
    submodule \
      update \
        --depth \
          1
  # patch \
  #   -p1 \
  #     -i \
  #       "${srcdir}/framealloc.patch"
  # wget \
  #   http://libav.org/releases/libav-${libav_version}.tar.xz
  cd \
    libav-"${libav_version}"
  export \
    CFLAGS="${_cflags[*]}" \
    CXXFLAGS="${_cxxflags[*]}" \
    CC="${_cc}" \
    CXX="${_cxx}"
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cxxflags[*]}" \
  CC="${_cc}" \
  CXX="${_cxx}" \
  ./configure
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cxxflags[*]}" \
  CC="${_cc}" \
  CXX="${_cxx}" \
  make
  cd \
    ..
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cxxflags[*]}" \
  CXX="${_cxx}" \
  CC="${_cc}" \
  "${_cxx}" \
    -o \
      untrunc \
    -I"./libav-${libav_version}" \
    file.cpp \
    main.cpp \
    track.cpp \
    atom.cpp \
    codec_*.cpp \
    codecstats.cpp \
    codec.cpp \
    mp4.cpp \
    log.cpp \
    -L"./libav-${libav_version}/libavformat" \
    -lavformat \
    -L"./libav-${libav_version}/libavcodec" \
    -lavcodec \
    -L"./libav-${libav_version}/libavresample" \
    -lavresample \
    -L"./libav-${libav_version}/libavutil" \
    -lavutil \
    -lpthread \
    -lz \
    -lbz2 \
    -llzma \
    -lX11 \
    -lvdpau \
    -ldl
} 

pkgver() {
  cd \
    "${srcdir}/${_pkgname}"
  printf \
    "r%s.%s" \
    "$( \
      git \
        rev-list \
	  --count \
	  HEAD)" \
    "$( \
      git \
        rev-parse \
	  --short \
	  HEAD)"
}

package() {
  install \
    -Dm755 \
    "${srcdir}/${pkgname}/${_pkgname}" \
    "${pkgdir}/usr/bin/${_pkgname}"
}

# vim: ft=sh syn=sh et
