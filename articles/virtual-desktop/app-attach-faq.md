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
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556339"
---
# <a name="msix-app-attach-faq"></a>Vanliga frågor och svar om MSIX app

I den här artikeln besvaras vanliga frågor om MSIX-appen Anslut för Windows Virtual Desktop.

## <a name="does-msix-app-attach-use-fslogix"></a>Ansluter MSIX-appen använder FSLogix?

MSIX app Attach använder inte FSLogix. App Attach-och FSLogix är dock utformade för att fungera tillsammans för att ge en smidig användar upplevelse.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan jag använda MSIX app som är ansluten utanför det virtuella Windows-skrivbordet?

Ja, MSIX app Attach är en funktion som ingår i Windows 10 Enterprise och kan användas utanför det virtuella Windows-skrivbordet. Det finns dock inget hanterings plan för MSIX-appen bifoga utanför det virtuella Windows-skrivbordet.

## <a name="how-do-i-get-an-msix-package"></a>Hur gör jag för att hämta ett MSIX-paket?

Din program varu leverantör får ett MSIX-paket. Du kan också konvertera icke-MSIX paket till MSIX. Lär dig mer om [hur du flyttar dina befintliga installations program till MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Vilka operativ system stöder MSIX-app anslutning?

Windows 10 Enterprise och Windows 10 Enterprise multi-session.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om MSIX app Attach kan du titta på vår [översikts](what-is-app-attach.md) [ord lista](app-attach-glossary.md). Annars kan du komma igång med [Konfigurera appen Anslut](app-attach.md).