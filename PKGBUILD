# Maintainer: Jason R. McNeil <jason@jasonrm.net>
# Contributor: Carsten Feuls <archlinux@carstenfeuls.de>
# Contributor: Gilles Hamel <hamelg at laposte dot net>

_pkgname=grafana
pkgname=${_pkgname}-git
pkgver=v2.6.0.r1552.ga531a17
pkgrel=1
pkgdesc="A general purpose dashboard and graph composer. It supports graphite, influxdb or opentsdb"
url="http://grafana.org"
arch=('x86_64' 'i686')
license=('APACHE')
depends=()
provides=("grafana")
conflicts=("grafana")
makedepends=("git" "go" "godep" "nodejs-grunt-cli" "npm" "phantomjs>=2.1.1")
install=${_pkgname}.install
backup=("etc/${_pkgname}/${_pkgname}.ini")
source=("git+https://github.com/${_pkgname}/${_pkgname}"
        "config.patch"
        "grafana.service")
sha512sums=('SKIP'
            '5adace9b12a58cefa5cf7763d2879673fb48279244fdc35da1512988969ff6077dbb182316fb5809d7597573fd8d2c5f9028bcf106a58a1b1dcfaf04bd4f99f7'
            '2fdb8eda4671a81cc7674f471a20f37cd2078123838a80c3906777da3b6a8602caf5bcb65a23038a67b70f556d43cd8db7982a8dc7f3ee317ce1634e4c7409f7')

pkgver() {
    cd "${srcdir}/${_pkgname}"
    git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare () {
   cd "${_pkgname}"
   patch -p1 -i "${srcdir}"/config.patch
}

build() {
    export GOPATH="${srcdir}"/${_pkgname}
    export PATH="$PATH:$GOPATH/bin"
    mkdir -p "$GOPATH/src/github.com/grafana/grafana/"
    ln -s "$GOPATH/pkg" "$GOPATH/src/github.com/grafana/grafana/"

    # Build the backend
    cd "$GOPATH"
    go run build.go setup
    godep restore
    go run build.go build

    # Build frontend assets
    npm install
    cd "$GOPATH"
    grunt
}

package() {
     install -Dm644 "${srcdir}/grafana.service" "$pkgdir/usr/lib/systemd/system/grafana.service"
     cd "${srcdir}/${_pkgname}"
     install -dm755 "${pkgdir}/var/lib/grafana"
     install -dm755 "${pkgdir}/var/log/grafana"
     install -Dsm755 bin/grafana-server "$pkgdir/usr/bin/grafana-server"
     install -Dm644 conf/sample.ini "$pkgdir/etc/${_pkgname}/${_pkgname}.ini"
     install -Dm644 conf/defaults.ini "$pkgdir/usr/share/grafana/conf/defaults.ini"
     cp -r vendor "$pkgdir/usr/share/grafana/"
     cp -r public_gen "$pkgdir/usr/share/grafana/public"
}
