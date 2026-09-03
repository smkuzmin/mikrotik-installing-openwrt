## MikroTik: Прошивка в OpenWrt и установка youtubeUnblock

### 1. Прошиваем OpenWrt 24.10

1. Скачиваем [Tiny PXE Server](http://erwan.labalec.fr/tinypxeserver/pxesrv.zip).
2. Распаковываем файлы **Tiny PXE Server** в отдельную папку: **C:\pxesrv\\**.
3. Убеждаемся, что в файле **config.ini** в секции **\[dhcp\]** есть параметр **rfc951=1**.
4. Скачиваем файлы прошивок в эту же папку - **C:\pxesrv\\**:
   - для **RB952Ui-5ac2nD (hAP ac lite)**:
     - [openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin](https://downloads.openwrt.org/releases/22.03.3/targets/ath79/mikrotik/openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin)
     - [openwrt-24.10.4-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin](https://downloads.openwrt.org/releases/24.10.4/targets/ath79/mikrotik/openwrt-24.10.4-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin)
   - для **RB951Ui-2HnD**:
     - [openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-initramfs-kernel.bin](https://downloads.openwrt.org/releases/24.10.8/targets/ath79/mikrotik/openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-initramfs-kernel.bin)
     - [openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-squashfs-sysupgrade.bin](https://downloads.openwrt.org/releases/24.10.8/targets/ath79/mikrotik/openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-squashfs-sysupgrade.bin)
5. Соединяем кабелем **WAN**-порт роутера (1-ый порт) с ПК.
6. На ПК в настройках сетевого подключения (Ethernet) меняем настройки IPv4 на:
```
   [x] Использовать следующий IP-адрес:
   IP-адрес: 192.168.1.10
   Маска подсети: 255.255.255.0
```
7. Запускаем с правами администратора **pxesrv.exe** и выбираем в поле **Option 54 (DHCP Server)** сервер с адресом **192.168.1.10**.
8. Нажимаем кнопку **...** (справа внизу) и указываем файл **\*-initramfs-kernel.bin**.
9. Нажимаем кнопку **Online** - в окне программы должны появиться сообщения:
```
   DHCPd 192.168.1.10:67 started...
   TFTPd 192.168.1.10:69 started...
   HTTPd:80 started...
```

10. На выключенном MikroTik втыкаем зубочистку в отверстие с надписью **RES**.
11. Не отпуская **RES**, включаем питание роутера и ждем, пока в окне **Tiny PXE Server** не появится сообщение:
```
   TFTPd:DoReadFile:openwrt-*-initramfs-kernel.bin
```
12. После этого отпускаем **RES**.
13. Ждем загрузки временной прошивки минуты две (пока индикатор **USR** не перестанет мигать).
14. На MikroTik переключаем кабель из 1-го порта (**WAN**) во 2-й порт (**LAN**).
15. На ПК в настройках сетевого подключения (Ethernet) меняем настройки IPv4 на:
```
   [x] Получить IP-адрес автоматически
   [x] Получить адрес DNS-сервера автоматически
```
16. Используя браузер, заходим в Web-интерфейс OpenWrt по адресу http://192.168.1.1/:
```
   Username: root
   Password:
```
17. В Web-интерфейсе OpenWrt переходим в раздел **System** -> **Backup / Flash Firmware**.
18. В подразделе **Flash new firmware image** нажимаем кнопку **Flash image..**, а затем - **Browse..**.
19. Указываем путь к файлу **\*-squashfs-sysupgrade.bin** и жмем кнопку **Upload**.
20. В следующем диалоге снимаем галочку **Keep settings and retain the current configuration**, ставим галочку **Force upgrade** и жмем **Continue**.
21. Ждем, когда роутер прошьется и перезагрузится (*НИ В КОЕМ СЛУЧАЕ НЕ ОТКЛЮЧАЙТЕ ПИТАНИЕ РОУТЕРА В ПРОЦЕССЕ ПРОШИВКИ*).
22. Заходим в Web-интерфейс (**Status** -> **Overview**) и убеждаемся, что прошивка удалась - **Firmware Version: 24.10.\***.

 > *Если к процессу прошивки остались вопросы, то можно посмотреть на инструкцию с картинками для [[MikroTik. Прошивка RB941-2nD (hAP lite) в OpenWrt|MikroTik RB941-2nD (hAP lite)]]. Но файлы прошивки используем строго те, что указаны выше.*

### 2. Подключаем устройство

1. Втыкаем **LAN**-кабель (на котором по DHCP раздается Интернет), в **WAN**-порт нашего устройства.
2. Подключаем **LAN**-порт нашего устройства к ПК. Дефолтные настройки подключения **OpenWrt**:
```ini
WAN IP: Dynamic
LAN IP: 192.168.1.1
  USER: root
  PASS:
```
3. Подключаемся к устройству по протоколу **SSH** через терминал [PuTTY](https://the.earth.li/~sgtatham/putty/latest/w32/putty.exe):
```powershell
   putty.exe 192.168.1.1 -l root
```
4. Следующие этапы подразумевают подключение к устройству через терминал и выполнение в нем указанных блоков кода.

### 3. Подготавливаем дефолтную OpenWrt и перезагружаемся

```bash
### Устанавливаем английский язык интерфейса
# System -> System -> Language and Style -> Language: English
uci set luci.main.lang='en'

### Устанавливаем часовой пояс
# System -> System -> General Settings -> Timezone: Europe/Samara
uci set system.@system[0].timezone='<+04>-4'
uci set system.@system[0].zonename='Europe/Samara'

### Отключаем PoE-Out на MikroTik (чтобы порт не горел красным) - добавляем команды (перед exit 0) в скрипт автозапуска
# System -> Startup -> Local Startup:
# sleep 2; for f in /sys/class/gpio/*poe*/value; do echo 0 >$f; done
# exit 0
# -> Save -> Dismiss
grep -q 'gpio.*poe' /etc/rc.local || sed -i '/exit 0/i sleep 2; for f in /sys/class/gpio/*poe*/value; do echo 0 >$f; done' /etc/rc.local

### Разрешаем подключения на WAN-интерфейсе
# Network -> Firewall -> Zones -> at the intersection of wan and Input, select accept -> Save & Apply
uci set firewall.@zone[1].input='ACCEPT'

### Отключаем IPv6
# Удаляем IPv6-туннели и интерфейсы
# Network -> Interfaces -> удаляем WAN6, 6in4, 6to4
uci -q delete network.wan6
uci -q delete network.6in4
uci -q delete network.6to4
# Отключаем IPv6 на LAN-интерфейсе
# Network -> Interfaces -> LAN -> Advanced Settings
uci    set    network.lan.ipv6='off'
uci    set    network.lan.delegate='0'
uci -q delete network.lan.ip6assign
uci -q delete network.lan.ip6addr
uci -q delete network.lan.ip6prefix
# Отключаем IPv6 на WAN-интерфейсе
# Network -> Interfaces -> WAN -> Advanced Settings
uci    set    network.wan.ipv6='0'
uci    set    network.wan.delegate='0'
uci -q delete network.wan.ip6addr
uci -q delete network.wan.ip6prefix
# Отключаем IPv6 в DHCP
uci    set    dhcp.lan.dhcpv6='disabled'
uci    set    dhcp.lan.ndp='disabled'
uci    set    dhcp.lan.ra='disabled'
uci -q delete dhcp.wan.dhcpv6
uci -q delete dhcp.wan.ra
# Удаляем IPv6-правила в Firewall
for rule in $(uci show firewall|grep "family='ipv6'"|cut -d'.' -f2|cut -d'=' -f1|sort -t'[' -k2 -rn); do uci delete firewall.$rule; done
for rule in $(uci show firewall|grep 'ip6proto='|cut -d'.' -f2|cut -d'=' -f1); do uci delete firewall.$rule.ip6proto; done
for rule in $(uci show firewall|grep '@rule'|grep -v 'family='|cut -d'[' -f2|cut -d']' -f1); do uci set firewall.@rule[$rule].family='ipv4'; done
# Отключаем IPv6 в sysctl
grep -q 'net.ipv6.conf.all.disable_ipv6=1'     /etc/sysctl.conf || echo >>/etc/sysctl.conf 'net.ipv6.conf.all.disable_ipv6=1'
grep -q 'net.ipv6.conf.default.disable_ipv6=1' /etc/sysctl.conf || echo >>/etc/sysctl.conf 'net.ipv6.conf.default.disable_ipv6=1'
grep -q 'net.ipv6.conf.lo.disable_ipv6=1'      /etc/sysctl.conf || echo >>/etc/sysctl.conf 'net.ipv6.conf.lo.disable_ipv6=1'
grep -q 'net.ipv6.conf.br-lan.disable_ipv6=1'  /etc/sysctl.conf || echo >>/etc/sysctl.conf 'net.ipv6.conf.br-lan.disable_ipv6=1'
# Удаляем IPv6-пакеты (удаляем пакеты, и все зависящие от них)
opkg remove --force-removal-of-dependent-packages odhcp6c
opkg remove --force-removal-of-dependent-packages odhcpd-ipv6only
opkg remove --force-removal-of-dependent-packages kmod-ip6tables
opkg remove --force-removal-of-dependent-packages kmod-nf-nat6
opkg remove --force-removal-of-dependent-packages kmod-ipt-nat6
opkg remove --force-removal-of-dependent-packages ip6tables
opkg remove --force-removal-of-dependent-packages luci-proto-ipv6

### Удаляем ненужные пакеты (не обращая внимания на зависимости)
# Оставляем в Services только:
#  - Kernel Manager
#  - QoS over Nftables
#  - youtubeUnblock
#  - Bandwith Monitor
#  - Watchcat
#  - Network Shares
#  - Terminal
#  - UPnP IGD & PCP
opkg remove --force-depends adblock  luci-app-adblock
opkg remove --force-depends aria2    luci-app-aria2
opkg remove --force-depends ddns     luci-app-ddns
opkg remove --force-depends hd-idle  luci-app-hd-idle
opkg remove --force-depends minidlna luci-app-minidlna
opkg remove --force-depends netdata  luci-app-netdata
opkg remove --force-depends qos      luci-app-qos
opkg remove --force-depends smartdns luci-app-smartdns

### Отключаем ненужные сервисы в автозагрузке
# System -> Startup:
#
# Pr  Имя              Описание                                   Можно ли отключать
# --  ---------------  -----------------------------------------  -------------------------------------------------------------------------
# 19  wpad             WPA-Enterprise / 802.1X                    Да, если используется только WPA2-Personal
# 21  fa-fancontrol    Управление вентилятором                    Да, если роутер с пассивным охлаждением
# 30  radius           RADIUS-клиент                              Да, если не корпоративная сеть 802.1X
# 35  odhcpd           DHCPv6 и RA для IPv6                       Да, если IPv6 отключён (рекомендуется для youtubeUnblock)
# 50  cron             Планировщик задач                          Да, если не используете расписания
# 50  vsftpd           FTP-сервер                                 Да, если не используется FTP
# 61  avahi-daemon     mDNS/Bonjour (локальное обнаружение)       Да, если не используете AirPlay/Chromecast discovery
# 80  blockd           Авто-монтирование USB-накопителей          Да, если нет USB-дисков
# 80  collectd         Сбор метрик для мониторинга                Да, если не используете внешние системы мониторинга
# 94  miniupnpd        Автоматический проброс портов (UPnP)       Да, если не используете торренты или устройства, которые сами открывают порты (IP-камеры, NAS и т.п.)
# 97  watchcat         Автоматическая перезагрузка при зависании  Да, если не используется Watchcat
# 98  samba4           SMB-сервер для общего доступа к файлам     Да, если не расшариваете папки по сети
# 99  wsdd2            Web Service Discovery для SMB              Да, если не нужен в Windows-сети
# --  ---------------  -----------------------------------------  -------------------------------------------------------------------------
# 50  sqm              Smart Queue Management (QoS)               Да, если не используете торренты/видеозвонки на перегруженном канале
# 60  nlbwmon          Мониторинг трафика по хостам               Да, если не нужен Bandwith Monitor
# 79  luci_statistics  Статистика в веб-интерфейсе                Да, если не смотрите графики в LuCI
/etc/init.d/avahi-daemon   stop 2>/dev/null && /etc/init.d/avahi-daemon   disable
/etc/init.d/blockd         stop 2>/dev/null && /etc/init.d/blockd         disable
/etc/init.d/collectd       stop 2>/dev/null && /etc/init.d/collectd       disable
/etc/init.d/cron           stop 2>/dev/null && /etc/init.d/cron           disable
/etc/init.d/fa-fancontrol  stop 2>/dev/null && /etc/init.d/fa-fancontrol  disable
/etc/init.d/miniupnpd      stop 2>/dev/null && /etc/init.d/miniupnpd      disable
/etc/init.d/odhcpd         stop 2>/dev/null && /etc/init.d/odhcpd         disable
/etc/init.d/radius         stop 2>/dev/null && /etc/init.d/radius         disable
/etc/init.d/samba4         stop 2>/dev/null && /etc/init.d/samba4         disable
/etc/init.d/vsftpd         stop 2>/dev/null && /etc/init.d/vsftpd         disable
/etc/init.d/watchcat       stop 2>/dev/null && /etc/init.d/watchcat       disable
/etc/init.d/wpad           stop 2>/dev/null && /etc/init.d/wpad           disable
/etc/init.d/wsdd2          stop 2>/dev/null && /etc/init.d/wsdd2          disable
/etc/init.d/youtubeUnblock stop 2>/dev/null && /etc/init.d/youtubeUnblock disable

### Применяем изменения
uci commit
sysctl -p -q

### Перезагружаемся
# System -> Reboot -> Perform reboot
reboot
```

### 4. Устанавливаем youtubeUnblock

```bash
(
  ### Установка youtubeUnblock. Поддерживаемые устройства:
  # - Любой MikroTik с архитектурой MIPSBE, прошитый в OpenWrt 24.10
  # - Nano Pi R3S LTS c установленной FriendlyWrt 24.10

  ### Обновляем списки пакетов
  # System -> Software -> Update lists..
  opkg update || { echo 'ERROR: opkg update'; exit 1; }

  ### Устанавливаем зависимости для youtubeUnblock
  # System -> Software -> Download and install package: kmod-nfnetlink-queue -> OK -> Install -> Dismiss
  # System -> Software -> Download and install package: kmod-nft-queue       -> OK -> Install -> Dismiss
  # System -> Software -> Download and install package: kmod-nf-conntrack    -> OK -> Install -> Dismiss
  opkg install kmod-nfnetlink-queue kmod-nft-queue kmod-nf-conntrack || { echo 'ERROR: opkg install youtubeUnblock depends'; exit 1; }

  ### Скачиваем и устанавливаем пакеты youtubeUnblock для OpenWrt 24.10
  # System -> Software -> Upload Package.. -> Browse.. -> youtubeUnblock-*.ipk -> Upload -> Install -> Dismiss
  # System -> Software -> Upload Package.. -> Browse.. -> luci-app-youtubeUnblock-*.ipk -> Upload -> Install -> Dismiss
   VERSION='1.3.1'
  BASE_URL="https://github.com/Waujito/youtubeUnblock/releases/download/v${VERSION}"
     BUILD='1-4a223b0'
      ARCH=$(opkg print-architecture|awk 'END{print $2}')
  pkg="youtubeUnblock-${VERSION}-${BUILD}-${ARCH}-openwrt-24.10"
  wget -qO     "/tmp/${pkg}.ipk" "${BASE_URL}/${pkg}.ipk" || { echo "ERROR: Download url: ${url}"; exit 1; }
  opkg install "/tmp/${pkg}.ipk"                          || { echo "ERROR: Install pkg: ${pkg}" ; exit 1; }
  rm -f        "/tmp/${pkg}.ipk"
  pkg="luci-app-youtubeUnblock-${VERSION}-${BUILD}"
  wget -qO     "/tmp/${pkg}.ipk" "${BASE_URL}/${pkg}.ipk" || { echo "ERROR: Download url: ${url}"; exit 1; }
  opkg install "/tmp/${pkg}.ipk"                          || { echo "ERROR: Install pkg: ${pkg}" ; exit 1; }
  rm -f        "/tmp/${pkg}.ipk"

  ### Отключаем Routing/NAT Offloading (он должен быть выключен для работы любых DPI-обходчиков на базе nfqws)
  # Network -> Firewall -> Routing/NAT Offloading -> Flow offloading type: None
  uci set firewall.@defaults[0].flow_offloading='0'
  uci set firewall.@defaults[0].flow_offloading_hw='0'

  ### Применяем изменения
  uci commit
  /etc/init.d/firewall reload

  ### Включаем youtubeUnblock в автозагрузку
  # System -> Startup -> youtubeUnblock -> Enabled
  /etc/init.d/youtubeUnblock enable

  ### Запускаем youtubeUnblock
  /etc/init.d/youtubeUnblock restart
)
```

Далее выходим из веб-интерфейса (**Log out**) и входим заново. В меню **Services** появится новый пункт **youtubeUnblock**.

Если ютуб еще не заработал, то мне (провайдер Ростелеком) помогло это: **Services** -> **youtubeUnblock** -> **Configuration** -> **Default section** -> **Edit** -> **\[ \] Fake sni** -> **Save** -> **Save & Apply**.

Вот и все - теперь YouTube работает без использования VPN. И еще бонусом - в YouTube не будет рекламы.

***

## Ссылки

- [Прошивка Mikrotik в OpenWRT](https://global-hotspot.ru/%D0%BF%D1%80%D0%BE%D1%88%D0%B8%D0%B2%D0%BA%D0%B0-mikrotik-%D0%B2-openwrt/)
