include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=dsl-cpe-mei-vrx
PKG_SOURCE_VERSION:=1.9.1
PKG_BASE_NAME:=drv_mei_cpe
PATCH_DIR:=./patches-$(PKG_SOURCE_VERSION)
PKG_RELEASE:=1
PKG_INSTALL:=1
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/prplfoundation/intel_dsl_cpe_mei.git
DSL_BIN_DIR=$(VENDOR_PATH)/bin
DSL_INITD_DIR=$(VENDOR_PATH)/etc/init.d
DSL_FW_DIR=/lib/firmware

PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

DSL_NETLINK_ID=$(strip $(subst ",, $(CONFIG_dsl-cpe-mei-vrx_DSL_NETLINK_ID)))

ifeq ($(DSL_NETLINK_ID),)
	DSL_NETLINK_ID=28
endif

define Package/dsl-cpe-mei-vrx
  SECTION:=intel
  CATEGORY:=Intel
  TITLE:=DSL CPE MEI Driver for VRX Family
  URL:=http://www.intel.com/
  MAINTAINER:=Intel Corporation
  DEPENDS:=+kmod-ifxos +kmod-dsl-cpe-mei-vrx +PACKAGE_kmod-vrx518-dp-mod:kmod-vrx518-dp-mod \
  	+PACKAGE_kmod-vrx518_tc_drv:kmod-vrx518_tc_drv +PACKAGE_kmod-vrx318-dp-mod:kmod-vrx318-dp-mod \
	+PACKAGE_safeclibs:safeclibs
  MENU:=1
endef

define KernelPackage/dsl-cpe-mei-vrx
  SUBMENU:=Intel
  TITLE:=DSL CPE MEI Driver for VRX Family
  URL:=http://www.intel.com/
  MAINTAINER:=Intel Corporation
  DEPENDS:=+kmod-ifxos +PACKAGE_kmod-vrx518-dp-mod:kmod-vrx518-dp-mod +PACKAGE_kmod-vrx318-dp-mod:kmod-vrx318-dp-mod \
	+PACKAGE_kmod-vrx518_tc_drv:kmod-vrx518_ep +PACKAGE_safeclibs:safeclibs
  KCONFIG:=CONFIG_LTQ_DSL_CPE_MEI_VRX=y
  FILES:=$(PKG_BUILD_DIR)/src/$(PKG_BASE_NAME)$(PKG_MODULE_EXTENSION)
  AUTOLOAD:=$(call AutoLoad,44,$(PKG_BASE_NAME))
endef

define Package/dsl-cpe-mei-vrx/config
	source "$(SOURCE)/Config.in"
endef

define Package/dsl-cpe-mei-vrx/description
	Intel MEI CPE Driver
	Supported Devices:
		- VRX200 Family
		- VRX300 Family
		- VRX500 Family
endef

define KernelPackage/dsl-cpe-mei-vrx/description
	Intel MEI CPE Kernel Module Driver
endef


ifeq ($(CONFIG_PACKAGE_safeclibs),y)
CONFIGURE_ARGS += \
	--enable-safec-support=yes \
	--enable-safec-include="-I$(STAGING_DIR)/usr/include" \
	--enable-safec-library="-L$(STAGING_DIR)/usr/lib"
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR9),y)
CONFIGURE_ARGS += --enable-device=vr9
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR10),y)
CONFIGURE_ARGS += --enable-device=vr10
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR10_320),y)
CONFIGURE_ARGS += --enable-device=vr10_320
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR11),y)
CONFIGURE_ARGS += --enable-device=vr11
else
CONFIGURE_ARGS += --enable-device=vr9
MEI_DRV_CFLAGS=-DMEI_SUPPORT_DSM=1
endif

