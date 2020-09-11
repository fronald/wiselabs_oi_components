#
# This file and its contents are supplied under the terms of the
# Common Development and Distribution License ("CDDL"), version 1.0.
# You may only use this file in accordance with the terms of version
# 1.0 of the CDDL.
#
# A full copy of the text of the CDDL should have accompanied this
# source.  A copy of the CDDL is also available via the Internet at
# http://www.illumos.org/license/CDDL.
#

#
# Copyright 2016 Alexander Pyhalov
# Copyright 2020 Franklin Ronald
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		openjdk9
OPENJDK_BUILD_NUMBER= 181
COMPONENT_VERSION=	9.0.$(OPENJDK_BUILD_NUMBER)
COMPONENT_REVISION=	1
IPS_COMPONENT_VERSION=	9.0.$(OPENJDK_BUILD_NUMBER)
COMPONENT_SUMMARY=	Open-source implementation of the Java Platform, Standard Edition
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_NAME)-$(COMPONENT_VERSION).tar.gz
COMPONENT_ARCHIVE_HASH= \
	sha256:870ce1031eb736e48c862109156cc59b03f3d05cf01d4c3949840ed9c9c31251
COMPONENT_ARCHIVE_URL= \
	https://us-east.manta.joyent.com/pkgsrc/public/pkg-distfiles/$(COMPONENT_ARCHIVE)
COMPONENT_PROJECT_URL=	http://openjdk.java.net/

CLEAN_PATHS += $(BUILD_DIR)
CLEAN_PATHS += cacerts.out

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/ips.mk
include $(WS_MAKE_RULES)/configure.mk

PATCH_LEVEL=0

#GCC_VERSION=9
CC=gcc
CXX=g++
PATH=$(GCC_ROOT)/bin:$(PATH.gnu)

COMPONENT_PRE_CONFIGURE_ACTION =        ($(CP) -r $(SOURCE_DIR)/* $(@D)) && $(SHELL) $(BUILD_DIR_$(BITS))/common/autoconf/autogen.sh
CONFIGURE_SCRIPT=	$(BUILD_DIR_$(BITS))/configure

CONFIGURE_OPTIONS+=        --with-boot-jdk=$(JAVA_HOME)
CONFIGURE_OPTIONS+=        --disable-option-checking # for --with-libiconv-prefix
CONFIGURE_OPTIONS+=        --with-zlib=system
CONFIGURE_OPTIONS+=        --with-libjpeg=system
CONFIGURE_OPTIONS+=        --with-giflib=bundled
CONFIGURE_OPTIONS+=        --with-native-debug-symbols=none
CONFIGURE_OPTIONS+=        --with-jvm-variants=server
CONFIGURE_OPTIONS+=        --with-debug-level=release
CONFIGURE_OPTIONS+=        --disable-warnings-as-errors
CONFIGURE_OPTIONS+=        --disable-freetype-bundling
CONFIGURE_OPTIONS+=        --with-memory-size=768
CONFIGURE_OPTIONS+=        --disable-precompiled-headers
CONFIGURE_OPTIONS+=        --disable-ccache
CONFIGURE_OPTIONS+=        --disable-gtest
CONFIGURE_OPTIONS+=        --disable-dtrace
CONFIGURE_OPTIONS+=        --disable-hotspot-gtest
CONFIGURE_OPTIONS+=        --with-user-release-suffix=oi-$(COMPONENT_VERSION)
CONFIGURE_OPTIONS+=        --with-update-version=$(OPENJDK_BUILD_NUMBER)
# OpenJDK uses CLFAGS for C and C++ unfortunately
CONFIGURE_OPTIONS+=        --with-extra-cflags="-std=gnu89 -std=gnu++98 -fno-lifetime-dse"
CONFIGURE_OPTIONS+=        --with-extra-cxxflags="-std=gnu++98"
CONFIGURE_OPTIONS+=        --with-extra-ldflags="$(LDFLAGS)"
CONFIGURE_OPTIONS+=        AS="$(GAS)"
CONFIGURE_OPTIONS+=        STRIP=$(STRIP)

COMPONENT_BUILD_ENV+=	USE_GCC=1 ASFLAGS=--$(BITS)

COMPONENT_BUILD_ARGS = JOBS=4

COMPONENT_BUILD_TARGETS = images

CACERTS_DIR=$(WS_TOP)/components/openindiana/ca-certificates
CACERTS_INSTALLED=$(CACERTS_DIR)/build/$(MACH32)/.installed
CACERTS_PROTO_DIR=$(CACERTS_DIR)/build/prototype/$(MACH)

MY_INSTALLED= $(BUILD_DIR_64)/.my_installed

$(MY_INSTALLED): $(BUILD_DIR_64)/.built
	[ -d $(PROTO_DIR)/usr/jdk/instances/openjdk9 ] || $(MKDIR) $(PROTO_DIR)/usr/jdk/instances/openjdk9
	chmod -R u+rw $(PROTO_DIR)/usr/jdk/instances/openjdk9
	cp -a $(@D)/build/solaris-*/images/jdk/* $(PROTO_DIR)/usr/jdk/instances/openjdk9
	ln -s $(PROTO_DIR)/usr/jdk/instances/openjdk9/lib/server/libjvm.so $(PROTO_DIR)/usr/jdk/instances/openjdk9/lib
	$(TOUCH) $@

$(CACERTS_INSTALLED):
	gmake -C $(CACERTS_DIR) install

cacerts.out: $(BUILD_DIR_64)/.built $(CACERTS_INSTALLED)
	rm -f $@
	for cert in $(CACERTS_PROTO_DIR)/etc/certs/CA/*.pem; do                 \
                LC_ALL=en_US.UTF-8 $(BUILD_DIR_64)/build/solaris-x86_64-normal-server-release/jdk/bin/keytool \
                  -noprompt					\
                  -importcert                                   \
                  -keystore $@                                  \
                  -alias `echo $$cert | $(GSED) -e 's,.*/\\([^/]*\\)\\.pem,\\1,'` \
                  -file $$cert                                          \
                  -storepass changeit;                                    \
        done

build:		$(BUILD_64)

install:	$(MY_INSTALLED) cacerts.out

test:		$(NO_TESTS)

# Auto-generated dependencies
REQUIRED_PACKAGES += SUNWcs
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += image/library/libjpeg6
REQUIRED_PACKAGES += runtime/java/openjdk8
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/freetype-2
REQUIRED_PACKAGES += system/library/c++/sunpro
REQUIRED_PACKAGES += system/header/header-audio
REQUIRED_PACKAGES += print/cups
REQUIRED_PACKAGES += $(GXX_RUNTIME_PKG)
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxi
REQUIRED_PACKAGES += x11/library/libxrender
REQUIRED_PACKAGES += x11/library/libxtst
