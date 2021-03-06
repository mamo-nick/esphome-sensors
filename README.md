# ESPhome interní sensory
 aneb co lze vyždímat z flasnuté desky
> Jak jsem se postupně učil flashovat ESPhome, přicházel jsem víc a víc na možnosti dat, které z flasnuté desky dostat.
> Některé údaje považuji za naprosto nutné, jiné třeba ani v lovelace nezobrazuji.
> Za naprosto zásadní považuji vědět, zda je zařízení momentálně na síti a pokud není, Homeassistant mi pošle notifikaci. 
> Uvítám jakékoliv připomínky, zejména zda existují ještě další informace, které tu nemám.
> Zdroj informací: [www.esphome.io](https://www.esphome.io)
## Stav zařízení
Říká, zda zařízení je na síti, či nikoliv. V mém případě mám u těch důležitých (elektroměr, plynoměr, vodoměr) nastaveno odeslání notifikací při odpojení a opětovném připojení.

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/Stav.png)
```
binary_sensor:
  - platform: status
    name: "Elektroměr Stav"
```
Zdroj: [https://www.esphome.io/components/binary_sensor/status.html](https://www.esphome.io/components/binary_sensor/status.html)
## Restart
Umožňuje restartovat zařízení z Homeassistanta (samozřejmě, pokud je připojeno)

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/Restart.png)
```
switch:
  - platform: restart
    name: "Elektroměr restart"
```
Zdroj: [https://www.esphome.io/components/switch/restart.html](https://www.esphome.io/components/switch/restart.html)
## Doba běhu v sekundách
Říká, jak dlouho od restartu zařízení běží. 

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/Doba_behu_v_sec.png)
```
sensor:
  - platform: uptime
    name: "Elektroměr Doba běhu v sec."
    id: uptime_s
    update_interval: 60s
```
Zdroj: [https://www.esphome.io/components/sensor/uptime.html](https://www.esphome.io/components/sensor/uptime.html)
## Doba běhu
Říká, jak dlouho od restartu zařízení běží, ovšem v rozumnějších jednotkách. 
Jako zdroj ale potřebuje i dobu běhu v sekundách.   

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/Doba_behu.png)
```
sensor:
  - platform: uptime
    name: "Elektroměr Doba běhu v sec."
    id: uptime_s
    update_interval: 60s
text_sensor:
  - platform: template
    name: "Elektroměr Doba běhu"
    lambda: |-
      uint32_t dur = id(uptime_s).state;
      int dys = 0;
      int hrs = 0;
      int mnts = 0;
      if (dur > 86399) {
      dys = trunc(dur / 86400);
      dur = dur - (dys * 86400);
      }
      if (dur > 3599) {
      hrs = trunc(dur / 3600);
      dur = dur - (hrs * 3600);
      }
      if (dur > 59) {
      mnts = trunc(dur / 60);
      dur = dur - (mnts * 60);
      }
      char buffer[17];
      sprintf(buffer, "%ud %02uh %02um %02us", dys, hrs, mnts, dur);
      return {buffer};
    icon: mdi:clock-start
    update_interval: 60s
```
Zdroj: [https://www.esphome.io/components/sensor/uptime.html](https://www.esphome.io/components/sensor/uptime.html)
##  Verze
Říká verzi ESPhome obsaženou v nahraném firmware, datum a čas kompilace souboru

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/Verze.png)
```
text_sensor:
  - platform: version
    name: "Elektroměr Verze FW"
```
Zdroj: [https://www.esphome.io/components/text_sensor/version.html](https://www.esphome.io/components/text_sensor/version.html)
##  Wifi signál
Jak napovídá název, ukazuje sílu wifi signálu v db. 

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/Wifi_signal.png)
```
sensor:
  - platform: wifi_signal
    name: "Elektroměr WiFi signál"
    update_interval: 60s
```
Zdroj: [https://www.esphome.io/components/sensor/wifi_signal.html](https://www.esphome.io/components/sensor/wifi_signal.html)
sensor/wifi_signal.html
## Wifi info
Pomocí tohoto sensoru  lze získat
 - IP adresu zařízení
 - na jakou wifi je zařízení připojeno
 - na jakou anténu
 - a jakou má zařízení MAC adresu

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/wifi_info.png)
```
text_sensor:
  - platform: wifi_info
    ip_address:
      name: "Elektroměr IP Address"
    ssid:
      name: "Elektroměr Connected SSID"
    bssid:
      name: "Elektroměr Connected BSSID"
    mac_address:
      name: "Elektroměr Mac Wifi Address"
```
Zdroj: [https://www.esphome.io/components/text_sensor/wifi_info.html](https://www.esphome.io/components/text_sensor/wifi_info.html)
## Napětí na čipu
Na deskách ESP8266 lze tímto měřit napětí na čipu. Na A0 pinu v tom případě nesmí nic být připojeno.

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/Voltage.png)
```
sensor:
  - platform: adc
    pin: VCC
    name: "Elektroměr VCC Voltage"
```
Zdroj: [https://www.esphome.io/components/sensor/adc.html](https://www.esphome.io/components/sensor/adc.html)
## A všechny sensory pohromadě
Soubor ke stažení: [sensors.yaml](https://github.com/mamo-nick/esphome-sensors/blob/main/sensors.yaml)

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/ESPhome_sensors.png)