ifeq ($(CONFIG_PTM_VECTORING),y)
MEI_DRV_CFLAGS=-DMEI_SUPPORT_DSM=1
else ifeq ($(CONFIG_VRX320_PTM_VECTORING),y)
MEI_DRV_CFLAGS=-DMEI_SUPPORT_DSM=1
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR11),y)
MEI_DRV_CFLAGS=-DMEI_SUPPORT_DSM=1
else
MEI_DRV_CFLAGS=-DMEI_SUPPORT_DSM=0
endif

ifeq ($(CONFIG_TARGET_x86),y)
MEI_DRV_CFLAGS+=-DMEI_TARGET_x86=1
else
MEI_DRV_CFLAGS+=-DMEI_TARGET_x86=0
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_IRQ_POLLING_PASSIVE),y)
MEI_DRV_CFLAGS += -DIRQ_POLLING_FORCE=0
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_IRQ_POLLING_ACTIVE),y)
MEI_DRV_CFLAGS += -DIRQ_POLLING_FORCE=99
endif

MEI_DRV_CFLAGS += -I$(STAGING_DIR)/include

CONFIGURE_ARGS += \
	--enable-kernelincl="$(LINUX_DIR)/include"
CONFIGURE_ARGS += \
	--enable-error_print \
	--enable-ifxos-include="-I$(STAGING_DIR)/usr/include/ifxos" \
	--enable-ifxos-library="-L$(STAGING_DIR)/usr/lib"

ifeq ($(CONFIG_mips),y)
CONFIGURE_ARGS += --enable-add_drv_cflags="${MEI_DRV_CFLAGS} -fno-pic -mlong-calls -O2 -g0"
else
CONFIGURE_ARGS += --enable-add_drv_cflags="${MEI_DRV_CFLAGS} -fno-pic -O2 -g0"
endif

ifdef CONFIG_dsl-cpe-mei-vrx_DSL_NETLINK_ID
CONFIGURE_ARGS += \
	--with-dbg-netlink-id="$(DSL_NETLINK_ID)"
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_MODEL_DEBUG),y)
	CONFIGURE_ARGS += --enable-debug
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_USE_KERNEL_BUILD_IN),y)
CONFIGURE_ARGS += \
	--enable-ifxos_drv=no \
	--enable-lkm=no
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_USE_PCI_SLAVE_DOWNLOAD),y)
CONFIGURE_ARGS += \
	--enable-pci-slave=yes

ifeq ($(CONFIG_dsl-cpe-mei-vrx_USE_PCI_SLAVE_ADDR_RANGE_ADTRAN),y)
CONFIGURE_ARGS += \
	--enable-pci-slave-addr=adtran
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_USE_PCI_SLAVE_ADDR_RANGE_BONDING),y)
CONFIGURE_ARGS += \
	--enable-pci-slave-addr=bonding
else
CONFIGURE_ARGS += \
	--enable-pci-slave-addr=bonding
endif
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_DTM_EMULATION),y)
CONFIGURE_ARGS += --enable-emulation-configuration=yes
else
CONFIGURE_ARGS += --enable-emulation-configuration=no
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_MEI_PREDEF_DBG_BAR),y)
CONFIGURE_ARGS += --enable-predef-dbg-bar=yes
else
CONFIGURE_ARGS += --enable-predef-dbg-bar=no
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_MEI_DEBUG_STREAM_SUPPORT),y)
CONFIGURE_ARGS += --enable-debug-stream-support=yes
else
CONFIGURE_ARGS += --enable-debug-stream-support=no
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_PPA_NO),y)
CONFIGURE_ARGS += --disable-ppa-callbacks
CONFIGURE_ARGS += --disable-ppa-callbacks-tc
endif
ifeq ($(CONFIG_dsl-cpe-mei-vrx_PPA_WITHOUT_TC_CB),y)
CONFIGURE_ARGS += --disable-ppa-callbacks-tc
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_USE_MEI_ADD_EXT_DRV_CFLAGS),y)
CONFIGURE_ARGS += --enable-add_ext_drv_cflags=$(CONFIG_dsl-cpe-mei-vrx_MEI_ADD_EXT_DRV_CFLAGS)
endif

