---
title: Konfigurera omdirigeringar av enheter – Azure
description: Så här konfigurerar du enhetsomdirigeringar för Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 37ecd06c4e3e71234e8fb1b6bad0cd05482dd31b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727857"
---
# <a name="configure-device-redirections"></a>Konfigurera omdirigering av enheter

Genom att konfigurera omdirigeringar för Windows Virtual Desktop miljö kan du använda skrivare, USB-enheter, mikrofoner och andra kringutrustning i fjärrsessionen. Vissa enhetsomdirigeringar kräver ändringar i både Remote Desktop Protocol (RDP)-egenskaper och grupprincip inställningar.

## <a name="supported-device-redirections"></a>Omdirigeringar som stöds

Varje klient stöder olika omdirigeringar av enheter. Se Jämför [klienterna för en](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) fullständig lista över omdirigeringar av enheter som stöds för varje klient.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Anpassa RDP-egenskaper för en värdpool

Mer information om hur du anpassar RDP-egenskaper för en värdpool med hjälp av PowerShell eller Azure Portal finns i [RDP-egenskaper.](customize-rdp-properties.md) En fullständig lista över RDP-egenskaper som stöds finns i [RDP-filinställningar som stöds.](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)

## <a name="setup-device-redirections"></a>Konfigurera omdirigeringar av enheter

Du kan använda följande RDP-egenskaper och grupprincip för att konfigurera omdirigeringar av enheter.

### <a name="audio-input-microphone-redirection"></a>Omdirigering av ljudindata (mikrofon)

Ange följande RDP-egenskap för att konfigurera omdirigering av ljudindata:

- `audiocapturemode:i:1` aktiverar omdirigering av ljudindata.
- `audiocapturemode:i:0` inaktiverar omdirigering av ljudindata.

### <a name="audio-output-speaker-redirection"></a>Omdirigering av ljudutdata (talare)

Ange följande RDP-egenskap för att konfigurera omdirigering av ljudutdata:

- `audiomode:i:0` aktiverar omdirigering av ljudutdata.
- `audiomode:i:1` eller `audiomode:i:2` inaktivera omdirigering av ljudutdata.

### <a name="camera-redirection"></a>Omdirigering av kamera

Ange följande RDP-egenskap för att konfigurera kameraomdirigering:

- `camerastoredirect:s:*` omdirigerar alla kameror.
- `camerastoredirect:s:` inaktiverar omdirigering av kameran.

>[!NOTE]
>Även om egenskapen `camerastoredirect:s:` är inaktiverad kan lokala kameror omdirigeras via `devicestoredirect:s:` egenskapen . Om du vill inaktivera kamerans omdirigeringsuppsättning helt och antingen ange eller definiera en delmängd `camerastoredirect:s:` `devicestoredirect:s:` av plug and play-enheter som inte innehåller någon kamera.

Du kan också omdirigera specifika kameror med hjälp av en semikolonavgränsad lista KSCATEGORY_VIDEO_CAMERA gränssnitt, till exempel `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` .

### <a name="clipboard-redirection"></a>Omdirigering av Urklipp

Ange följande RDP-egenskap för att konfigurera omdirigering av Urklipp:

- `redirectclipboard:i:1` aktiverar omdirigering av Urklipp.
- `redirectclipboard:i:0` inaktiverar omdirigering av Urklipp.

### <a name="com-port-redirections"></a>COM-portomdirigeringar

Ange följande RDP-egenskap för att konfigurera omdirigering av COM-portar:

- `redirectcomports:i:1` aktiverar COM-portomdirigering.
- `redirectcomports:i:0` inaktiverar COM-portomdirigering.

### <a name="usb-redirection"></a>USB-omdirigering

Ange först följande RDP-egenskap för att aktivera omdirigering av USB-enheter:

- `usbdevicestoredirect:s:*` aktiverar omdirigering av USB-enheter.
- `usbdevicestoredirect:s:` inaktiverar omdirigering av USB-enheter.

Ange sedan följande grupprincip på användarens lokala enhet:

- Gå till **Konfigurationsprinciper för**  >  **datorer** >  **Administrativa mallar**  >  **Windows-Fjärrskrivbordstjänster**  >    >  **Anslutning till fjärrskrivbord-klienten**  >  **RemoteFX USB-enhet omdirigering.**
- Välj **Tillåter RDP-omdirigering av andra RemoteFX USB-enheter som stöds från den här datorn.**
- Välj alternativet **Aktiverad** och välj sedan rutan Administratörer och **användare i Åtkomstbehörighet för RemoteFX USB-omdirigering.**
- Välj **OK**.

### <a name="plug-and-play-device-redirection"></a>Omdirigering av Plug and Play-enheter

Ange följande RDP-egenskap för att konfigurera omdirigering av plug and play-enheter:

- `devicestoredirect:s:*` aktiverar omdirigering av alla plug and play-enheter.
- `devicestoredirect:s:` inaktiverar omdirigering av plug and play-enheter.

Du kan också välja specifika plug and play-enheter med hjälp av en semikolonavgränsad lista, till exempel `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Omdirigering av lokal enhet

Ange följande RDP-egenskap för att konfigurera omdirigering av lokal enhet:

- `drivestoredirect:s:*` aktiverar omdirigering av alla diskenheter.
- `drivestoredirect:s:` inaktiverar omdirigering av lokal enhet.

Du kan också välja specifika enheter med hjälp av en semikolonavgränsad lista, till exempel `drivestoredirect:s:C:;E:;` .

Om du vill konfigurera filöverföring av webbklienten anger du `drivestoredirect:s:*` .

### <a name="printer-redirection"></a>Omdirigering av skrivare

Ange följande RDP-egenskap för att konfigurera omdirigering av skrivare:

- `redirectprinters:i:1` aktiverar omdirigering av skrivare.
- `redirectprinters:i:0` inaktiverar omdirigering av skrivare.

### <a name="smart-card-redirection"></a>Omdirigering av smartkort

Ange följande RDP-egenskap för att konfigurera smartkortomdirigering:

- `redirectsmartcards:i:1` aktiverar smartkortsomdirigering.
- `redirectsmartcards:i:0` inaktiverar smartkortsomdirigering.
