# Maintainer: Justin Dray <justin@dray.be>
# Contributor: Gilles Hamel <hamelg at laposte dot net>
_pkgname=grafana
pkgname=${_pkgname}-git
pkgver=v2.0.2.r566.ga386733
pkgrel=2
pkgdesc="A general purpose dashboard and graph composer. It supports graphite, influxdb or opentsdb"
url="http://grafana.org"
arch=('x86_64' 'i686')
license=('APACHE')
depends=(phantomjs)
makedepends=(go nodejs-grunt-cli)
provides=grafana
conflicts=grafana
install=${_pkgname}.install
backup=("etc/${_pkgname}/${_pkgname}.ini")
source=("git+https://github.com/${_pkgname}/${_pkgname}"
        "config.patch"
        "grafana.service")
md5sums=('SKIP'
         'f676cfe7bdd2463e2600ff8fce75a2d6'
         '05508c9c21f4c93ad86944a52d37e925')

pkgver() {
	cd "${srcdir}/${_pkgname}"
	git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare () {
  cd "${_pkgname}"	
  patch -p1 -i "${srcdir}"/config.patch
  #patch -p1 -i "${srcdir}"/0001-Replaced-slug-dependency-with-one-that-did-not-use-g.patch
}

build() {
  export GOPATH="${srcdir}/${_pkgname}"
  export PATH="$PATH:$GOPATH/bin"
  cd "$GOPATH"
  go run build.go setup
  godep restore
  mkdir -p "$GOPATH/src/github.com/grafana/grafana/"
  ln -s "$GOPATH/pkg" "$GOPATH/src/github.com/grafana/grafana/"
  # build less to css for the frontend 
  npm install
  #grunt
  # build the backend
  # no longer doing package build since this just kicks off rpm/deb builds at the end.
  #go run build.go build package
  go run build.go build
  grunt build
  grunt build-post-process
}

package() {
  install -Dm644 "${srcdir}/grafana.service" "$pkgdir/usr/lib/systemd/system/grafana.service"
  cd "${srcdir}/${_pkgname}"
  install -dm755 "${pkgdir}/var/lib/grafana"
  install -dm755 "${pkgdir}/var/log/grafana"
  install -Dsm755 bin/grafana-server "$pkgdir/usr/bin/grafana-server"
  install -Dm644 tmp/conf/sample.ini "$pkgdir/etc/${_pkgname}/${_pkgname}.ini"
  install -Dm644 tmp/conf/defaults.ini "$pkgdir/usr/share/grafana/conf/defaults.ini"
  cp -r tmp/public tmp/vendor "$pkgdir/usr/share/grafana/"
  rm "$pkgdir/usr/share/grafana/vendor/phantomjs/phantomjs"
}