CONFIGURE_ARGS += \
	--enable-linux-26 \
	--enable-kernelbuild="$(LINUX_DIR)" \
	ARCH=$(LINUX_KARCH)
PKG_MODULE_EXTENSION=.ko

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/{lib,include}
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_api_intern.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_api_atm_ptm_intern.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_interface.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/drv_mei_cpe_config.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/cmv_message_format.h $(1)/usr/include/
endef

define Package/dsl-cpe-mei-vrx/install
	$(INSTALL_DIR) $(1)/$(DSL_INITD_DIR)
	$(INSTALL_BIN) ./files/ltq_load_cpe_mei_drv.sh $(1)/$(DSL_INITD_DIR)/
	$(SED) "s:@dsl_bin_dir@:$(DSL_BIN_DIR):g" $(1)/$(DSL_INITD_DIR)/ltq_load_cpe_mei_drv.sh

ifeq ($(CONFIG_dsl-cpe-mei-vrx_DTM_EMULATION),y)
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/ltq_load_cpe_mei_drv_standalone.sh $(1)/etc/init.d/
	$(SED) "s:@dsl_init_dir@:$(DSL_INITD_DIR):g" $(1)/etc/init.d/ltq_load_cpe_mei_drv_standalone.sh

	$(INSTALL_DIR) $(1)/$(DSL_BIN_DIR)
	$(INSTALL_BIN) ./files/ltq_init_dsl_phy_emulation.sh $(1)/etc/init.d/
ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR10),y)
	$(SED) "s:@dsl_fwbin_dir@:/lib/firmware:g" $(1)/etc/init.d/ltq_init_dsl_phy_emulation.sh
	$(SED) "s:@dsl_pmcs_cfg@:0 7 9:g" $(1)/etc/init.d/ltq_init_dsl_phy_emulation.sh
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR11),y)
	$(SED) "s:@dsl_fwbin_dir@:/firmware:g" $(1)/etc/init.d/ltq_init_dsl_phy_emulation.sh
	$(SED) "s:@dsl_pmcs_cfg@:0 8 9:g" $(1)/etc/init.d/ltq_init_dsl_phy_emulation.sh
endif
	$(SED) "s:@dsl_bin_dir@:$(DSL_BIN_DIR):g" $(1)/etc/init.d/ltq_init_dsl_phy_emulation.sh
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_DTM_STANDALONE),y)
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/ltq_load_cpe_mei_drv_standalone.sh $(1)/etc/init.d/
	$(SED) "s:@dsl_init_dir@:$(DSL_INITD_DIR):g" $(1)/etc/init.d/ltq_load_cpe_mei_drv_standalone.sh
ifneq ($(CONFIG_PACKAGE_dsl-cpe-api-vrx),y)
	$(INSTALL_BIN) ./files/ltq_init_dsl_phy_standalone.sh $(1)/etc/init.d/
ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR10),y)
	$(SED) "s:@dsl_fwbin_dir@:/lib/firmware:g" $(1)/etc/init.d/ltq_init_dsl_phy_standalone.sh
	$(SED) "s:@dsl_pmcs_cfg@:0 7 9:g" $(1)/etc/init.d/ltq_init_dsl_phy_standalone.sh
else ifeq ($(CONFIG_dsl-cpe-mei-vrx_DEVICE_VR11),y)
	$(SED) "s:@dsl_fwbin_dir@:/firmware:g" $(1)/etc/init.d/ltq_init_dsl_phy_standalone.sh
	$(SED) "s:@dsl_pmcs_cfg@:0 8 9:g" $(1)/etc/init.d/ltq_init_dsl_phy_standalone.sh
endif
	$(SED) "s:@dsl_bin_dir@:$(DSL_BIN_DIR):g" $(1)/etc/init.d/ltq_init_dsl_phy_standalone.sh
endif
endif

	$(INSTALL_DIR) $(1)/$(DSL_BIN_DIR)

