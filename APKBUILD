# Contributor: Ian McBeth <ian.mcbeth@gettyimages.com>
# Maintainer: Natanael Copa <ncopa@alpinelinux.org>
pkgname=collectd
pkgver=5.6.0
pkgrel=0
pkgdesc="The system statistics collection daemon"
url="http://collectd.org"
arch="all"
license="GPL2"
depends=""
makedepends="curl-dev net-snmp-dev postgresql-dev perl-dev python-dev
	libgcrypt-dev mariadb-dev zlib-dev openssl-dev iptables-dev
	rrdtool-dev yajl-dev lm_sensors-dev libxml2-dev
	hiredis-dev libdbi-dev
	autoconf automake libtool"
install=""
subpackages="$pkgname-dev $pkgname-doc $pkgname-perl $pkgname-snmp
	$pkgname-curl:_curl
	$pkgname-write_http $pkgname-nginx $pkgname-apache $pkgname-postgresql
	$pkgname-mysql $pkgname-network $pkgname-rrdtool $pkgname-sensors
	$pkgname-python:_python $pkgname-iptables:_ipt $pkgname-bind
	$pkgname-ascent $pkgname-redis $pkgname-dbi"

source="http://collectd.org/files/collectd-$pkgver.tar.bz2
	gnu_source.patch
	collectd.initd"

_builddir="$srcdir"/$pkgname-$pkgver

prepare() {
	cd "$_builddir"
	autoreconf -vif || return 1
	sed -i -e '/CFLAGS/s/-Werror//' configure src/Makefile.in \
		src/*/Makefile.in || return 1
}

build() {
	cd "$_builddir"
	./configure \
		--build=$CBUILD \
		--host=$CHOST \
		--prefix=/usr \
		--sysconfdir=/etc/collectd \
		--mandir=/usr/share/man \
		--infodir=/usr/share/info \
		--localstate=/var \
		--with-libiptc \
		--enable-rrdtool \
		--enable-bind \
		|| return 1
	make || return 1
	# disable network plugin by default since its in a subpackage
	sed -i -e 's/^LoadPlugin network/#LoadPlugin network/' \
		src/collectd.conf
}

package() {
	cd "$_builddir"
	make DESTDIR="$pkgdir" install || return 1

	find "$pkgdir" \( -name perllocal.pod -o -name .packlist \) -delete
	install -m755 -D "$srcdir"/$pkgname.initd "$pkgdir"/etc/init.d/$pkgname
}

perl() {
	pkgdesc="perl bindings to collectd"
	depends="perl"
	arch="noarch"
	install -d "$subpkgdir"/usr/lib/ "$subpkgdir"/usr/share
	mv "$pkgdir"/usr/lib/perl* "$subpkgdir"/usr/lib/
}

_plugin() {
	local mod=$1
	local desc=${2:-$mod}
	pkgdesc="$desc pluin for collectd"
	depends="collectd"
	install -d "$subpkgdir"/usr/lib/collectd
	mv "$pkgdir"/usr/lib/collectd/$mod.so "$subpkgdir"/usr/lib/collectd/
}

snmp()		{ _plugin snmp; }
_curl()		{ _plugin 'curl*'; }
write_http()	{ _plugin write_http; }
nginx()		{ _plugin nginx; }
apache()	{ _plugin apache; }
postgresql()	{ _plugin postgresql; }
mysql()		{ _plugin mysql; }
network()	{ _plugin network; }
rrdtool()	{ _plugin 'rrd*'; }
sensors()	{ _plugin sensors; }
_python()	{ _plugin python; }
_ipt()		{ _plugin iptables; }
bind()		{ _plugin bind; }
ascent()	{ _plugin ascent; }
redis()		{ _plugin redis; }
dbi()		{ _plugin dbi; }

md5sums="572b346ca47d6466fe48e6e504b41cf0  collectd-5.6.0.tar.bz2
4c0b185623baebc4f4b71b3b47383b3a  gnu_source.patch
8015a61e7cda7abdf60e63eeff2923d2  collectd.initd"
sha256sums="f0ffbbd91fac3682bd324a74b9b4c9eabe781394b303b5cfd457c4cfbe748623  collectd-5.6.0.tar.bz2
5f177512b28b5955231457a8c4468e28dfb667986affebd30c1cba01980e6bff  gnu_source.patch
7d7b20cb0c804d56d4e7ffe60b975b82c194898160674be2de49d1489589f6a3  collectd.initd"
sha512sums="5eecd7fe1619850b29e7853e0ab8f9eb7688d0fdf5687cf04930c75a94181c53d7dc0601a1dfa02417bdeb63cccf09b87acd7129e7f155b8d11147ec46982ed8  collectd-5.6.0.tar.bz2
34b65fc2c21980e047e3d803a8293e8b523ad06e725113065787f1ce3ea81c5ce3e907845b03192770972ece3d8b79dc884c9e7782d428416ee418bc0658daae  gnu_source.patch
296b9f58df7b0da5b2fbf8c6e77cbd08c6673da28e98e67a34cb52dffc26c45d54a95f0bc51a16729d1844752636419fae98a4d3fd744b736568dfc23fab4296  collectd.initd"
