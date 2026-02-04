include $(TOPDIR)/rules.mk

PKG_NAME:=bandix
PKG_VERSION:=0.12.3
PKG_RELEASE:=1

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=timsaya

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/timsaya/bandix.git
PKG_SOURCE_VERSION:=v0.12.3
PKG_MIRROR_HASH:=skip

PKG_BUILD_DEPENDS:=rust/host

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/rust/rust-package.mk

define Package/$(PKG_NAME)
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Bandix - Network traffic monitoring tool
	DEPENDS:=+libbpf
endef

define Package/$(PKG_NAME)/description
	Bandix core service for network traffic monitoring
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/bandix $(1)/usr/bin/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/bandix.init $(1)/etc/init.d/bandix

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/bandix.config $(1)/etc/config/bandix

	$(INSTALL_DIR) $(1)/lib/upgrade/keep.d
	$(INSTALL_DATA) ./files/bandix.keep $(1)/lib/upgrade/keep.d/bandix
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