# Copy configuration files to rootfs
ifeq ($(CONFIG_dsl-cpe-mei-vrx_MODEL_DEBUG),y)
	@- [ -e ./files/dsl_dbg.cfg ] && $(INSTALL_DATA) ./files/dsl_dbg.cfg $(1)/$(DSL_BIN_DIR)/dsl.cfg
else
	@- [ -e ./files/dsl_rel.cfg ] && $(INSTALL_DATA) ./files/dsl_rel.cfg $(1)/$(DSL_BIN_DIR)/dsl.cfg
endif

	@- [ -e ./files/dsl_auto.cfg ] && $(INSTALL_DATA) ./files/dsl_auto.cfg $(1)/$(DSL_BIN_DIR)
	$(SED) "s:@dsl_bin_dir@:$(DSL_BIN_DIR):g" $(1)/$(DSL_BIN_DIR)/dsl_auto.cfg
	@- [ -e ./files/dsl_auto_mei.cfg ] && $(INSTALL_DATA) ./files/dsl_auto_mei.cfg $(1)/$(DSL_BIN_DIR)



	# Set support flag for L2 vectoring
ifeq ($(CONFIG_PTM_VECTORING),y)
	$(SED) "s:@vectoring_l2_cfg@:1:g" $(1)/$(DSL_BIN_DIR)/dsl_auto_mei.cfg;
else ifeq ($(CONFIG_VRX320_PTM_VECTORING),y)
	$(SED) "s:@vectoring_l2_cfg@:1:g" $(1)/$(DSL_BIN_DIR)/dsl_auto_mei.cfg;
else
	$(SED) "s:@vectoring_l2_cfg@:0:g" $(1)/$(DSL_BIN_DIR)/dsl_auto_mei.cfg;
endif


	$(INSTALL_BIN) ./files/inst_drv_mei_cpe.sh $(1)/$(DSL_BIN_DIR)
	$(SED) "s:@dsl_bin_dir@:$(DSL_BIN_DIR):g" $(1)/$(DSL_BIN_DIR)/inst_drv_mei_cpe.sh
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/scripts/alias_drv_mei_cpe.sh $(1)/$(DSL_BIN_DIR)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/scripts/enable_ARC_JTAG.sh $(1)/$(DSL_BIN_DIR)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/mei_cpe_drv_test $(1)/$(DSL_BIN_DIR)
ifeq ($(CONFIG_dsl-cpe-mei-vrx_MEI_DEBUG_STREAM_SUPPORT),y)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/mei_cpe_drv_dbg_strm_dmp $(1)/$(DSL_BIN_DIR)
endif

ifeq ($(CONFIG_dsl-cpe-mei-vrx_USE_KERNEL_BUILD_IN),y)
	$(INSTALL_DIR) $(1)/$(DSL_FW_DIR)
	$(CP) -s $(PKG_BUILD_DIR)/src/$(PKG_BASE_NAME).o $(LINUX_DIR)/drivers/char/ltq_dsl_cpe_mei_vrx.o
else
	-rm -f $(LINUX_DIR)/drivers/char/ltq_dsl_cpe_mei_vrx.o
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/$(PKG_BASE_NAME)$(PKG_MODULE_EXTENSION) $(1)/$(DSL_BIN_DIR)
	$(INSTALL_DIR) $(1)/$(MODULES_SUBDIR)
ifneq ($(CONFIG_LANTIQ_OPENWRT_FIRMWARE),y)
	ln -snf /$(MODULES_SUBDIR)/$(PKG_BASE_NAME).$(LINUX_KMOD_SUFFIX) $(1)/$(DSL_BIN_DIR)/$(PKG_BASE_NAME)$(PKG_MODULE_EXTENSION)
endif
	$(INSTALL_DIR) $(1)/$(DSL_FW_DIR)
endif

endef

$(eval $(call BuildPackage,dsl-cpe-mei-vrx))
$(eval $(call KernelPackage,dsl-cpe-mei-vrx))
