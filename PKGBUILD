# Maintainer: Oliver Weissbarth <mail@oweissbarth.de>
pkgname=djv
pkgver=2.0.8
pkgrel=2
pkgdesc="Professional media review software for VFX, animation, and film production"
arch=("x86_64")
url="http://djv.sourceforge.net/"
license=('CUSTOM')
groups=()
depends=('libjpeg' 'libpng' 'libtiff' 'ffmpeg')
makedepends=('cmake' 'alsa-lib' 'glew' 'libxcursor' 'libxinerama' 'libxrandr' 'nasm')
replaces=()
backup=()
options=()
source=("${pkgname}-${pkgver}.tgz::https://github.com/darbyjohnston/DJV/archive/$pkgver.tar.gz" "djv.desktop" "djv.sh" "CMakeLists.patch")
noextract=()
md5sums=('3b5df10a31591b21441ea2512da34c6b' 'SKIP' 'SKIP' 'SKIP'
)


prepare() {
	patch DJV-${pkgver}/CMakeLists.txt < CMakeLists.patch
	
	# Remove assert macro
	sed -i '44,51d' DJV-${pkgver}/lib/djvCore/Core.h
	sed -i '44i#ifdef DJV_ASSERT \n #undef DJV_ASSERT\n#endif\n#define DJV_ASSERT(value)' DJV-${pkgver}/lib/djvCore/Core.h
}

build() {
	export DJV_BUILD=$PWD
	export LD_LIBRARY_PATH=$DJV_BUILD/DJV-install/lib:$LD_LIBRARY_PATH

	njobs=$(grep -Po -- '-j\s?[0-9]+'<<<"${MAKEFLAGS}") ||
	njobs=$(grep -c ^processor /proc/cpuinfo)
	
	cmake -S "DJV-${pkgver}/third-party" -B DJV-third-party \
		-DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_INSTALL_PREFIX="$DJV_BUILD/DJV-install"
	cmake --build DJV-third-party -j "${njobs}"
	cmake --build DJV-third-party -j "${njobs}" --target install
	
	cmake -S "DJV-${pkgver}" -B DJV-Release \
		-DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_INSTALL_PREFIX="$DJV_BUILD/DJV-install" \
		-DCMAKE_PREFIX_PATH="$DJV_BUILD/DJV-install" \
		-DCMAKE_INSTALL_RPATH=""

	cmake --build DJV-Release -j "${njobs}"
	cmake --build DJV-Release -j "${njobs}" --target install

}

package() {
	install -D -m755 "$srcdir"/DJV-install/bin/djv* -t "$pkgdir/opt/${pkgname}/bin/"
	cp -r "$srcdir/DJV-install/docs" "$pkgdir/opt/${pkgname}/"
	cp -r "$srcdir/DJV-install/etc" "$pkgdir/opt/${pkgname}/"
	install -D -m644 "${srcdir}/DJV-${pkgver}/LICENSE.txt" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE.txt"
	install -D -m644 "${srcdir}/${pkgname}.desktop" "${pkgdir}/usr/share/applications/${pkgname}.desktop"
	install -D -m644 "${srcdir}/DJV-${pkgver}/etc/Icons/djv-app-icon-512.svg" "${pkgdir}/usr/share/pixmaps/djv.svg"
	
	install -d -m755 "${pkgdir}/usr/bin/"
	for file in "${pkgdir}/opt/${pkgname}"/bin/*; do
		ln -s "/opt/${pkgname}/bin/${file##*/}" "${pkgdir}/usr/bin/${file##*/}"
	done
	
	install -D -m655 "${srcdir}/djv.sh" "${pkgdir}/usr/bin/djv"
	
}
