#
# Copyright (C) 2010-2017 OpenWrt.org
# Copyright (C) 2010 Alfred E. Heggestad
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=baresip
PKG_VERSION:=1.1.0
PKG_RELEASE:=6

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/baresip/baresip/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=f9230b27c4a62f31223847bc485c51f3d960f8a09f36998dedb73358e1784b4e

PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=docs/COPYING
PKG_MAINTAINER:=Jiri Slachta <jiri@slachta.eu>

PKG_BUILD_DEPENDS:=glib2/host
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

baresip-mods:= \
	aac \
	alsa \
	amr \
	avcodec \
	avfilter \
	avformat \
	cons \
	ctrl-dbus \
	evdev \
	g711 \
	g722 \
	g726 \
	gst \
	gst-video \
	httpreq \
	l16 \
	mqtt \
	opus \
	opus_multistream \
	oss \
	plc \
	portaudio \
	pulse \
	snapshot \
	sndfile \
	speex-pp \
	srtp \
	stdio \
	syslog \
	v4l2 \
	vp8 \
	vp9

PKG_CONFIG_DEPENDS:= \
	$(patsubst %,CONFIG_PACKAGE_baresip-mod-%,$(subst _,-,$(baresip-mods)))

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/baresip/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Telephony
  URL:=https://github.com/baresip/baresip
endef

define Package/baresip
$(call Package/baresip/Default)
  TITLE:=Portable and modular SIP User-Agent with A/V support
  DEPENDS:=+libre +librem
  USERID:=$(PKG_NAME)=374:$(PKG_NAME)=374
  MENU:=1
  FILE_MODES:= \
	  /etc/baresip:baresip:baresip:0750 \
	  /etc/baresip/accounts:baresip:baresip:0640 \
	  /etc/baresip/config:baresip:baresip:0640 \
	  /etc/baresip/contacts:baresip:baresip:0640
endef

baresip-mod-aac         := USE_AAC
baresip-mod-alsa        := USE_ALSA
baresip-mod-amr         := USE_AMR
baresip-mod-avcodec     := USE_AVCODEC
baresip-mod-avfilter    := USE_AVFILTER
baresip-mod-avformat    := USE_AVFORMAT
baresip-mod-cons        := USE_CONS
baresip-mod-ctrl-dbus   := HAVE_GLIB USE_DBUS
baresip-mod-evdev       := USE_EVDEV
baresip-mod-g711        := USE_G711
baresip-mod-g722        := USE_G722
baresip-mod-g726        := USE_G726
baresip-mod-gst         := USE_GST
baresip-mod-gst-video   := USE_GST_VIDEO
baresip-mod-httpreq     := USE_HTTPREQ
baresip-mod-l16         := USE_L16
baresip-mod-mqtt        := USE_MQTT
baresip-mod-opus        := USE_OPUS
baresip-mod-opus_multistream   := USE_OPUS_MS
baresip-mod-oss         := USE_OSS
baresip-mod-plc         := USE_PLC
baresip-mod-portaudio   := USE_PORTAUDIO
baresip-mod-pulse       := USE_PULSE
baresip-mod-snapshot    := USE_SNAPSHOT
baresip-mod-sndfile     := USE_SNDFILE
baresip-mod-speex-pp    := USE_SPEEX_PP
baresip-mod-srtp        := USE_SRTP
baresip-mod-stdio       := USE_STDIO
baresip-mod-syslog      := USE_SYSLOG
baresip-mod-vp8         := USE_VPX
baresip-mod-vp9         := USE_VPX
baresip-mod-v4l2        := USE_V4L2

BARESIP_MOD_OPTIONS:= \
	MOD_AUTODETECT= \
	$(foreach m,$(baresip-mods),$(foreach v,$(baresip-mod-$(m)),$(v)=$(if $(CONFIG_PACKAGE_baresip-mod-$(subst _,-,$(m))),1)))

