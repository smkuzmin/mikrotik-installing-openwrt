## MikroTik: Прошивка в OpenWrt

Инструкция предназначена для роутеров **MikroTik** с архитектурой **MIPSBE** и проверена на:

- **RB952Ui-5ac2nD (hAP ac lite)**
- **RB951Ui-2HnD**

Загрузчик RouterOS `7.xx` не поддерживается OpenWrt и она загружается только при зажатии **RES** на 10 сек. (до мигания индикатора **USR**). Поэтому, если у вас MikroTik с RouterOS `7.xx`, нужно прошить RouterOS `6.xx` перед прошивкой OpenWrt.

### 1. Проверяем версию RouterOS

1. Подключаемся к MikroTik через [WinBox](https://mikrotik.com/download/winbox) (логин: `admin`, пароль: либо отсутствует, либо см. на наклейке с обратной стороны).
2. Заходим в **System -> RouterBOARD** и смотрим **Current Firmware** / **Upgrade Firmware** - они должны совпадать (для синхронизации нажмите **Upgrade** и перезагрузитесь: **System -> Reboot**).
3. Если **Current Firmware** / **Upgrade Firmware**: `6.xx` - переходим к пункту: **3. Прошиваем OpenWrt 24.10**.

### 2. Прошиваем RouterOS 6.xx

1. Скачиваем [routeros-mipsbe-6.49.17.npk](https://download.mikrotik.com/routeros/6.49.17/routeros-mipsbe-6.49.17.npk) в `C:\netinstall\`.
2. Скачиваем [netinstall-7.24.zip](https://download.mikrotik.com/routeros/7.24/netinstall-7.24.zip) и распаковываем в `C:\netinstall\`.
3. Соединяем кабелем **WAN**-порт роутера (1-ый порт) с ПК.
4. На ПК в настройках этого Ethernet-подключения меняем:
```
   [x] Использовать следующий IP-адрес:
   IP-адрес: 192.168.88.10
   Маска подсети: 255.255.255.0
```

5. Запускаем с правами администратора **netinstall.exe**.
6. На выключенном MikroTik втыкаем зубочистку в отверстие с надписью **RES**.
7. Не отпуская **RES**, включаем питание роутера. Ждем, пока роутер не определится в окне **Routers/Drives** и отпускаем **RES**.
8. Выбираем роутер в окне **Routers/Drives**, снимаем галочку **Keep old configuration**, жмем **Browse..**, выбираем `C:\netinstall`, выбираем пакет `routeros-mipsbe` и жмем **Install**.
9. Ждем сообщения "**Installation finished successfully**"
10. Подключаемся к MikroTik через **WinBox**, синхронизируем загрузчик: **System** -> **RouterBOARD** -> **Upgrade** И перезагружаемся: **System** -> **Reboot**.

### 3. Прошиваем OpenWrt 24.10

1. Скачиваем [Tiny PXE Server](http://erwan.labalec.fr/tinypxeserver/pxesrv.zip).
2. Распаковываем файлы **Tiny PXE Server** в отдельную папку: `C:\pxesrv\`.
3. Убеждаемся, что в файле **config.ini** в секции `[dhcp]` есть параметр `rfc951=1`.
4. Скачиваем файлы прошивок в эту же папку - `C:\pxesrv\`:
   - для **RB952Ui-5ac2nD (hAP ac lite)**:
     - [openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin](https://downloads.openwrt.org/releases/22.03.3/targets/ath79/mikrotik/openwrt-22.03.3-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-initramfs-kernel.bin)
     - [openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin](https://downloads.openwrt.org/releases/24.10.8/targets/ath79/mikrotik/openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-952ui-5ac2nd-squashfs-sysupgrade.bin)
   - для **RB951Ui-2HnD**:
     - [openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-initramfs-kernel.bin](https://downloads.openwrt.org/releases/24.10.8/targets/ath79/mikrotik/openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-initramfs-kernel.bin)
     - [openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-squashfs-sysupgrade.bin](https://downloads.openwrt.org/releases/24.10.8/targets/ath79/mikrotik/openwrt-24.10.8-ath79-mikrotik-mikrotik_routerboard-951ui-2hnd-squashfs-sysupgrade.bin)
5. Соединяем кабелем **WAN**-порт роутера (1-ый порт) с ПК.
6. На ПК в настройках этого Ethernet-подключения меняем:
```
   [x] Использовать следующий IP-адрес:
   IP-адрес: 192.168.1.10
   Маска подсети: 255.255.255.0
```
7. Запускаем с правами администратора **pxesrv.exe** и выбираем в поле **Option 54 (DHCP Server)** сервер с адресом `192.168.1.10`.
8. Нажимаем кнопку **...** (справа внизу) и указываем файл `*-initramfs-kernel.bin`.
9. Нажимаем кнопку **Online** - в окне программы должны появиться сообщения:
```
   DHCPd 192.168.1.10:67 started...
   TFTPd 192.168.1.10:69 started...
   HTTPd:80 started...
```

10. На выключенном MikroTik втыкаем зубочистку в отверстие с надписью **RES**.
11. Не отпуская **RES**, включаем питание роутера. Ждем, пока в окне **Tiny PXE Server** не появится сообщение `TFTPd:DoReadFile:openwrt-*-initramfs-kernel.bin` и отпускаем **RES**.
12. Ждем загрузки временной прошивки минуты две (пока индикатор **USR** не перестанет мигать).
13. На MikroTik переключаем кабель из 1-го порта (**WAN**) во 2-й порт (**LAN**).
14. На ПК в настройках сетевого подключения (Ethernet) меняем настройки IPv4 на:
```
   [x] Получить IP-адрес автоматически
   [x] Получить адрес DNS-сервера автоматически
```
15. Используя браузер, заходим в Web-интерфейс OpenWrt по адресу http://192.168.1.1/:
```
   Username: root
   Password:
```
16. В Web-интерфейсе OpenWrt переходим в раздел **System** -> **Backup / Flash Firmware**.
17. В подразделе **Flash new firmware image** нажимаем кнопку **Flash image..**, а затем - **Browse..**.
18. Указываем путь к файлу `*-squashfs-sysupgrade.bin` и жмем кнопку **Upload**.
19. В следующем диалоге снимаем галочку **Keep settings and retain the current configuration**, ставим галочку **Force upgrade** (если она есть) и жмем **Continue**.
20. Ждем, когда роутер прошьется и перезагрузится (*НИ В КОЕМ СЛУЧАЕ НЕ ОТКЛЮЧАЙТЕ ПИТАНИЕ РОУТЕРА В ПРОЦЕССЕ ПРОШИВКИ*).
21. Заходим в Web-интерфейс (**Status** -> **Overview**) и убеждаемся, что прошивка удалась - **Firmware Version:** `24.10.8`.

 > *Если к процессу прошивки остались вопросы, то можно посмотреть на инструкцию с картинками для [[MikroTik. Прошивка RB941-2nD (hAP lite) в OpenWrt|MikroTik RB941-2nD (hAP lite)]]. Но файлы прошивки используем строго те, что указаны выше.*

***

## Ссылки

- [Прошивка Mikrotik в OpenWRT](https://global-hotspot.ru/%D0%BF%D1%80%D0%BE%D1%88%D0%B8%D0%B2%D0%BA%D0%B0-mikrotik-%D0%B2-openwrt/)
