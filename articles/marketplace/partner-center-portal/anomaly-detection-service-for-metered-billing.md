---
title: Avvikelse identifierings tjänst för avgiftsbelagda fakturerings Microsoft Azure Marketplace
description: Beskriver hur avvikelse identifiering fungerar, när meddelanden skickas och vad du kan göra med dem, samt support alternativ.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: ac3e07c67ca82c2960de1c4341a714b33751bfc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092408"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Tjänst för att identifiera avvikelser vid förbrukningsbaserad fakturering

Med [Marketplace för avläsning](marketplace-metering-service-apis-faq.md) av program vara kan du skapa erbjudanden i det kommersiella Marketplace-programmet som debiteras enligt andra enheter än standard enheter. Med avgiftsbelagd fakturering skickar du användnings händelser för kundens användning till Microsoft och vi förbereder faktureringen baserat på den användningen.

Felaktiga användnings data kan komma från olika orsaker, t. ex. buggar, fel konfigurationer i förbruknings spårningen eller bedrägerier. Felaktiga användnings data leder till felaktiga kund avgifter och fakturerings tvister.

För att minska risken använder vår avvikelse identifierings tjänst Machine Learning-algoritmer för att fastställa det normala fakturerings beteendet för mätningen, analysera den avgiftsbelagda fakturerings användningen och upptäcka avvikelser med minimal inblandning av användaren.

Du får ett meddelande om avvikelser upptäcks i din avgiftsbelagda fakturerings användning. Det ger dig möjlighet att undersöka och meddela oss om en avvikelse bekräftas vara ett reellt problem, där punkt åtgärder kan vidtas för att lösa kundens fakturerings problem proaktivt.

Förutom plötsliga toppar, DIP-och trend ändringar i den avgiftsbelagda fakturerings användningen, har vår modell också konto för säsongs effekter. Eftersom den avgiftsbelagda faktureringen kommunicerar via överförbruknings data kan vår modell också hantera långa perioder av data som saknas.

Följande är exempel på avvikelse identifierings resultat. Det förväntade intervallet visas som ett gult band. Acceptabel fakturerings användning för mätare visas som gröna stjärnor på bandet. Fakturerings användning utanför bandet visas som en röd punkt.  

Avvikelser identifierade utanför en förutsägbar trend:

![Visar avvikelser identifierade utanför en förutsägbar trend.](media/anomaly-1.png)

Avvikelser identifierade utanför en återkommande cyklisk trend:

![Visar avvikelser identifierade utanför en återkommande cyklisk trend.](media/anomaly-2.png)

Avvikelser identifierade i en uppåt-trend:

![Visar avvikelser identifierade i en uppåt-trend.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Så här fungerar avvikelse identifierings tjänsten

Avvikelse identifiering aktive ras automatiskt för all avgiftsbelagd fakturerings användning. När du skickar användnings händelser till Microsoft skapar avvikelse identifierings tjänsten en modell med förväntade värden baserat på tidigare användnings data. Den här modellen körs varje vecka.

Avvikelse identifierings funktioner på per mätare och per kund nivå. Det innebär att varje mätare med varje kund har en modell som har tränats baserat på den här kundens tidigare användnings mönster för den här mätaren.

Modellen fungerar genom att skapa retroaktiva konfidens intervall. Tids serie prognosen är en generaliserad additiv modell bestående av en trend förutsägelse del och en säsongs beroende del. Eftersom modellen formuleras som en Regressions uppgift kan den hantera långa perioder av data som saknas. Om en observation faller utanför de förväntade konfidens intervallen innebär det att observationen inte kan förklaras baserat på historiska mönster i den avgiftsbelagda faktureringen och kan därför vara en avvikelse.

## <a name="anomaly-detection-notification"></a>Meddelande om avvikelse identifiering

Du kan utvärdera, hantera och bekräfta avvikelser i Partner Center. Mer information finns i [Hantera avlästa fakturerings avvikelser i Partner Center](../anomaly-detection.md).

För att säkerställa att dina kunder inte överbelastas för mätnings användning bör du undersöka om identifierade avvikelser är verkliga problem. I så fall kan du godkänna felaktig användning i Partner Center.

Vi rekommenderar att du bekräftar om identifierade avvikelser är normal användning. På så sätt kan du förbättra de avvikelser som vi tillhandahåller. Om en avvikelse representerar en potentiellt hög ekonomisk risk kan vi kontakta dig för att bekräfta användningen.

## <a name="when-and-how-to-get-support"></a>När och hur man får support

Om du har skickat oss en felaktig mätnings användning som gjorde eller leder till en underkostnad till kunden, kommer vi inte att initiera någon faktura till kunden om du har rapporterat användningen eller betalar dig för den användningen. Du får själv stå för kostnaderna som beror på underrapporteringen.

Om något av följande fall gäller kan du justera användnings beloppet i Partner Center, vilket leder till en åter betalning eller fakturerings justering för dina kunder:

- Du bekräftade att ett av de avvikelser som vi påträffade är ett verkligt problem och att den felaktiga användningen skulle leda till att kunden överbelastas.
- Du upptäcker att du har skickat felaktig användning till oss och att den felaktiga användningen skulle leda till att kunden överbelastas.

Så här skickar du ett support ärende som rör avgiftsbelagda fakturerings avvikelser:

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) med ditt arbets konto.
1. Välj ikonen **support** på menyn längst upp till höger på sidan. Rutan **Hjälp och support** visas till höger på sidan.
1. Om du behöver hjälp med den kommersiella Marketplace väljer du **kommersiell marknads** plats.
   ![Visar fönstret support.](../media/support/commercial-marketplace-support-pane.png)
1. I rutan **problem Sammanfattning** anger du > för försäljnings **marknads plats avgiftsbelagd fakturering**.
1. I rutan **problem typ** väljer du något av följande:
    - **Marknads plats > avgiftsbelagd fakturerings > fel användning har skickats för Azure-program**
    - **Marknads plats > avgiftsbelagd fakturerings > fel användning har skickats för SaaS-erbjudandet**
1. Under **Nästa steg** väljer du **Granska lösningar**.
1. Granska de rekommenderade dokumenten, om det finns några eller Välj **ge ärende information** för att skicka in ett support ärende.

Fler support alternativ för utgivare finns i [Support för programmet för kommersiella marknads platser i Partner Center](../support.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [API för avläsning av marknads](marketplace-metering-service-apis.md)plats.
- [Hantera avgiftsbelagda fakturerings avvikelser i Partner Center](../anomaly-detection.md)
