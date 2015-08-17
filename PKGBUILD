# Maintainer: Massimiliano Torromeo <massimiliano.torromeo@gmail.com>

pkgname=percona-server
pkgver=5.5.15_rel21.0
pkgrel=3
pkgdesc="A backwards-compatible drop-in replacement for MySQL that provides improved performance, diagnostics and instrumentation, and manageability of the server"
arch=('i686' 'x86_64')

depends=('mysql-clients' 'net-tools' 'libaio')
conflicts=('mysql')
provides=('mysql')
optdepends=('perl-dbi' 'perl-dbd-mysql')
makedepends=('cmake' 'openssl' 'zlib')

license=('GPL')
url="http://www.percona.com/software/percona-server/"
options=('!libtool' 'emptydirs')
backup=('etc/mysql/my.cnf')
install=percona.install
source=("http://www.percona.com/redir/downloads/Percona-Server-${pkgver%.*_*}/Percona-Server-${pkgver/_rel/-}/source/Percona-Server-${pkgver/_/-}.tar.gz"
        'mysqld'
        'my.cnf')

build() {
	cd "${srcdir}/Percona-Server-${pkgver/_/-}"
	sed -i 's|ADD_SUBDIRECTORY(libmysqld/examples)|# ADD_SUBDIRECTORY(libmysqld/examples)|' CMakeLists.txt

	cd "${srcdir}"
	rm -rf build
	mkdir build
	cd build

	# CFLAGS/CXXFLAGS as suggested upstream
	CFLAGS="-fPIC ${CFLAGS} -fno-strict-aliasing -DBIG_JOINS=1 -fomit-frame-pointer" \
	CXXFLAGS="-fPIC ${CXXFLAGS} -fno-strict-aliasing -DBIG_JOINS=1 -felide-constructors -fno-rtti" \

	cmake "${srcdir}/Percona-Server-${pkgver/_/-}" \
		-DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_INSTALL_PREFIX=/usr \
		-DSYSCONFDIR=/etc/mysql \
		-DMYSQL_DATADIR=/var/lib/mysql \
		-DMYSQL_UNIX_ADDR=/var/run/mysqld/mysqld.sock \
		-DDEFAULT_CHARSET=utf8 \
		-DDEFAULT_COLLATION=utf8_general_ci \
		-DENABLED_LOCAL_INFILE=ON \
		-DINSTALL_INFODIR=share/mysql/docs \
		-DINSTALL_MANDIR=share/man \
		-DINSTALL_PLUGINDIR=/usr/lib/mysql/plugin \
		-DINSTALL_SCRIPTDIR=bin \
		-DINSTALL_INCLUDEDIR=include/mysql \
		-DINSTALL_DOCREADMEDIR=share/mysql \
		-DINSTALL_SUPPORTFILESDIR=share/mysql \
		-DINSTALL_MYSQLSHAREDIR=share/mysql \
		-DINSTALL_DOCDIR=share/mysql/docs \
		-DINSTALL_SHAREDIR=share/mysql \
		-DWITH_READLINE=ON \
		-DWITH_ZLIB=system \
		-DWITH_SSL=system \
		-DWITH_LIBWRAP=OFF \
		-DWITH_MYSQLD_LDFLAGS="${LDFLAGS}" \
		-DWITH_EXTRA_CHARSETS=complex \
		-DWITH_EMBEDDED_SERVER=ON \
		-DWITH_INNOBASE_STORAGE_ENGINE=1 \
		-DWITH_PARTITION_STORAGE_ENGINE=1 \
		-DWITHOUT_EXAMPLE_STORAGE_ENGINE=1 \
		-DWITHOUT_ARCHIVE_STORAGE_ENGINE=1 \
		-DWITHOUT_BLACKHOLE_STORAGE_ENGINE=1 \
		-DWITHOUT_FEDERATED_STORAGE_ENGINE=1

	make
}

package() {
	cd "${srcdir}"/build
	make DESTDIR=${pkgdir} install

	install -Dm644 ${srcdir}/my.cnf ${pkgdir}/etc/mysql/my.cnf
	install -Dm755 ${srcdir}/mysqld ${pkgdir}/etc/rc.d/mysqld

	# provided by libmysqlclient
	rm ${pkgdir}/usr/bin/mysql_config
	rm ${pkgdir}/usr/lib/libmysql*
	rm -r ${pkgdir}/usr/include/
	rm ${pkgdir}/usr/share/man/man1/{mysql_config,mysql_client_test_embedded,mysqltest_embedded}.1

	# provided by mysql-clients
	rm ${pkgdir}/usr/bin/{mysql,mysqladmin,mysqlcheck,mysqldump,mysqlimport,mysqlshow,mysqlslap}
	rm ${pkgdir}/usr/share/man/man1/{mysql,mysqladmin,mysqlcheck,mysqldump,mysqlimport,mysqlshow,mysqlslap}.1

	# not needed
	rm -r ${pkgdir}/usr/{data,mysql-test,sql-bench}
	rm "${pkgdir}"/usr/share/man/man1/mysql-test-run.pl.1

	install -dm700 "${pkgdir}"/var/lib/mysql
}

md5sums=('d04b6d1cc863f121f5d1eac8bc618331'
         '243864805611764a7e5883c1dba7afd8'
         '1c949c0dbea5206af0db14942d9927b6')
