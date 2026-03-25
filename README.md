## Прошиваем MikroTik [RB952Ui-5ac2nD (hAP ac lite)](https://mikrotik.com/product/RB952Ui-5ac2nD) в [OpenWrt](https://openwrt.org/) и устанавливаем [youtubeUnblock](https://github.com/Waujito/youtubeUnblock)

Полученный таким образом *новый роутер* можно использовать для просмотра YouTube на смарт-TV, не поддерживающем никакие методы обхода блокировок.

Могу предложить два варианта использования *нового роутера*:
1. Вставить его в разрыв между LAN-портом *старого роутера* и смарт-TV (в этом случае никаких дополнительных настроек делать не придется - WAN-порт *нового роутера* получит IP по DHCP от *старого роутера*, а смарт-TV получит IP по DHCP от *нового роутера*).
2. Заменить им *старый роутер*, настроив на *новом роутере* подключение к Интернет.

### Прошивка MikroTik RB952Ui-5ac2nD (hAP ac lite) в OpenWrt

1. Скачиваем [Tiny PXE Server](http://erwan.labalec.fr/tinypxeserver/pxesrv.zip).
2. Распаковываем файлы **Tiny PXE Server** в отдельную папку: **C:\pxesrv\\**.
3. Убеждаемся, что в файле **config.ini** в секции **\[dhcp\]** есть параметр **rfc951=1**.
4. Скачиваем файлы прошивок в эту же папку - **C:\pxesrv\\**:
   - [openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin](https://downloads.openwrt.org/releases/22.03.3/targets/ath79/mikrotik/openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin)
   - [openwrt-24.10.4-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin](https://downloads.openwrt.org/releases/24.10.4/targets/ath79/mikrotik/openwrt-24.10.4-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin)
5. Соединяем кабелем WAN-порт роутера (1-ый порт) с ПК.
6. На ПК в настройках сетевого подключения меняем:
```
   [x] Использовать следующий IP-адрес:
   IP-адрес: 192.168.1.10
   Маска подсети: 255.255.255.0
```
7. Запускаем с правами администратора **pxesrv.exe** и выбираем в поле **DHCP Server** сервер с адресом **192.168.1.10**.
8. Нажимаем кнопку **...** (справа внизу) и указываем файл **\*-initramfs-kernel.bin**.
9. Нажимаем кнопку **Online** - в окне программы должны появиться сообщения:
```
   DHCPd 192.168.1.10:67 started...
   TFPTd 192.168.1.10:69 started...
   HTTPd:80 started...
```
10. На MikroTik втыкаем зубочистку в отверстие с надписью **RES**.
11. Включаем питание роутера и ждем пока в окне **Tiny PXE Server** не появится сообщение:
```
   TFTPd:DoReadFile:openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin
```
12. После этого отпускаем зубочистку.
13. Ждем загрузки прошивки минуты две (пока **USR** не перестанет мигать).
14. На MikroTik переключаем кабель из 1-го порта (WAN) во 2-й порт (LAN).
15. Сбрасываем в сетевом подключении настройки на получение адреса по DHCP:
```
   [x] Получить IP-адрес автоматически
   [x] Получить адрес DNS-сервера автоматически
```
16. Используя браузер заходим в Web-интерфейс OpenWrt по адресу http://192.168.1.1/:
```
   Username: root
   Password:
```
17. В Web-интерфейсе OpenWrt переходим в раздел **System** -> **Backup / Flash Firmware**.
18. В подразделе **Flash new firmware image** нажимаем кнопку **Flash image**, а затем - **Browse**.
19. Указываем путь к файлу **\*-squashfs-sysupgrade.bin** и жмем кнопку **Upload**.
20. В следующем диалоге снимаем галочку **Keep settings and retain the current configuration** и жмем **Continue**.
21. Ждем когда роутер прошьется и перезагрузится (*НИ В КОЕМ СЛУЧАЕ НЕ ОТКЛЮЧАЙТЕ ПИТАНИЕ РОУТЕРА В ПРОЦЕССЕ ПРОШИВКИ*).
22. Заходим в Web-интерфейс (**Status** -> **Overview**) и убеждаемся, что прошивка удалась - **Firmware Version: 24.10.4**.

Если к процессу прошивки остались вопросы, то можно посмотреть на инструкцию с картинками для **MikroTik RB941-2nD (hAP lite)** ниже. Но файлы прошивки используем эти:
- [openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin](https://downloads.openwrt.org/releases/22.03.3/targets/ath79/mikrotik/openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin)
- [openwrt-24.10.4-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin](https://downloads.openwrt.org/releases/24.10.4/targets/ath79/mikrotik/openwrt-24.10.4-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin)

### Установка youtubeUnblock на MikroTik RB952Ui-5ac2nD (hAP ac lite)

1. Подключаемся к роутеру по SSH через [PuTTY](https://the.earth.li/~sgtatham/putty/latest/w32/putty.exe) (при запросе пароля просто жмем **ENTER**):
```powershell
putty.exe 192.168.1.1
```
2. Устанавливаем зависимости - в окне **PuTTY** выполняем команду:
```bash
   opkg update && opkg install kmod-nfnetlink-queue kmod-nft-queue kmod-nf-conntrack
```
3. Скачиваем на ПК пакет **youtubeUnblock** (для **hAP ac lite** с архитектурой **mipsbe** это пакет **\*-mips_24kc-\***):
   - [youtubeUnblock-1.1.0-2-2d579d5-mips_24kc-openwrt-23.05.ipk](https://github.com/Waujito/youtubeUnblock/releases/download/v1.1.0/youtubeUnblock-1.1.0-2-2d579d5-mips_24kc-openwrt-23.05.ipk)
   
4. Устанавливаем его в Web-интерфейсе роутера: **System** -> **Software**, жмем кнопку **Upload package** -> выбираем **youtubeUnblock-1.1.0-2-2d579d5-mips_24kc-openwrt-23.05.ipk** -> жмем **Upload** -> **Install** -> **Dismiss**.

5. Скачиваем на ПК еще пакет **luci-app-yotubeUnblock** (он общий для всех архитектур) для настройки **youtubeUnblock**:
   - [luci-app-youtubeUnblock-1.1.0-1-473af29.ipk](https://github.com/Waujito/youtubeUnblock/releases/download/v1.1.0/luci-app-youtubeUnblock-1.1.0-1-473af29.ipk)

6. Устанавливаем его в Web-интерфейсе роутера: **System** -> **Software**, жмем кнопку **Upload package** -> выбираем **luci-app-youtubeUnblock-1.1.0-1-473af29.ipk** -> жмем **Upload** -> **Install** -> **Dismiss**.

7. Далее выходим из веб-интерфейса (**Log out**) и входим заново. В меню **Services** появится новый пункт **youtubeUnblock**.

8. Если ютуб еще не заработал, то мне (провайдер Ростелеком) помогло это: **Services** -> **youtubeUnblock** -> **Configuration** -> **Default section** -> **Edit** -> **\[ \] Fake sni** -> **Save** -> **Save & Apply**.

Вот и все - теперь YouTube работает без использования VPN. И еще бонусом - в YouTube не будет рекламы.

### Ссылки по теме

- [Прошивка MikroTik RB941-2nD (hAP lite) в OpenWrt](https://global-hotspot.ru/%D0%BF%D1%80%D0%BE%D1%88%D0%B8%D0%B2%D0%BA%D0%B0-mikrotik-%D0%B2-openwrt/)
- [Routerich AX3000 - Российский роутер с OpenWRT. Гайд по youtubeUnblock и Podkop](https://youtu.be/2hOFnEP89DE?si=ZCVW9Kdh4vynQpch)
- [Cudy WR3000E с OpenWRT: Настройка YouTube Unblock + звонки в Discord, Telegram, WhatsApp](https://youtu.be/s-XdOlSpBYY?si=ZrNPtxM9yo9P-FIU)