MAKE_FLAGS+= \
	CROSS_COMPILE="$(TARGET_CROSS)" \
	DESTDIR="$(PKG_INSTALL_DIR)" \
	EXTRA_LFLAGS="$(TARGET_LDFLAGS)" \
	LD="$(TARGET_CC)" \
	LIBRE_MK="$(STAGING_DIR)/usr/share/re/re.mk" \
	LIBRE_INC="$(STAGING_DIR)/usr/include/re" \
	LIBRE_SO="$(STAGING_DIR)/usr/lib" \
	LIBREM_PATH="$(STAGING_DIR)/usr" \
	OS=linux \
	RELEASE=1 \
	SYSROOT="$(shell $(FIND) $(TOOLCHAIN_DIR) -path '*/include/pthread.h' | sed -ne '1s|/include/pthread.h||p')" \
	SYSROOT_ALT="$(STAGING_DIR)/usr" \
	$(BARESIP_MOD_OPTIONS)

TARGET_CFLAGS+=-D_GNU_SOURCE

define Package/baresip/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/baresip $(1)/usr/bin
	$(INSTALL_DIR) $(1)/usr/lib/baresip/modules
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/{account,auloop,contact,ice,menu,stun,turn}.so \
		$(1)/usr/lib/baresip/modules
	$(INSTALL_DIR) $(1)/usr/share/baresip
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/share/baresip/* $(1)/usr/share/baresip
	$(INSTALL_DIR) $(1)/etc/baresip
	$(SED) '/^#/!s/^/#/' $(PKG_BUILD_DIR)/docs/examples/accounts
	$(SED) '/^#module_path/s|^#||;s|/local||' $(PKG_BUILD_DIR)/docs/examples/config
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/docs/examples/{accounts,config,contacts} $(1)/etc/baresip
	$(INSTALL_DIR) $(1)/etc/default
	$(INSTALL_CONF) ./files/baresip.default $(1)/etc/default/baresip
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/baresip.init $(1)/etc/init.d/baresip
endef

define Package/baresip/conffiles
/etc/baresip/accounts
/etc/baresip/config
/etc/baresip/contacts
/etc/default/baresip
/etc/init.d/baresip
endef

##################
# bareSIP modules
# 1. Name
# 2. Title
# 3. Files
# 4. Dependencies
##################

define BuildPlugin

  define Package/baresip-mod-$(subst _,-,$(1))
    $$(call Package/baresip/Default)
    TITLE:=$(2)
    DEPENDS:=baresip $(patsubst +%,+PACKAGE_$(PKG_NAME)-mod-$(subst _,-,$(1)):%,$(4))
  endef

  define Package/baresip-mod-$(subst _,-,$(1))/install
	[ -z "$(3)" ] || $(INSTALL_DIR) $$(1)/usr/lib/baresip/modules
	for f in $(3); do \
		$(INSTALL_DATA) \
			$(PKG_INSTALL_DIR)/usr/lib/baresip/modules/$$$$$$$${f}.so \
			$$(1)/usr/lib/baresip/modules; \
	done
  endef

  $$(eval $$(call BuildPackage,baresip-mod-$(subst _,-,$(1))))
endef

$(eval $(call BuildPackage,baresip))

$(eval $(call BuildPlugin,aac,MPEG-4 AAC Audio Codec,aac,+fdk-aac))
$(eval $(call BuildPlugin,alsa,ALSA audio driver,alsa,+alsa-lib))
$(eval $(call BuildPlugin,amr,Adaptive Multi-Rate [AMR] audio codec,amr,))
$(eval $(call BuildPlugin,aubridge,Audio bridge module,aubridge,))
$(eval $(call BuildPlugin,aufile,Audio module for using a WAV-file as audio input,aufile,))
$(eval $(call BuildPlugin,ausine,Sine Audio Source,ausine,))
$(eval $(call BuildPlugin,avcodec,Video codec using FFmpeg,avcodec,+libffmpeg-full))
$(eval $(call BuildPlugin,avformat,Video source using FFmpeg,avformat,baresip-mod-avcodec))
$(eval $(call BuildPlugin,b2bua,Back-to-Back User-Agent module,b2bua,))
$(eval $(call BuildPlugin,cons,UDP/TCP console UI driver,cons,))
$(eval $(call BuildPlugin,ctrl_dbus,DBus control interface,ctrl_dbus,+glib2))
$(eval $(call BuildPlugin,ctrl_tcp,TCP control interface,ctrl_tcp,))
$(eval $(call BuildPlugin,debug_cmd,Debug commands,debug_cmd,))
$(eval $(call BuildPlugin,dtls_srtp,DTLS-SRTP end-to-end encryption,dtls_srtp,))
$(eval $(call BuildPlugin,ebuacip,EBU ACIP [Audio Contribution over IP] Profile,ebuacip,))
$(eval $(call BuildPlugin,echo,Echo server module,echo,))
$(eval $(call BuildPlugin,evdev,Linux input driver,evdev,))
$(eval $(call BuildPlugin,fakevideo,Fake video input/output driver,fakevideo,))
$(eval $(call BuildPlugin,g711,G.711 audio codec,g711,))
$(eval $(call BuildPlugin,g722,G.722 audio codec,g722,+libspandsp))
$(eval $(call BuildPlugin,g726,G.726 audio codec,g726,+libspandsp))
$(eval $(call BuildPlugin,gst,Gstreamer 1.0 playbin pipeline,gst,@AUDIO_SUPPORT +glib2 +libgstreamer1))
$(eval $(call BuildPlugin,gst_video,Video codecs using Gstreamer 1.0,gst_video,@AUDIO_SUPPORT +glib2 +libgst1app +libgstreamer1))
$(eval $(call BuildPlugin,httpd,HTTP webserver UI-module,httpd,))
$(eval $(call BuildPlugin,httpreq,HTTP request module,httpreq,))
$(eval $(call BuildPlugin,l16,16-bit linear codec,l16,))
$(eval $(call BuildPlugin,mixausrc,Mixes another audio source into audio stream,mixausrc,))
$(eval $(call BuildPlugin,mqtt,Message Queue Telemetry Transport [MQTT] client,mqtt,+libmosquitto))
$(eval $(call BuildPlugin,multicast,Multicast support,multicast,))
$(eval $(call BuildPlugin,mwi,Message Waiting Indication,mwi,))
$(eval $(call BuildPlugin,natpmp,NAT Port Mapping Protocol module,natpmp,))
$(eval $(call BuildPlugin,opus,OPUS Interactive audio codec,opus,+libopus))
$(eval $(call BuildPlugin,opus_multistream,Opus Multistream Audio Codec,opus_multistream,+libopus))
$(eval $(call BuildPlugin,oss,OSS audio driver,oss,))
$(eval $(call BuildPlugin,plc,Packet Loss Concealment,plc,+libspandsp))
$(eval $(call BuildPlugin,portaudio,Portaudio audio driver,portaudio,+portaudio))
$(eval $(call BuildPlugin,presence,Presence module,presence,))
$(eval $(call BuildPlugin,pulse,Pulseaudio audio driver,pulse,PACKAGE_$(PKG_NAME)-mod-pulse:pulseaudio))
$(eval $(call BuildPlugin,rtcpsummary,RTCP summary module,rtcpsummary,))
$(eval $(call BuildPlugin,selfview,Video selfview module,selfview,))
$(eval $(call BuildPlugin,serreg,Serial registration mode,serreg,))
$(eval $(call BuildPlugin,snapshot,Snapshot video module,snapshot,+libpng))
$(eval $(call BuildPlugin,sndfile,Audio dumper using libsndfile,sndfile,+libsndfile))
$(eval $(call BuildPlugin,speex_pp,Speex Pre-processor,speex_pp,+libspeexdsp))
$(eval $(call BuildPlugin,srtp,Secure RTP module using libre,srtp,))
$(eval $(call BuildPlugin,stdio,Standard input/output UI driver,stdio,))
$(eval $(call BuildPlugin,syslog,Syslog module,syslog,))
$(eval $(call BuildPlugin,uuid,UUID generator and loader,uuid,))
$(eval $(call BuildPlugin,v4l2,Video4Linux2 video source,v4l2,+libv4l))
$(eval $(call BuildPlugin,v4l2_codec,Video4Linux2 video codec module,v4l2_codec,))
$(eval $(call BuildPlugin,vidbridge,Video bridge module,vidbridge,))
$(eval $(call BuildPlugin,vidinfo,Video-info filter,vidinfo,))
$(eval $(call BuildPlugin,vidloop,Video-loop test module,vidloop,))
$(eval $(call BuildPlugin,vumeter,Display audio levels in console,vumeter,))
$(eval $(call BuildPlugin,vp8,VP8 video codec,vp8,+libvpx))
$(eval $(call BuildPlugin,vp9,VP9 video codec,vp9,+libvpx))
