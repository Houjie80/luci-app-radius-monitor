# This is free software, licensed under the Apache License, Version 2.0
#
# Copyright (C) 2024 Maizil <maizilpc7@gmail.com>

include $(TOPDIR)/rules.mk

LUCI_TITLE:=LuCI for Radius Monitor.
PKG_VERSION:=0.46.050
LUCI_DEPENDS:=+luci +php8-cli +php8 +php8-mod-mysqli +php8-mod-pdo-mysql +php8-mod-gd +php8-mod-xml +php8-mod-filter +php8-mod-curl +iptables-nft +iptables-mod-hashlimit
LUCI_DESCRIPTION:=Application for monitoring RADIUS server.
LUCI_MAINTAINER:=Maizil <maizilpc7@gmail.com>

PKG_MAINTAINER:=Maizil <maizilpc7@gmail.com>
PKG_VERSION:=beta
PKG_LICENSE:=Apache-2.0
PKG_ARCH:=all

define Package/$(PKG_NAME)/postinst
#!/bin/sh
	[ -d "/www/RadMonv2" ] && rm -rf /www/RadMonv2
[ -d "/usr/share/RadMonv2" ] && rm -rf /usr/share/RadMonv2
[ -d "/etc/freeradius3" ] && rm -rf /etc/freeradius3

if find /usr/bin -type f -name "radmon-*"; then
    rm -rf /usr/bin/radmon-*
fi

sleep 1

# echo -e "\e[33mClone Repository\e[0m"
# git clone --depth 1 https://github.com/maizil41/RadMonv2.git /usr/share/RadMonv2 > /dev/null 2>&1

# echo -e "\e[33mDownloading freeradius3.zip\e[0m"
# wget https://raw.githubusercontent.com/maizil41/luci-app-radius-monitor/main/files/freeradius3.zip -O /etc/freeradius3.zip > /dev/null 2>&1

echo -e "\e[33mExtracting freeradius3.zip\e[0m"
cd /etc/ && unzip -o freeradius3.zip > /dev/null

sleep 1

echo -e "\e[33mCreate link mods-enabled\e[0m"
cd /etc/freeradius3/mods-enabled
for mod in always attr_filter chap detail digest eap exec expiration expr files logintime mschap pap preprocess radutmp realm sql sradutmp unix; do
    ln -sf ../mods-available/$mod
done
cd

sleep 1

echo -e "\e[33mCreate link sites-enabled\e[0m"
cd /etc/freeradius3/sites-enabled
ln -sf ../sites-available/default
ln -sf ../sites-available/inner-tunnel
cd

sleep 1

echo -e "\e[33mChange permission\e[0m"
unzip /root/radmon.zip -d /usr/bin
chmod +x /usr/bin/radmon-exec /usr/bin/radmon-log /usr/bin/radmon-kuota

sleep 1

echo -e "\e[33mChange radmon\e[0m"
unzip /root/RadMonv2.zip -d /usr/share

sleep 1

echo -e "\e[33mCrate link On /www\e[0m"
ln -sf /usr/share/RadMonv2 /www/RadMonv2


echo -e "\e[33mSetup cronjob...\e[0m"

if (crontab -l | grep -q '/usr/bin/radmon-kuota'); then
    echo -e "\e[33mCronjob already exist, skipping...\e[0m"
else
    (crontab -l; echo "* * * * * /usr/bin/radmon-kuota >/dev/null 2>&1") | crontab -
fi

if (crontab -l | grep -q 'find /www/RadiusMonitor/dist/pages/data/tmp -type f ! -name '\''index.php'\'' -exec rm -f {} +'); then
else
    (crontab -l; echo "0 0 * * * find /www/RadiusMonitor/dist/pages/data/tmp -type f ! -name 'index.php' -exec rm -f {} +") | crontab -
fi

endef

include $(TOPDIR)/feeds/luci/luci.mk

# call BuildPackage - OpenWrt buildroot signature
