# Contributor:
# Maintainer:
pkgname=dbmail
pkgver=3.2.3
pkgrel=0
pkgdesc="Fast and scalable sql based email services"
url="http://www.dbmail.org"
arch="all"
license="GPL2"
depends=""
depends_dev=""
makedepends="$depends_dev asciidoc xmlto automake autoconf openldap-dev libsieve-dev glib-dev gmime-dev mhash-dev libevent-dev libzdb-dev bsd-compat-headers"
install="$pkgname.pre-install"
subpackages="$pkgname-doc $pkgname-auth-ldap:auth_ldap $pkgname-contrib"
source="http://www.dbmail.org/download/3.2/dbmail-$pkgver.tar.gz
	dbmail-imapd.initd
	dbmail-lmtpd.initd
	dbmail-pop3d.initd
	dbmail-timsieved.initd
	"

_builddir="$srcdir"/$pkgname-$pkgver
prepare() {
	local i
	cd "$_builddir"
	for i in $source; do
		case $i in
		*.patch) msg $i; patch -p1 -i "$srcdir"/$i || return 1;;
		esac
	done
	sed -i -e "s:nobody:dbmail:" dbmail.conf
	sed -i -e "s:nogroup:dbmail:" dbmail.conf
	sed -i -e "s:/var/run:/var/run/dbmail:" dbmail.conf
}

build() {
	cd "$_builddir"
	./configure \
		--prefix=/usr \
		--sysconfdir=/etc \
		--mandir=/usr/share/man \
		--localstatedir=/var \
		--with-sieve \
		--with-ldap \
		--disable-static \
		|| return 1
	make || return 1
	
	cd "$_builddir/man"
	make || return 1
}

package() {
	cd "$_builddir"
	make DESTDIR="$pkgdir" install || return 1

	install -d -m750 -o dbmail -g dbmail "$pkgdir"/var/lib/dbmail

	install -m755 -D "$srcdir"/$pkgname-imapd.initd \
		"$pkgdir"/etc/init.d/$pkgname-imapd || return 1
	install -m755 -D "$srcdir"/$pkgname-lmtpd.initd \
		"$pkgdir"/etc/init.d/$pkgname-lmtpd || return 1
	install -m755 -D "$srcdir"/$pkgname-pop3d.initd \
		"$pkgdir"/etc/init.d/$pkgname-pop3d || return 1
	install -m755 -D "$srcdir"/$pkgname-timsieved.initd \
		"$pkgdir"/etc/init.d/$pkgname-timsieved || return 1
	install -m644 -D "$srcdir"/$pkgname-$pkgver/$pkgname.conf \
		"$pkgdir"/etc/$pkgname.conf || return 1
}

auth_ldap() {
	pkgdesc="$pkgname ldap authentication"
	install -d "$subpkgdir"/usr/lib/dbmail
	mv "$pkgdir"/usr/lib/dbmail/libauth_ldap* \
		"$subpkgdir"/usr/lib/dbmail/ || return 1
	install -D "$srcdir"/$pkgname-$pkgver/dbmail.schema \
		"$subpkgdir"/etc/openldap/schema/dbmail.schema || return 1
}

doc() {
	arch="noarch"
	cd "$_builddir/man"
	make DESTDIR="$subpkgdir" install || return 1
	
	cd "$srcdir"/$pkgname-$pkgver
	docdir="$subpkgdir"/usr/share/doc/$pkgname
	mkdir -p "$docdir"

	for f in AUTHORS BUGS ChangeLog README* INSTALL NEWS THANKS UPGRADING doc/*; do
		if [ -s $f ]; then
			install -m0644 "$f" "$docdir" || return 1
		fi
	done

	cp -R "$srcdir"/$pkgname-$pkgver/sql "$docdir"/ || return 1
	cp -R "$srcdir"/$pkgname-$pkgver/test-scripts "$docdir"/ || return 1

	default_doc
}

contrib() {
	arch="noarch"
	pkgdesc="$pkgname contributed files"
	mkdir -p "$subpkgdir"/usr/share/$pkgname/contrib
	cp -r "$srcdir"/$pkgname-$pkgver/contrib \
		"$subpkgdir"/usr/share/$pkgname/ || return 1
}

md5sums="4f140751642559395dfe3a6bb2cc9300  dbmail-3.2.3.tar.gz
f4e5dc381a84c98ac3ec99fb97e9edbb  dbmail-imapd.initd
2890edd8bb0841c58473001b01f44ced  dbmail-lmtpd.initd
499aac58fd71b55de4c01fba3a6dfdd5  dbmail-pop3d.initd
1415fab716c37179ea7f9535acf56bb6  dbmail-timsieved.initd"
sha256sums="fd4d90e3e5ddb0c3fbdaa766d19d2464b5027a8c8d0b0df614418a3aac811832  dbmail-3.2.3.tar.gz
0fafa39ae7c7aef485970e40c7780675495ed17729fd20a1afed67f16418e4fe  dbmail-imapd.initd
ecbc30265454847e727afe823a5b35113e1161f61491f73a5773a3f9c2b89bb6  dbmail-lmtpd.initd
72c10c12b5436804b7d7629d6bb7a85cc812adc38509887644d3f9e91f0a78be  dbmail-pop3d.initd
2c84911f2dec3f971ca6d2cecaa1ffd600aefe9f0057afb06137ce54bb49a20c  dbmail-timsieved.initd"
sha512sums="29cc89830278f2ff28a4221fc81d4ff13cc371fbca7fb51d5376e9243234220b552cb4b6ec035c150e6cfa178435c4f36b9ce132fc02334bc6cdeb1a41c8ed85  dbmail-3.2.3.tar.gz
6ebaa811a22173cdc7b407d90ebc20cedbfe77fd1e3541a03fca9101833f5b82be68eb0c795bed87f87a73c90701df6548ec3a7c08d0e68a4136387df6116587  dbmail-imapd.initd
aa5a567fe4bb7949e795cc6004338d995919b46229e79462ba5106c7d1ab5898ae0885047a4c5f1ce742653e32545c623a377274bdc49ee53107dc7872d6d449  dbmail-lmtpd.initd
ab66aa08150d6c200f6653fc969403afc0ce4a4322b1cc225b83f7ad87b958be120684f4bb60125b0dd685f07dd2600875c0bb463d58b4cbb6b897007cbd95f6  dbmail-pop3d.initd
4fef0321ab51538c738c11e22a286a6081e3a834eccb3518ba669d7d85c569c4bc2865a6082c247693a80fad1d5495e3be8d0a38cf519307494fcf1c8ed797cc  dbmail-timsieved.initd"
