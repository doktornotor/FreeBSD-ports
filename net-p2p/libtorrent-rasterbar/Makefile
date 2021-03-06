# Created by: Doug Barton <dougb@FreeBSD.org>
# $FreeBSD$

PORTNAME=	libtorrent-rasterbar
PORTVERSION=	1.1.0
CATEGORIES?=	net-p2p ipv6
MASTER_SITES=	https://github.com/arvidn/libtorrent/releases/download/libtorrent-${PORTVERSION:R:S/./_/g}/

MAINTAINER=	matthew@reztek.cz
COMMENT?=	C++ library implementing a BitTorrent client

LICENSE=	BSD3CLAUSE

LIB_DEPENDS+=	libboost_chrono.so:devel/boost-libs \
		libboost_random.so:devel/boost-libs \
		libboost_system.so:devel/boost-libs

USES+=		compiler:c++11-lang iconv:wchar_t libtool pathfix pkgconfig ssl
GNU_CONFIGURE=	yes
USE_LDCONFIG=	yes
TEST_TARGET=	check

CONFIGURE_ARGS=	--disable-static \
		--enable-dht \
		--enable-encryption \
		--enable-pool-allocators \
		--enable-deprecated-functions \
		--with-boost=${LOCALBASE} \
		--with-boost-system=boost_system \
		--with-libiconv \
		--with-openssl=${OPENSSLBASE}

SHLIB_VER=	9
PLIST_SUB+=	SHLIB_VER="${SHLIB_VER}"

DOCFILES=	AUTHORS ChangeLog
PORTDOCS=	${DOCFILES} docs/*
PORTEXAMPLES=	*.cpp

OPTIONS_DEFINE=	DEBUG DOCS EXAMPLES TEST
TEST_DESC=	Build and run tests (increases library size)

DEBUG_CONFIGURE_ENABLE=	debug
TEST_CONFIGURE_ENABLE=	tests

.include <bsd.port.options.mk>

.if defined(SLAVE_PORT) && ${SLAVE_PORT:tl} == "yes" && defined(USE_PYTHON)
CONFIGURE_ARGS+=	--enable-python-binding \
			--with-boost-python=boost_python
.else
CONFIGURE_ARGS+=	--disable-python-binding
.endif

post-patch:
	@${REINPLACE_CMD} -e 's|/usr/local/include|${PREFIX}/include|' \
		${WRKSRC}/Jamfile

post-configure:
	${FIND} ${WRKSRC} -name Makefile -o -name link_flags | \
		${XARGS} ${REINPLACE_CMD} 's| *-R${LOCALBASE}/lib||'

post-install:
.if defined(SLAVE_PORT) && ${SLAVE_PORT:tl} == "yes" && defined(USE_PYTHON)
	${STRIP_CMD} ${STAGEDIR}${PYTHON_SITELIBDIR}/libtorrent.so
.else
	${STRIP_CMD} \
	${STAGEDIR}${PREFIX}/lib/libtorrent-rasterbar.so.${SHLIB_VER}
.endif

post-install-DOCS-on:
	@${MKDIR} ${STAGEDIR}${DOCSDIR}
	${INSTALL_MAN} ${DOCFILES:S|^|${WRKSRC}/|} ${STAGEDIR}${DOCSDIR}
	(cd ${WRKSRC} && ${COPYTREE_SHARE} docs ${STAGEDIR}${DOCSDIR})

post-install-EXAMPLES-on:
	@${MKDIR} ${STAGEDIR}${EXAMPLESDIR}
	${INSTALL_DATA} ${WRKSRC}/examples/${PORTEXAMPLES} \
		${STAGEDIR}${EXAMPLESDIR}

.include <bsd.port.mk>
