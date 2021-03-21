---
title: Genomgång av Peering Service för partner
titleSuffix: Azure
description: Genomgång av Peering Service för partner
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 292c850f5352c80112e3f0bcc5c868f8128819be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592321"
---
# <a name="peering-service-partner-walkthrough"></a>Genomgång av Peering Service för partner

I det här avsnittet beskrivs de steg som en provider måste följa för att aktivera en direkt peering för peering-tjänsten.

## <a name="create-direct-peering-connection-for-peering-service"></a>Skapa direkt peering-anslutning för peering-tjänsten
Tjänste leverantörer kan utöka sin geografiska räckvidd genom att skapa en ny direkt peering som stöder peering-tjänsten. För att åstadkomma detta
1. Bli en peering service-partner om den inte redan är
1. Följ instruktionerna för att [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md). Se till att det uppfyller kraven för hög tillgänglighet.
1. Följ sedan stegen för att [Aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Använd äldre direkt peering-anslutning för peering-tjänsten
Om du har äldre direkt peering som du vill använda för att stödja peering-tjänsten
1. Bli en peering service-partner om du inte redan gjort det.
1. Följ instruktionerna för att [konvertera en äldre direkt peering till Azure-resursen med hjälp av portalen](howto-legacy-direct-portal.md). Om det behövs beställer du ytterligare kretsar för att uppfylla kraven på hög tillgänglighet.
1. Följ sedan stegen för att [Aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [peering-principen](https://peering.azurewebsites.net/peering).
* Om du vill veta mer om hur du konfigurerar direkt peering med Microsoft följer du [genom gången av direkt peering](walkthrough-direct-all.md).
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [genom gången av Exchange-peering](walkthrough-exchange-all.md).