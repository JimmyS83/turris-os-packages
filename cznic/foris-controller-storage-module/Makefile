#
# Copyright (C) 2017 CZ.NIC z. s. p. o. (https://www.nic.cz/)
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=foris-controller-storage-module
PKG_VERSION:=0.1
PKG_RELEASE:=5
PKG_SOURCE_SUBDIR:=$(PKG_NAME)
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://gitlab.labs.nic.cz/turris/foris-controller-storage-module.git
PKG_SOURCE_VERSION:=6948f63c189a97b2f63a55988d68427137f04515
PKG_SOURCE:=$(PKG_NAME)-$(PKG_SOURCE_VERSION).tar.gz
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)

PKG_LICENSE:=GPL-3.0
PKG_LICENSE_FILE:=LICENSE

include $(INCLUDE_DIR)/package.mk

$(call include_mk, python-package.mk)

define Package/$(PKG_NAME)
	TITLE:=$(PKG_NAME)
	DEPENDS:=\
		+foris-controller +e2fsprogs +coreutils-stat +rsync +partx-utils
	MAINTAINER:=Stepan Henek <stepan.henek@nic.cz>
endef

define Package/$(PKG_NAME)/description
 This is a netmetr module for foris-controller
endef

define Package/$(PKG_NAME)/conffiles
/etc/config/storage
endef

define Build/Compile
	$(call Build/Compile/PyMod,,install --prefix=/usr --root=$(PKG_INSTALL_DIR))
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)$(PYTHON_PKG_DIR) $(1)/usr/libexec $(1)/etc/init.d $(1)/etc/config
	echo -e "config srv 'srv'\\n\\toption uuid ''\\n\\toption old_uuid ''" > $(1)/etc/config/storage
	find $(PKG_INSTALL_DIR)$(PYTHON_PKG_DIR) -mindepth 1 -maxdepth 1 \( -type f -o -type d \) -exec $(CP) \{\} $(1)$(PYTHON_PKG_DIR)/ \;
	$(RM) $(1)/usr/lib/python2.7/site-packages/foris_controller_modules/__init__.py
	$(RM) $(1)/usr/lib/python2.7/site-packages/foris_controller_backends/__init__.py
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/scripts/srv.init $(1)/etc/init.d/srv
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/scripts/format_and_set_srv.sh $(1)/usr/libexec
endef

define Package/$(PKG_NAME)/postrm
#!/bin/sh
set -x
[ -n "$${IPKG_INSTROOT}" ] || /etc/init.d/foris-controller restart
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
set -x
if [ -z "$${IPKG_INSTROOT}" ]; then
	/etc/init.d/foris-controller restart
	/etc/init.d/srv enable
	mkdir -p /srv
fi
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
