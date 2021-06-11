# ESPhome interní sensory
 aneb co lze vyždímat z flasnuté desky
> Jak jsem se postupně učil flashovat ESPhome, přicházel jsem víc a víc na možnosti dat, které z flasnuté desky dostat.
> Některé údaje považuji za naprosto nutné, jiné třeba ani v lovelace nezobrazuji.
> Za naprosto zásadní považuji vědět, zda je zařízení momentálně na síti a pokud není, Homeassistant mi pošle notifikaci. 
> Uvítám jakékoliv připomínky, zejména zda existují ještě další informace, které tu nemám.
## Stav zařízení
Říká, zda zařízení je na síti, či nikoliv. V mém případě mám u těch důležitých (elektroměr, plynoměr, vodoměr) nastaveno odeslání notifikací při odpojení a opětovném připojení.
```
binary_sensor:
  - platform: status
    name: "Elektroměr Stav"
```
## Restart
Umožňuje restartovat zařízení z Homeassistanta (samozřejmě, pokud je připojeno)
```
switch:
  - platform: restart
    name: "Elektroměr restart"
```
## Doba běhu v sekundách
Říká, jak dlouho od restartu zařízení běží. 
```
sensor:
  - platform: uptime
    name: "Elektroměr Doba běhu v sec."
    id: uptime_s
    update_interval: 60s
```
## Doba běhu
Říká, jak dlouho od restartu zařízení běží, ovšem v rozumnějších jednotkách. 
Jako zdroj ale potřebuje i dobu běhu v sekundách.   
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
##  Verze
Říká verzi ESPhome obsaženou v nahraném firmware, datum a čas kompilace souboru
```
text_sensor:
  - platform: version
    name: "Elektroměr Verze FW"
```
##  Wifi signál
Jak napovídá název, ukazuje sílu wifi signálu v db. 
```
sensor:
  - platform: wifi_signal
    name: "Elektroměr WiFi signál"
    update_interval: 60s
```
## Wifi info
Pomocí tohoto sensoru  lze získat
 - IP adresu zařízení
 - na jakou wifi je zařízení připojeno
 - na jakou anténu
 - a jakou má zařízení MAC adresu
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
## Napětí na čipu
Na deskách ESP8266 lze tímto měřit napětí na čipu. Na A0 pinu v tom případě nesmí nic být připojeno.
```
sensor:
  - platform: adc
    pin: VCC
    name: "Elektroměr VCC Voltage"
```
## A všechny sensory pohromadě
Soubor ke stažení: [sensors.yaml](https://github.com/mamo-nick/esphome-sensors/blob/main/sensors.yaml)

![](https://github.com/mamo-nick/esphome-sensors/blob/main/picture/ESPhome_sensors.png)
