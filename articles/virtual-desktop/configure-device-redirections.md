---
title: Konfigurera omdirigering av enheter – Azure
description: Konfigurera omdirigering av enheter för virtuella Windows-datorer.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018383"
---
# <a name="configure-device-redirections"></a>Konfigurera omdirigering av enheter

Genom att konfigurera enhets omdirigeringar för Windows Virtual Desktop-miljön kan du använda skrivare, USB-enheter, mikrofoner och andra kring utrustnings enheter i fjärrsessionen. Vissa enhets omdirigeringar kräver ändringar i egenskaperna för både Remote Desktop Protocol (RDP) och grupprincip inställningar.

## <a name="supported-device-redirections"></a>Enhets omdirigeringar som stöds

Varje klient har stöd för olika enhets omdirigeringar. Kolla in [Jämför klienterna](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) med den fullständiga listan över enhets omdirigeringar som stöds för varje klient.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Anpassa RDP-egenskaperna för en värd pool

Om du vill veta mer om hur du anpassar RDP-egenskaperna för en adresspool med PowerShell eller Azure Portal kan du läsa [RDP-egenskaperna](customize-rdp-properties.md). En fullständig lista över RDP-egenskaper som stöds finns i [Inställningar för RDP-filer som stöds](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Installations enhets omdirigeringar

Du kan använda följande RDP-egenskaper och grupprincip inställningar för att konfigurera omdirigering av enheter.

### <a name="audio-input-microphone-redirection"></a>Omdirigering av ljud inspelning (mikrofon)

Ange följande RDP-egenskap för att konfigurera omdirigering av ljud inspelning:

- `audiocapturemode:i:1` aktiverar omdirigering av ljud inspelning.
- `audiocapturemode:i:0` inaktiverar omdirigering av ljud indata.

### <a name="audio-output-speaker-redirection"></a>Omdirigering av ljud utmatning (talare)

Ange följande RDP-egenskap för att konfigurera omdirigering av ljud utdata:

- `audiomode:i:0` aktiverar omdirigering av ljud utdata.
- `audiomode:i:1` eller `audiomode:i:2` inaktivera omdirigering av ljud utdata.

### <a name="camera-redirection"></a>Omdirigering av kamera

Ange följande RDP-egenskap för att konfigurera omdirigering av kamera:

- `camerastoredirect:s:*` omdirigerar alla kameror.
- `camerastoredirect:s:` inaktiverar omdirigering av kamera.

>[!NOTE]
>Även om `camerastoredirect:s:` egenskapen är inaktive rad kan lokala kameror omdirigeras via `devicestoredirect:s:` egenskapen. För att helt inaktivera uppsättning av omdirigering `camerastoredirect:s:` av kamera och antingen ange `devicestoredirect:s:` eller definiera vissa delar av plug and Play-enheter som inte innehåller någon kamera.

Du kan också omdirigera särskilda kameror med en semikolonavgränsad lista med KSCATEGORY_VIDEO_CAMERA gränssnitt, till exempel `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>Omdirigering av Urklipp

Ange följande RDP-egenskap för att konfigurera omdirigering av Urklipp:

- `redirectclipboard:i:1` aktiverar omdirigering av Urklipp.
- `redirectclipboard:i:0` inaktiverar omdirigering av Urklipp.

### <a name="com-port-redirections"></a>Omdirigering av COM-portar

Ange följande RDP-egenskap för att konfigurera omdirigering av COM-portar:

- `redirectcomports:i:1` aktiverar omdirigering av COM-portar.
- `redirectcomports:i:0` inaktiverar omdirigering av COM-portar.

### <a name="usb-redirection"></a>USB-omdirigering

Ange först följande RDP-egenskap för att aktivera omdirigering av USB-enheter:

- `usbdevicestoredirect:s:*` aktiverar omdirigering av USB-enheter.
- `usbdevicestoredirect:s:` inaktiverar omdirigering av USB-enheter.

Ange sedan följande grupprincip på användarens lokala enhet:

- Navigera till principer för **dator konfiguration**  >   >  **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >  **anslutning till fjärrskrivbord klientens**  >  **RemoteFX USB-enhetsomdirigering**.
- Välj **Tillåt RDP-omdirigering av andra RemoteFX USB-enheter som stöds från den här datorn**.
- Välj alternativet **aktive rad** och markera sedan rutan **Administratörer och användare i åtkomst rättigheter för RemoteFX USB-omdirigering** .
- Välj **OK**.

### <a name="plug-and-play-device-redirection"></a>Plug and Play-omdirigering av enheter

Ange följande RDP-egenskap för att konfigurera omdirigering av plug and Play-enheter:

- `devicestoredirect:s:*` aktiverar omdirigering av alla plug and Play-enheter.
- `devicestoredirect:s:` inaktiverar omdirigering av plug and Play-enheter.

Du kan också välja särskilda plug and Play-enheter med en semikolonavgränsad lista, till exempel `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Omdirigering av lokal enhet

Ange följande RDP-egenskap för att konfigurera omdirigering av lokal enhet:

- `drivestoredirect:s:*` aktiverar omdirigering av alla disk enheter.
- `drivestoredirect:s:` inaktiverar omdirigering av lokal enhet.

Du kan också välja särskilda enheter med en semikolonavgränsad lista, till exempel `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>Omdirigering av skrivare

Ange följande RDP-egenskap för att konfigurera omdirigering av skrivare:

- `redirectprinters:i:1` aktiverar omdirigering av skrivare.
- `redirectprinters:i:0` inaktiverar omdirigering av skrivare.

### <a name="smart-card-redirection"></a>Omdirigering av smartkort

Ange följande RDP-egenskap för att konfigurera omdirigering av smartkort:

- `redirectsmartcards:i:1` aktiverar omdirigering av smartkort.
- `redirectsmartcards:i:0` inaktiverar omdirigering av smartkort.