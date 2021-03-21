---
title: Genomgång av Exchange-peering
titleSuffix: Azure
description: Genomgång av Exchange-peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586830"
---
# <a name="exchange-peering-walkthrough"></a>Genomgång av Exchange-peering

I det här avsnittet beskrivs de steg du måste följa för att konfigurera och hantera en Exchange-peering.

## <a name="create-an-exchange-peering"></a>Skapa en Exchange-peering
> [!div class="mx-imgBorder"]
> ![Arbets flöde för Exchange-peering och anslutnings status](./media/exchange-peering.png)

Följande steg måste följas för att etablera en Exchange-peering:
1. Granska Microsofts [peering-princip](https://peering.azurewebsites.net/peering) för att förstå kraven för Exchange-peering.
1. Hitta Microsofts peering-plats och peering Facility-ID i [PeeringDB](https://www.peeringdb.com/net/694)
1. Begär Exchange-peering för en peering-plats med hjälp av anvisningarna i [skapa och ändra en Exchange-peering med hjälp av PowerShell](howto-exchange-powershell.md) -artikeln för mer information.
1. När du har skickat en peering-begäran granskar Microsoft begäran och kontaktar dig om det behövs.
1. När det har godkänts ändras anslutnings tillstånd till godkänd
1. Konfigurera BGP-sessionen i slutet och meddela Microsoft
1. Vi etablerar BGP-sessionen med neka ALL-princip och validerar slut punkt till slut punkt.
1. Om det lyckas visas ett meddelande om att peering-anslutningens status är aktiv.
1. Trafiken kommer sedan att tillåtas via den nya peering.

Observera att anslutnings tillstånd inte kan förväxlas med standard tillstånd för [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) -sessioner.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konvertera en äldre Exchange-peering till en Azure-resurs
Följande steg måste följas för att konvertera en äldre Exchange-peering till Azure-resursen:
1. Följ instruktionerna i [konvertera en äldre Exchange-peering till Azure Resource](howto-legacy-exchange-powershell.md)
1. När du har skickat en konverterings förfrågan granskar Microsoft begäran och kontaktar dig om det behövs.
1. När den har godkänts visas din Exchange-peering med anslutnings status som aktiv.

## <a name="deprovision-exchange-peering"></a>Avetablera Exchange-peering
Kontakta [Microsoft-peering](mailto:peering@microsoft.com) för att avetablera Exchange-peering.

När en Exchange-peering är inställd för avetablering visas anslutnings statusen som **PendingRemove**

> [!NOTE]
> Om du kör PowerShell-cmdleten för att ta bort Exchange-peering när anslutnings statusen är ProvisioningStarted eller ProvisioningCompleted kommer åtgärden att Miss par.

## <a name="next-steps"></a>Nästa steg

* Läs om [förutsättningar för att konfigurera peering med Microsoft](prerequisites.md).
