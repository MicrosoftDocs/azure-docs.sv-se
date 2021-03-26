---
title: Felsöka allmänna problem med Azure percept DK och IoT Edge
description: Få fel söknings tips för några av de vanligaste problemen med Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c9c62ec07873272b956877ec51d8765ae0bbd100
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605645"
---
# <a name="azure-percept-dk-troubleshooting"></a>Fel sökning av Azure percept-DK

Se rikt linjerna nedan för allmänna fel söknings tips för Azure percept DK.

## <a name="general-troubleshooting-commands"></a>Allmänna fel söknings kommandon

Kör dessa kommandon genom att använda [ssh i dev-paketet](./how-to-ssh-into-percept-dk.md) och ange kommandona i SSH-klientens prompt.

Om du vill omdirigera utdata till en txt-fil för ytterligare analys, använder du följande syntax:

```console
sudo [command] > [file name].txt
```

Ändra behörigheter för txt-filen så att den kan kopieras:

```console
sudo chmod 666 [file name].txt
```

När du har omdirigerat utdata till en txt-fil kopierar du filen till värddatorn via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` refererar till platsen på värddatorn som du vill kopiera. txt-filen till. ```[remote username]``` är SSH-användarnamnet valt under [installationen](./quickstart-percept-dk-set-up.md).

Mer information om Azure IoT Edge-kommandon finns i [fel söknings dokumentationen för Azure IoT Edge enheten](../iot-edge/troubleshoot.md).

|Kategori:         |Kommandoprompt                    |Funktioner                  |
|------------------|----------------------------|---------------------------|
|Operativsystem                |```cat /etc/os-release```         |kontrol lera Mariner-avbildnings version |
|Operativsystem                |```cat /etc/os-subrelease```      |kontrol lera derivat-avbildnings version |
|Operativsystem                |```cat /etc/adu-version```        |kontrol lera ADU-versionen |
|Temperatur       |```cat /sys/class/thermal/thermal_zone0/temp``` |kontrol lera temperaturen för devkit |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |kontrol lera SoftAP-loggar|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |kontrol lera Wi-Fi Services-loggar |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |kontrol lera Wi-Fi inga loggar för touch-etablering |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |kontrol lera Mariner Network stack-loggar |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |kontrol lera konfigurations information för WiFi-åtkomst punkt |
|KOMS              |```sudo journalctl -u oobe -b```       |kontrol lera OOBE-loggar |
|Telemetri         |```sudo azure-device-health-id```      |Hitta unika telemetri HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |Kör konfigurations-och anslutnings kontroller för vanliga problem |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |kontrol lera behållar loggar, t. ex. tal-och syn-moduler |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |samla in modulreferens, Azure IoT Edge Security Manager-loggar, behållar motor loggar, ```iotedge check``` JSON-utdata och annan användbar felsöknings information från den senaste timmen |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |Visa loggarna för Azure IoT Edge Security Manager |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Starta om Azure IoT Edge Security daemon |
|Azure IoT Edge          |```sudo iotedge list```           |Visa en lista över distribuerade Azure IoT Edge-moduler |
|Övrigt             |```df [option] [file]```          |Visa information om tillgängligt/totalt utrymme i angivna fil system |
|Övrigt             |`ip route get 1.1.1.1`        |Visa enhetens IP-och gränssnitts information |
|Övrigt             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |Visa endast enhets-IP-adress |


```journalctl```Wi-Fi kommandon kan kombineras i följande enda kommando:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker-felsöknings kommandon

|Kommandoprompt                        |Funktioner                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[visar vilka behållare som körs](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[visar vilka bilder som finns på enheten](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[tar bort en bild från enheten](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[använder container loggar för angiven modul](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[tar bort alla Dangling-avbildningar](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |kontrol lera nedladdnings status för Docker-behållare |

## <a name="usb-updates"></a>USB-uppdateringar

|Fel:                                    |Lösning:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX under USB-Flash via UUU |Det här felet är resultatet av en USB-anslutning som misslyckats under UUU-uppdateringen. Om USB-kabeln inte är korrekt ansluten till USB-portarna på datorn eller percept DK-tavlan, så inträffar ett fel i det här formuläret. Försök att koppla från och återansluta båda ändar av USB-kabeln och jiggling kabeln för att säkerställa en säker anslutning. Detta löser nästan alltid problemet. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Indikator tillstånd för Azure percept DK-kortet

Det finns tre små indikatorer ovanpå transport örens hölje. En moln ikon skrivs ut bredvid lampa 1, en Wi-Fi ikon skrivs ut bredvid lampa 2 och en utrops ikon skrivs ut bredvid indikator 3. I tabellen nedan finns information om varje LED-tillstånd.

|SPOLNING             |Stat      |Beskrivning                      |
|----------------|-----------|---------------------------------|
|LAMPA 1 (IoT Hub) |På (heldragen) |Enheten är ansluten till en IoT Hub. |
|LAMPA 2 (Wi-Fi)   |Långsam blink |Enheten är redo att konfigureras med Wi-Fi enkel anslutning och vi presenterar dess förekomst i en Configurator. |
|LAMPA 2 (Wi-Fi)   |Snabb blinkning |Autentiseringen lyckades, enhets associationen pågår. |
|LAMPA 2 (Wi-Fi)   |På (heldragen) |Autentisering och Association lyckades. enheten är ansluten till ett Wi-Fi nätverk. |
|INDIKATOR 3           |NA         |INDIKATORn används inte. |