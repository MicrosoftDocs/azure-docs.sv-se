---
title: Uppdatera din Azure percept DK över en USB-anslutning
description: Lär dig hur du uppdaterar Azure percept DK över en USB-anslutning
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663852"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Så här uppdaterar du Azure percept DK över en USB-anslutning

Följ den här guiden för att lära dig hur du utför en USB-uppdatering för transport kortet för din Azure percept DK.

## <a name="prerequisites"></a>Förutsättningar
- Värddator med en tillgänglig USB-C-eller USB-A-port.
- Azure percept DK-bärvåg (dev Kit) och levererat USB-C till USB-C-kabel. Om värddatorn har en USB-A-port, men inte en USB-C-port, kan du använda en USB-C för USB-A-kabel (säljs separat).
- Installera [SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (administratörs åtkomst krävs).
- Installera NXP UUU-verktyget. [Hämta den senaste versionen](https://github.com/NXPmicro/mfgtools/releases) uuu.exe-filen (för Windows) eller uuu-filen (för Linux) under fliken till gångar.
- [Installera 7-zip](https://www.7-zip.org/). Den här program varan kommer att användas för att extrahera RAW-bildfilen från den komprimerade filen XZ. Hämta och installera lämplig. exe-fil.

## <a name="steps"></a>Steg
1.  Hämta följande [tre USB-uppdateringsfiler](https://go.microsoft.com/fwlink/?linkid=2155734):
    - pe101-UEFI-***&lt; versions &gt; nummer***. RAW. XZ
    - emmc_full.txt
    - fast-HAB-FW. RAW
 
1. Extrahera till pe101-UEFI-***&lt; versions &gt; nummer**_. Raw från den komprimerade pe101-UEFI_- * _&lt; &gt; versions nummer_* *. RAW. XZ-filen. Är du osäker på hur du extraherar? Ladda ned och installera 7-zip, högerklicka sedan på avbildnings filen **. XZ** och välj 7-zip- &gt; extrahering här.

1. Kopiera följande tre filer till den mapp som innehåller UUU-verktyget:
    - Extraherade pe101-UEFI-***&lt; versions nummer &gt;***. RAW-fil (från steg 2).
    - emmc_full.txt (från steg 1).
    - fast-HAB-FW. RAW (från steg 1).
 
1. Slå på dev-paketet.
1. [Ansluta till dev-paketet via SSH](./how-to-ssh-into-percept-dk.md)
1. Öppna en kommando tolk i Windows (Start &gt; cmd) eller en Linux-terminal och navigera till mappen där uppdateringsfilerna lagras. Kör följande kommando för att initiera uppdateringen:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
När du har kört dessa kommandon kan du se ett meddelande om att "väntar på enhet..." i kommando tolken. Detta är förväntat och du bör gå vidare till nästa steg.
    
1. Anslut dev-paketets transport kort till värddatorn via en USB-kabel. Anslut alltid från frakt tavlornas USB-C-port till antingen värddatorns USB-C-eller USB-A-port (USB-C till USB-en kabel som säljs separat), beroende på vilka portar som är tillgängliga. 
 
1. I SSH/SparaTillFil-terminalen anger du följande kommandon för att ställa in dev-paketet i USB-läge och sedan starta om dev-paketet.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. Du kan få en indikation på att värddatorn känner igen enheten och att uppdaterings processen startar automatiskt. Gå tillbaka till kommando tolken för att se statusen. Processen tar upp till tio minuter och när uppdateringen lyckas visas ett meddelande om att "lyckades 1"-fel 0 "
 
1. Stäng av frakt kortet när uppdateringen är klar. Koppla från USB-kabeln från datorn.  Anslut Azure percept vision-modulen tillbaka till frakt tavlan med USB-kabeln.

1. Sätt tillbaka operatörs kortet på.

## <a name="next-steps"></a>Nästa steg

Ditt dev-paket har nu uppdaterats. Du kan fortsätta utveckling och drift med din devkit.