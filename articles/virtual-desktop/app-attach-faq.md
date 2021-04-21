---
title: Windows Virtual Desktop vanliga frågor och svar om att bifoga MSIX-appen – Azure
description: Vanliga frågor och svar om bifoga MSIX-app för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6057f4a76f274e34b036ea352a3691b34d24b3a1
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835370"
---
# <a name="msix-app-attach-faq"></a>Vanliga frågor och svar om att bifoga MSIX-appar

I den här artikeln får du svar på vanliga frågor om msix-appen för Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Vad är skillnaden mellan att bifoga MSIX- och MSIX-appar?

MSIX är ett paketeringsformat för appar, medan MSIX-appinstallation är den funktion som levererar MSIX-paket till distributionen.

## <a name="does-msix-app-attach-use-fslogix"></a>Använder MSIX-appen FSLogix?

MSIX-app attach använder inte FSLogix. MSIX-apparna attach och FSLogix är dock utformade för att fungera tillsammans för att ge en sömlös användarupplevelse.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan jag använda MSIX-appen för att ansluta utanför Windows Virtual Desktop?

DE API:er som power MSIX-appen kopplar är tillgängliga för Windows 10 Enterprise. Dessa API:er kan användas utanför Windows Virtual Desktop. Det finns dock inget hanteringsplan för msix-appen som bifogas utanför Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Hur gör jag för att du ett MSIX-paket?

Programvaruleverantören ger dig ett MSIX-paket. Du kan också konvertera icke-MSIX-paket till MSIX. Läs mer i [Så här flyttar du dina befintliga installationsprogram till MSIX.](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)

## <a name="which-operating-systems-support-msix-app-attach"></a>Vilka operativsystem stöder MSIX-app attach?

Windows 10 Enterprise och Windows 10 Enterprise Multi-session, version 2004 eller senare.

## <a name="is-msix-app-attach-currently-generally-available"></a>Är MSIX-appen bifogad för närvarande allmänt tillgänglig?

MSIX-app attach är en del Windows 10 Enterprise och Windows 10 Enterprise Multi-session, version 2004 eller senare. Båda operativsystemen är för närvarande allmänt tillgängliga. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan jag använda MSIX-app bifoga utanför Windows Virtual Desktop?

BIFOGA API:er för MSIX- och MSIX-appar ingår i Windows 10 Enterprise och Windows 10 Enterprise Multi-session, version 2004 och senare. Vi tillhandahåller för närvarande inte hanteringsprogram för MSIX-app bifoga utanför Windows Virtual Desktop.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Kan jag köra två versioner av samma program på samma gång?

För att två versioner av samma MSIX-program ska kunna köras samtidigt måste MSIX-paketfamiljen som definieras i appxmanifest.xml-filen vara olika för varje app.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Ska jag inaktivera automatisk uppdatering när jag använder MSIX-app bifoga?

Ja. MSIX-app attach stöder inte automatisk uppdatering för MSIX-program.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Hur fungerar behörigheter när MSIX-appen ansluts?

Alla virtuella datorer i en värdpool som använder MSIX-apptillägg måste ha läsbehörighet för filresursen där MSIX-avbildningarna lagras. Om den även Azure Files måste de beviljas både behörigheter för rollbaserad åtkomstkontroll (RBAC) och NTFS (New Technology File System).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Kan jag använda MSIX-app attach för HTTP eller HTTPs?

Användning av MSIX-app attach via HTTP eller HTTPs stöds inte för närvarande.

## <a name="can-i-restage-the-same-msix-application"></a>Kan jag använda samma MSIX-program igen?

Ja. Du kan restage-program som du redan har restaged och detta bör inte orsaka några fel.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Har MSIX-appen stöd för själv signerade certifikat?

Ja. Du måste installera det själv signerade certifikatet på alla virtuella sessionsvärdar där MSIX-appen bifogas används som värd för det själv signerade programmet.

## <a name="what-applications-can-i-repackage-to-msix"></a>Vilka program kan jag paketera om till MSIX?

Varje program använder olika funktioner i operativsystemet, programmeringsspråk och ramverk. Om du vill paketera om programmet följer du anvisningarna i [Så här flyttar du dina befintliga installationsprogram till MSIX.](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix) Du hittar en lista över de saker du behöver för att paketera om ett program i [Förbereda för att paketera ett skrivbordsprogram](/windows/msix/desktop/desktop-to-uwp-prepare). 

Vissa program kan inte vara programlagerlagrade, vilket innebär att de inte kan paketeras om i en MSIX-fil. Här är en lista över de program som inte kan paketeras på nytt:

- Drivrutiner 
- Active-X eller Silverlight
- VPN-klienter
- Antivirusprogram

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om att bifoga MSIX-appen kan du läsa [vår översikt](what-is-app-attach.md) [och ordlista.](app-attach-glossary.md) Annars kan du komma igång [med Konfigurera app attach](app-attach.md).
