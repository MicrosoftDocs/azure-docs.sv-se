---
title: Felsöka allmänna problem med Azure percept DK och IoT Edge
description: Få fel söknings tips för några av de vanligaste problemen som påträffas under den här insikts upplevelsen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a6d099e8d267c9fe03e0bb676276e7a4ab8157ab
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521534"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Fel sökning för Azure percept DK (dev Kit)

Se rikt linjerna nedan för allmänna fel söknings tips för Azure percept DK.

## <a name="general-troubleshooting-commands"></a>Allmänna fel söknings kommandon

För att köra dessa kommandon, 
1. Ansluta till [dev kits Wi-Fi AP](./quickstart-percept-dk-set-up.md)
1. [SSH till dev-paketet](./how-to-ssh-into-percept-dk.md)
1. Ange kommandon i SSH-terminalen

Om du vill omdirigera utdata till en txt-fil för ytterligare analys, använder du följande syntax:

```console
[command] > [file name].txt
```

När du har omdirigerat utdata till en txt-fil kopierar du filen till värddatorn via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` refererar till platsen på värddatorn som du vill kopiera. txt-filen till. ```[remote username]``` är SSH-användarnamnet valt under [installationen](./quickstart-percept-dk-set-up.md). Om du inte har konfigurerat en SSH-inloggning under OOBE är ditt fjärranvändarnamn ```root``` .

Mer information om Azure IoT Edge-kommandon finns i [fel söknings dokumentationen för Azure IoT Edge enheten](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

|Kategori:         |Kommandoprompt                    |Funktioner                  |
|------------------|----------------------------|---------------------------|
|Operativsystem                |```cat /etc/os-release```         |kontrol lera Mariner-avbildnings version |
|Operativsystem                |```cat /etc/os-subrelease```      |kontrol lera derivat-avbildnings version |
|Operativsystem                |```cat /etc/adu-version```        |kontrol lera ADU-versionen |
|Temperatur       |```cat /sys/class/thermal/thermal_zone0/temp``` |kontrol lera temperaturen för devkit |
|Wi-Fi             |```journalctl -u hostapd.service``` |kontrol lera SoftAP-loggar|
|Wi-Fi             |```journalctl -u wpa_supplicant.service``` |kontrol lera Wi-Fi Services-loggar |
|Wi-Fi             |```journalctl -u ztpd.service```  |kontrol lera Wi-Fi inga loggar för touch-etablering |
|Wi-Fi             |```journalctl -u systemd-networkd``` |kontrol lera Mariner Network stack-loggar |
|Wi-Fi             |```/data/misc/wifi/hostapd_virtual.conf``` |kontrol lera konfigurations information för WiFi-åtkomst punkt |
|KOMS              |```journalctl -u oobe -b```       |kontrol lera OOBE-loggar |
|Telemetri         |```azure-device-health-id```      |Hitta unika telemetri HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |Kör konfigurations-och anslutnings kontroller för vanliga problem |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |kontrol lera behållar loggar, t. ex. tal-och syn-moduler |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |samla in modulreferens, Azure IoT Edge Security Manager-loggar, behållar motor loggar, ```iotedge check``` JSON-utdata och annan användbar felsöknings information från den senaste timmen |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |Visa loggarna för Azure IoT Edge Security Manager |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |Starta om Azure IoT Edge Security daemon |
|Azure IoT Edge          |```sudo iotedge list```           |Visa en lista över distribuerade Azure IoT Edge-moduler |
|Annat             |```df [option] [file]```          |Visa information om tillgängligt/totalt utrymme i angivna fil system |
|Annat             |```ip route get 1.1.1.1```        |Visa enhetens IP-och gränssnitts information |
|Annat             |```ip route get 1.1.1.1 \| awk '{print $7}'``` <br> ```ifconfig [interface]``` |Visa endast enhets-IP-adress |


```journalctl```Wi-Fi kommandon kan kombineras i följande enda kommando:

```console
journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Docker-felsöknings kommandon

|Kommandoprompt                        |Funktioner                  |
|--------------------------------|---------------------------|
|```docker ps``` |[visar vilka behållare som körs](https://docs.docker.com/engine/reference/commandline/ps/) |
|```docker images``` |[visar vilka bilder som finns på enheten](https://docs.docker.com/engine/reference/commandline/images/)|
|```docker rmi [image id] -f``` |[tar bort en bild från enheten](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```docker logs -f edgeAgent``` <br> ```docker logs -f [module_name]``` |[använder container loggar för angiven modul](https://docs.docker.com/engine/reference/commandline/logs/) |
|```docker image prune``` |[tar bort alla Dangling-avbildningar](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```watch docker ps``` <br> ```watch ifconfig [interface]``` |kontrol lera nedladdnings status för Docker-behållare |

## <a name="usb-updating"></a>USB-uppdatering

|Fel:                                    |Lösning:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX under USB-Flash via UUU |Det här felet är resultatet av en USB-anslutning som misslyckats under UUU-uppdateringen. Om USB-kabeln inte är korrekt ansluten till USB-portarna på datorn eller PE-10X, uppstår ett fel i det här formuläret. Försök att koppla bort och återansluta båda ändar av USB-kabeln och jiggling kabeln för att säkerställa en säker anslutning. Detta löser nästan alltid problemet. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Indikator tillstånd för Azure percept DK-kortet

Det finns tre små indikatorer ovanpå transport örens hölje. En moln ikon skrivs ut bredvid lampa 1, en Wi-Fi ikon skrivs ut bredvid lampa 2 och en utrops ikon skrivs ut bredvid indikator 3. I tabellen nedan finns information om varje LED-tillstånd.

|SPOLNING             |Stat      |Beskrivning                      |
|----------------|-----------|---------------------------------|
|LAMPA 1 (IoT Hub) |På (heldragen) |Enheten är ansluten till en IoT Hub. |
|LAMPA 2 (Wi-Fi)   |Långsam blink |Enheten är redo att konfigureras med Wi-Fi enkel anslutning och vi presenterar dess förekomst i en Configurator. |
|LAMPA 2 (Wi-Fi)   |Snabb blinkning |Autentiseringen lyckades, enhets associationen pågår. |
|LAMPA 2 (Wi-Fi)   |På (heldragen) |Autentisering och Association lyckades. enheten är ansluten till ett Wi-Fi nätverk. |
|INDIKATOR 3           |NA         |INDIKATORn används inte. |


