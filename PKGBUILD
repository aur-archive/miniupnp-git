# Maintainer: Adrian Perez de Castro <aperez@igalia.com>
pkgname='miniupnp-git'
pkgver=1.8.507.g81dc08d
pkgrel=1
pkgdesc='MiniUPnP suite: SSDP daemon, IGD/NAT-PMP/PCP gateway, and IGD control point'
arch=('arm' 'x86_64' 'i686')
url='http://http://miniupnp.tuxfamily.org/'
license=('BSD')
conflicts=('miniupnpd' 'minissdpd' 'miniupnpc')
provides=('miniupnpd' 'minissdpd' 'miniupnpc')
backup=('etc/conf.d/minissdpd'
	'etc/miniupnpd/miniupnpd.conf')
source=("${pkgname}::git+https://github.com/miniupnp/miniupnp.git"
	miniupnpd-1.8.20140401-foreground.patch
	miniupnpd.service
	minissdpd-nodaemon-flag.patch
	minissdpd
	minissdpd.service)

pkgver () {
	cd "${srcdir}/${pkgname}"
	git describe --tags --always --long | sed 's/^miniupnpc_//;s/[_-]/./g'
}

prepare () {
	cd "${srcdir}/${pkgname}"
	patch -p1 < "${srcdir}/minissdpd-nodaemon-flag.patch"

	cd "${srcdir}/${pkgname}/miniupnpd"
	patch -p0 < "${srcdir}/miniupnpd-1.8.20140401-foreground.patch"
}

build () {
	make -C "${srcdir}/${pkgname}/minissdpd"
	make -C "${srcdir}/${pkgname}/miniupnpc"
	CONFIG_OPTIONS="--ipv6 --igd2 --leasefile --portinuse" \
		make -C "${srcdir}/${pkgname}/miniupnpd" -f Makefile.linux
}

package () {
	cd "${pkgname}"

	# manpages
	install -m 755 -d "${pkgdir}/usr/share/man/man1" \
	                  "${pkgdir}/usr/share/man/man8" \
	                  "${pkgdir}/usr/share/man/man3"
	install -m 644 -t "${pkgdir}/usr/share/man/man1" minissdpd/minissdpd.1
	install -m 644 -t "${pkgdir}/usr/share/man/man8" miniupnpd/miniupnpd.8
	install -m 644 -t "${pkgdir}/usr/share/man/man3" miniupnpc/man3/miniupnpc.3

	# Programs
	make PREFIX="${pkgdir}" -C miniupnpc install
	make PREFIX="${pkgdir}" -C miniupnpd -f Makefile.linux install

	install -m 755 -d "${pkgdir}/sbin"
	install -m 700 -t "${pkgdir}/sbin" \
		minissdpd/minissdpd

	# Configuration files
	install -m 755 -d "${pkgdir}/etc/conf.d"
	install -m 600 -t "${pkgdir}/etc/conf.d" \
		"${srcdir}/minissdpd"

  sed -i 's:/s\?bin/iptables:/usr/bin/iptables:
          s:eth0:"`cat /etc/miniupnpd/miniupnpd.conf | '"awk -F= '/^ext_ifname/ { print \$2 }'"'`":' "${pkgdir}"/etc/miniupnpd/*.sh
  sed -i -e "s/^uuid=[-0-9a-f]*/uuid=00000000-0000-0000-0000-000000000000/
             s/make genuuid/uuidgen/" "${pkgdir}/etc/miniupnpd/miniupnpd.conf"

	# systemd unit files
	install -m 755 -d "${pkgdir}/usr/lib/systemd/system"
	install -m 644 -t "${pkgdir}/usr/lib/systemd/system" \
		"${srcdir}/minissdpd.service" \
		"${srcdir}/miniupnpd.service"

	# Cleanups
	rm -rf "${pkgdir}/etc/init.d"
}

sha1sums=('SKIP'
          '329d2851ef17a359c241f36e6e55b34b13a1e977'
          '9aea052ab8bb5793270f261c205df1fb38093e0c'
          'e9ec498296f27040195fa2365be712de7df2defc'
          '8274b31638f343875a659ac6ea8c9702011b305a'
          'f7d5ac5fa38e0096159421fc0b57d41a188d0ded')
