---
title: Genomgång av Direct-peering
titleSuffix: Azure
description: Genomgång av Direct-peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592389"
---
# <a name="direct-peering-walkthrough"></a>Genomgång av Direct-peering

I det här avsnittet beskrivs de steg du måste följa för att konfigurera och hantera en direkt peering.

## <a name="create-a-direct-peering"></a>Skapa en direkt peering
> [!div class="mx-imgBorder"]
> ![Arbets flöde för direkt peering och anslutnings tillstånd](./media/direct-peering.png)

Följande steg måste följas för att tillhandahålla en direkt peering:
1. Granska Microsofts [peering-princip](https://peering.azurewebsites.net/peering) för att förstå krav för direkt peering.
1. Följ instruktionerna i [skapa eller ändra en direkt peering](howto-direct-powershell.md) för att skicka en peering-begäran.
1. När du har skickat in en peering-begäran kommer Microsoft att kontaktas med din registrerade e-postadress för att tillhandahålla LOA (rem bur tillstånd) eller för annan information.
1. När peering-begäran har godkänts ändras anslutnings status till ProvisioningStarted.
1. Du måste:
    1. Slutför kablar enligt LOA
    1. (valfritt) utför Link-test med 169.254.0.0/16
    1. Konfigurera BGP-sessionen och meddela oss sedan oss.
1. Microsoft etablerar BGP-sessionen med neka ALL-princip och validerar slut punkt till slut punkt.
1. Om det lyckas visas ett meddelande om att peering-anslutningens status är aktiv.
1. Trafiken kommer sedan att tillåtas via den nya peering.

Observera att anslutnings tillstånd inte kan förväxlas med standard tillstånd för [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) -sessioner.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Konvertera en äldre Direct-peering till en Azure-resurs
Följande steg måste följas för att konvertera en äldre direkt peering till Azure-resursen:
1. Följ instruktionerna i [konvertera en äldre direkt peering till Azure Resource](howto-legacy-direct-powershell.md)
1. När du har skickat en konverterings förfrågan granskar Microsoft begäran och kontaktar dig om det behövs.
1. När den har godkänts visas din direkta peering med anslutnings status som aktiv.

## <a name="deprovision-direct-peering"></a>Avetablera direkt peering
Kontakta [Microsofts peering](mailto:peering@microsoft.com) -team för att avetablera direkt peering.

När en direkt peering har angetts för avetablering visas anslutnings statusen som **PendingRemove**

> [!NOTE]
> Om du kör PowerShell-cmdleten för att ta bort direkt peering när ConnectionState är ProvisioningStarted eller ProvisioningCompleted kommer åtgärden att Miss par.

## <a name="next-steps"></a>Nästa steg

* Läs om [förutsättningar för att konfigurera peering med Microsoft](prerequisites.md).
