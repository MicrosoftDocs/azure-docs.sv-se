---
title: Vanliga frågor och svar om MSIX app Attach i Windows Virtual Desktop – Azure
description: Vanliga frågor och svar om MSIX-appen ansluter till det virtuella Windows-skrivbordet.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 395c274630131c2ae5f451443913e1e69c7c422a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738709"
---
# <a name="msix-app-attach-faq"></a>Vanliga frågor och svar om MSIX app

I den här artikeln besvaras vanliga frågor om MSIX-appen Anslut för Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Vad är skillnaden mellan MSIX och MSIX-appen koppling?

MSIX är ett paket format för appar, medan MSIX app Attach är den funktion som levererar MSIX-paket till din distribution.

## <a name="does-msix-app-attach-use-fslogix"></a>Ansluter MSIX-appen använder FSLogix?

MSIX app Attach använder inte FSLogix. MSIX app Attach och FSLogix är dock utformade för att fungera tillsammans för att ge en smidig användar upplevelse.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan jag använda MSIX-appen som är ansluten utanför det virtuella Windows-skrivbordet?

De API: er som Power MSIX-appen ansluter till är tillgängliga för Windows 10 Enterprise. Dessa API: er kan användas utanför det virtuella Windows-skrivbordet. Det finns dock inget hanterings plan för MSIX-appen bifoga utanför det virtuella Windows-skrivbordet.

## <a name="how-do-i-get-an-msix-package"></a>Hur gör jag för att hämta ett MSIX-paket?

Din program varu leverantör får ett MSIX-paket. Du kan också konvertera icke-MSIX paket till MSIX. Lär dig mer om [hur du flyttar dina befintliga installations program till MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Vilka operativ system stöder MSIX-app anslutning?

Windows 10 Enterprise och Windows 10 Enterprise multi-session, version 2004 eller senare.

## <a name="is-msix-app-attach-currently-generally-available"></a>Är MSIX app-anslutning för närvarande allmänt tillgänglig?

MSIX app Attach är en del av Windows 10 Enterprise och Windows 10 Enterprise multi-session, version 2004 eller senare. Båda operativ systemen är för närvarande allmänt tillgängliga. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan jag använda MSIX app som är ansluten utanför det virtuella Windows-skrivbordet?

MSIX-och MSIX-appen bifogade API: er ingår i Windows 10 Enterprise och Windows 10 Enterprise multi-session, version 2004 och senare. Vi tillhandahåller för närvarande inte hanterings program för MSIX-appen bifoga utanför det virtuella Windows-skrivbordet.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Kan jag köra två versioner av samma program på samma tidpunkt?

För att två versioner av samma MSIX-program ska kunna köras samtidigt måste MSIX-paket familjen som definieras i appxmanifest.xml-filen vara olika för varje app.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Bör jag inaktivera automatisk uppdatering när jag använder MSIX-appen?

Ja. MSIX app Attach stöder inte automatisk uppdatering för MSIX-program.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Hur fungerar behörigheter med MSIX-appen bifoga?

Alla virtuella datorer (VM) i en adresspool som använder MSIX app Attach måste ha Läs behörighet för fil resursen där MSIX-avbildningarna lagras. Om den också använder Azure Files måste de beviljas både rollbaserad åtkomst kontroll (RBAC) och nya funktioner för teknik fil system (NTFS).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Kan jag använda MSIX app Attach för HTTP eller HTTPs?

Användning av MSIX-appen via HTTP eller HTTPs stöds inte för närvarande.

## <a name="can-i-restage-the-same-msix-application"></a>Kan jag ommellanlagra samma MSIX-program?

Ja. Du kan återmellanlagra program som du redan har omberett och detta bör inte orsaka några fel.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Ansluter MSIX-appen support självsignerade certifikat?

Ja. Du måste installera det självsignerade certifikatet på alla de virtuella datorerna i sessionen där MSIX app Attach används som värd för det självsignerade programmet.


## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om MSIX app Attach kan du kolla vår [Översikt](what-is-app-attach.md) och [ord lista](app-attach-glossary.md). Annars kan du komma igång med [Konfigurera appen Anslut](app-attach.md).
