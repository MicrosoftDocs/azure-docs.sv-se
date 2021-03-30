---
title: Så här konfigurerar du pris och tillgänglighet för konsult tjänster i Microsoft Partner Center
description: Lär dig hur du konfigurerar pris information för konsult tjänster och marknads tillgänglighet på Microsofts kommersiella marknads plats med hjälp av Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780237"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>Så här konfigurerar du pris och tillgänglighet för konsult tjänster

Den här artikeln förklarar hur du definierar marknadens tillgänglighet och pris information för ditt konsult tjänst erbjudande på Microsofts kommersiella marknads plats.

> [!NOTE]
> Konsult tjänster erbjuder endast listor. Alla transaktioner måste hanteras mellan dig och dina kunder utanför den kommersiella marknads platsen.

## <a name="markets"></a>Marknaden

I avsnittet **marknader** väljer du de länder eller regioner där konsult tjänsten ska vara tillgänglig.

1. Under **marknader** väljer du länken **Redigera marknader** .
2. I dialog rutan som visas väljer du de marknads platser där du vill göra ditt erbjudande tillgängligt. Du måste välja minst en och högst 141 marknader.
3. Stäng dialog rutan genom att välja **Spara** .

## <a name="preview-audience"></a>Förhandsgranska mål grupp

När du publicerar eller uppdaterar konsult tjänst erbjudandet skapar Partner Center en för hands version av listan. Den här för hands versionen visas bara för användare som har en **Dölj-nyckel**.

I fältet **Dölj nyckel** anger du en alfanumerisk sträng som du använder för att få åtkomst till för hands versionen av ditt erbjudande.

## <a name="pricing-informational-only"></a>Priser (endast information)

I avsnittet **prissättning** anger du om det här är ett kostnads fritt eller betalt erbjudande.

För avgiftsbelagda erbjudanden anger du om priset är fast eller uppskattat. Om priset beräknas måste din erbjudande Beskrivning beskriva vilka faktorer som kommer att påverka priset.

Om du väljer ett land eller en region i avsnittet **marknader** , anger du priset i en valuta som stöds för den marknaden och väljer **Spara utkast**. Se [geografisk tillgänglighet och valuta support för den kommersiella Marketplace](./marketplace-geo-availability-currencies.md) för att visa en lista över valutor som stöds.

Om du väljer flera länder eller regioner i avsnittet **marknader** anger du priset i USA dollar (USD) och väljer **Spara utkast**. Partner Center omvandlar det priset till den lokala valutan för alla valda marknader med de växelkurser som är tillgängliga när du sparade utkastet.

Om du vill validera konverteringen eller ange anpassade priser på en enskild marknad måste du exportera, ändra och sedan importera pris kalkyl bladet:

1. Under **prissättning** väljer du länken **Exportera pris data** . En fil laddas ned till enheten.
1. Öppna exportedPrice.xlsx-filen i Microsoft Excel.
1. I kalkyl bladet kan du justera priser och valutor för varje marknad. Se [geografisk tillgänglighet och valuta support för den kommersiella Marketplace](./marketplace-geo-availability-currencies.md) för att visa en lista över valutor som stöds. Spara filen när du är klar.
1. Välj länken **Importera pris data** under **prissättning** i Partner Center. Om du importerar filen skrivs tidigare pris information över.

> [!IMPORTANT]
> De priser som du definierar i Partner Center är statiska och följer inte variationer i växelkurserna. Om du vill ändra priset på en eller flera marknader efter publiceringen uppdaterar du och skickar ditt erbjudande på nytt i Partner Center.

Välj **Spara utkast** innan du fortsätter.

## <a name="next-steps"></a>Nästa steg

* [Granska och publicera](review-publish-offer.md)