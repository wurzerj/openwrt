#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

PKG_NAME:=automake
PKG_CPE_ID:=cpe:/a:gnu:automake
PKG_VERSION:=1.17
PKG_API_VERSION:=$(word 2,$(subst ., ,$(PKG_VERSION)))

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@GNU/automake
PKG_HASH:=8920c1fc411e13b90bf704ef9db6f29d540e76d232cb3b2c9f4dc4cc599bd990

include $(INCLUDE_DIR)/host-build.mk

HOST_CONFIGURE_ARGS += \
	--disable-silent-rules

HOST_CONFIGURE_VARS += \
	PERL="$(shell command -v perl)" \
	am_cv_prog_PERL_ithreads=no

define Host/Configure
	(cd $(HOST_BUILD_DIR); $(AM_TOOL_PATHS) STAGING_DIR_HOST="" ./bootstrap)
	$(call Host/Configure/Default)
endef

define Host/Install
	# remove old automake resources to avoid version conflicts
	$(call Host/Uninstall)
	$(call Host/Compile/Default,install)
	mv $(STAGING_DIR_HOST)/bin/aclocal $(STAGING_DIR_HOST)/bin/aclocal.real
	$(INSTALL_BIN) ./files/aclocal $(STAGING_DIR_HOST)/bin
	( \
		api=$(PKG_API_VERSION); \
		while [ "$$$$api" -ge 11 ]; do \
			ln -sf aclocal "$(STAGING_DIR_HOST)/bin/aclocal-1.$$$$api"; \
			api=$$$$(($$$$api - 1)); \
		done; \
	)
endef

define Host/Uninstall
	-$(call Host/Compile/Default,uninstall)
	rm -rf $(STAGING_DIR_HOST)/share/aclocal-[0-9]*
	rm -rf $(STAGING_DIR_HOST)/share/automake-[0-9]*
endef

$(eval $(call HostBuild))
