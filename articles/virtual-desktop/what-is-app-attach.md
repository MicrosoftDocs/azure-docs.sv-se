---
title: Översikt över Windows Virtual Desktop MSIX-appen – Azure
description: Vad är MSIX-appen ansluten? Läs mer i den här artikeln.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556349"
---
# <a name="what-is-msix-app-attach"></a>Vad är MSIX-appen ansluten?

MSIX är ett nytt paket format som innehåller många funktioner som syftar till att förbättra paketeringen för alla Windows-appar. Mer information om MSIX finns i [Översikt över MSIX](/windows/msix/overview).

MSIX app Attach är ett sätt att leverera MSIX-program till både fysiska och virtuella datorer. MSIX app Attach skiljer sig dock från vanliga MSIX, eftersom det görs särskilt för virtuella Windows-datorer. Den här artikeln beskriver vad MSIX app Attach är och vad det kan göra åt dig.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Program leverans alternativ i Windows Virtual Desktop

Du kan leverera appar i virtuella Windows-datorer via någon av följande metoder:

- Lägg till appar i en huvud avbildning
- Använd verktyg som SCCM eller Intune för central hantering
- Dynamisk app-etablering (AppV, VMWare AppVolumes eller Citrix AppLayering)
- Skapa anpassade verktyg eller skript med Microsoft och ett verktyg från tredje part

## <a name="what-does-msix-app-attach-do"></a>Vad ansluter MSIX app?

I en distribution av virtuella Windows-datorer kan MSIX app Attach:

- Skapa separering mellan användar data, operativ systemet och appar med hjälp av [MSIX-behållare](/windows/msix/msix-container).
- Ta bort behovet av att paketera om när du levererar program dynamiskt.
- Minska den tid det tar för en användare att logga in.
- Minska infrastruktur krav och kostnader.

MSIX app Attach måste vara tillämpligt utanför VDI eller SBC.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Traditionell app-skiktning jämfört med MSIX app Attach

I följande tabell jämförs nyckel funktionen i MSIX-appens koppling och app-skiktning.

| Funktion | Traditionell app-skiktning  | MSIX app-anslutning  |
|-----|-----------------------------|--------------------|
| Format               | Olika program skikts tekniker kräver olika tillverkarspecifika format. | Fungerar med det interna förpacknings formatet för MSIX.        |
| Omförpacka omkostnader | Tillverkarspecifika format kräver sekvensering och ompaketering per uppdatering.         | Appar som publiceras som MSIX behöver inte paketera om. Om MSIX-paketet inte är tillgängligt gäller dock ompackningen fortfarande. |
| Ekosystem            | Ej tillämpligt (till exempel leverantörer levererar inte App-V)  | MSIX är Microsofts vanliga teknik som viktiga ISV-partners och interna appar som Office antar. Du kan använda MSIX både på virtuella skriv bord och fysiska Windows-datorer. |
| Infrastruktur       | Ytterligare infrastruktur krävs (servrar, klienter och så vidare) | Endast lagring   |
| Administration       | Kräver underhåll och uppdatering   | Fören klar uppdateringar av appar |
| Användarupplevelse      | Påverkar användarens inloggnings tid. Det finns en avgränsning mellan OS-tillstånd, app-tillstånd och användar data.  | Levererade appar kan skilja sig från lokalt installerade program. |

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om MSIX app Attach kan du kolla vår [ord lista](app-attach-glossary.md) och [vanliga frågor och svar](app-attach-faq.md). Annars kan du komma igång med [Konfigurera appen Anslut](app-attach.md).
