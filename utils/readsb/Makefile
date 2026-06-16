# Copyright (C) 2020 Stijn Tintel <stijn@linux-ipv6.be>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

include $(TOPDIR)/rules.mk

PKG_NAME:=readsb
PKG_VERSION:=3.16.15
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/wiedehopf/readsb/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=7a8c3886c6c548578e9a65f7b45a993bcd49aa52f70d5bffe1e76b4cbca9998f

PKG_LICENSE:=GPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING LICENSE

PKG_MAINTAINER:=Stijn Tintel <stijn@linux-ipv6.be>

PKG_BUILD_DEPENDS:=!USE_GLIBC:argp-standalone
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=gc-sections lto

include $(INCLUDE_DIR)/package.mk

define Package/readsb/default
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libncurses +libzstd +zlib
  TITLE:=Mode-S/ADSB/TIS decoder for various devices
  URL:=https://github.com/Mictronic/readsb
endef

define Package/readsb
  $(call Package/readsb/default)
  TITLE+= (readsb)
  DEPENDS+=+librtlsdr
endef

define Package/viewadsb
  $(call Package/readsb/default)
  TITLE+= (viewadsb)
endef

MAKE_FLAGS += \
	BLADERF=no \
	CFLAGS+="$(TARGET_CFLAGS)" \
	LIBS+="$(TARGET_LDFLAGS)" \
	SDR_OBJ+=sdr_rtlsdr.o \
	UNAME="Linux"

TARGET_CFLAGS += \
	-D_GNU_SOURCE \
	-DENABLE_RTLSDR \
	-DREADSB_SHORT_VERSION='\"$(PKG_VERSION)\"' \
	-DREADSB_SHORT_COMMIT='\"nogit\"'


TARGET_LDFLAGS += \
	$(if $(CONFIG_USE_GLIBC),,-largp) \
	-lm -lncursesw -lpthread -lrtlsdr -lusb-1.0 -lz -lzstd -pthread \
	-Wl,--as-needed

define Package/readsb/conffiles
/etc/config/readsb
endef

define Package/readsb/install
	$(INSTALL_DIR) $(1)/etc/{config,init.d} $(1)/usr/bin
	$(INSTALL_CONF) ./files/readsb.config $(1)/etc/config/readsb
	$(INSTALL_BIN) ./files/readsb.init $(1)/etc/init.d/readsb
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/readsb $(1)/usr/bin
endef

define Package/viewadsb/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/viewadsb $(1)/usr/bin
endef

$(eval $(call BuildPackage,readsb))
$(eval $(call BuildPackage,viewadsb))
