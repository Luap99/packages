include $(TOPDIR)/rules.mk

PKG_NAME:=podman
PKG_VERSION:=3.0.0-rc1
PKG_RELEASE:=3
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/containers/podman/archive/v$(PKG_VERSION)
PKG_HASH:=674bc3e66affa5d7c1ff2776c4315dbb53e766532b2edb7893e9282b54d3b179

PKG_MAINTAINER:=Daniel Golle <daniel@makrotopia.org>

PKG_BUILD_DEPENDS:=golang/host protobuf/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

GO_PKG:=github.com/containers/podman/
GO_PKG_BUILD_PKG:=github.com/containers/podman/v2/cmd/podman/

GO_PKG_TAGS=seccomp,exclude_graphdriver_devicemapper

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Download/default-registries
  URL:=https://raw.githubusercontent.com/projectatomic/registries/da9a9c87781823f45401ca49da04e269c9e3100e
  URL_FILE:=registries.fedora
  FILE:=registries.fedora-da9a9c8778
  HASH:=bc2b58c209aa8ca35b6814ec9a3c64716d4970b884ade460b65000e56024dfee
endef

define Download/default-policy
  URL:=https://raw.githubusercontent.com/containers/skopeo/362f70b056a1f5d2bd4184527a0ae0d20c4d35d3
  URL_FILE:=default-policy.json
  FILE:=default-policy.json-362f70b056
  HASH:=cddfaa8e6a7e5497b67cc0dd8e8517058d0c97de91bf46fff867528415f2d946
endef

define Package/podman
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Podman
  URL:=https://podman.io
  DEPENDS:=$(GO_ARCH_DEPENDS) +conmon +cni +cni-plugins +btrfs-progs +glib2 +gnupg2 +iptables +libgpg-error +libseccomp +libgpgme +nsenter +zoneinfo-simple
endef

define Package/podman/description
  Podman: A tool for managing OCI containers and pods
endef

define Package/podman/conffiles
/etc/containers
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	$(eval $(call Download,default-registries))
	$(eval $(call Download,default-policy))
endef

define Package/podman/install
	$(call GoPackage/Package/Install/Bin,$(1))
	$(INSTALL_DIR) $(1)/etc/containers
	$(INSTALL_CONF) $(DL_DIR)/default-policy.json-362f70b056 $(1)/etc/containers/policy.json
	$(INSTALL_CONF) $(DL_DIR)/registries.fedora-da9a9c8778 $(1)/etc/containers/registries.conf
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/vendor/github.com/containers/storage/storage.conf $(1)/etc/containers/storage.conf
	$(INSTALL_CONF) ./files/containers.conf $(1)/etc/containers/containers.conf
	$(INSTALL_DIR) $(1)/etc/cni/net.d
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/cni/87-podman-bridge.conflist $(1)/etc/cni/net.d/
endef

$(eval $(call GoBinPackage,podman))
$(eval $(call BuildPackage,podman))
