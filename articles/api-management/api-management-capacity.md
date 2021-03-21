---
title: Kapacitet för en Azure API Management-instans | Microsoft Docs
description: I den här artikeln förklaras hur kapacitets måttet är och hur du fattar välgrundade beslut om att skala en Azure API Management-instans.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081067"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapacitet hos en Azure API Management-instans

**Kapaciteten** är det viktigaste [Azure Monitor måttet](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) för att fatta välgrundade beslut om att skala en API Management-instans för att få plats med mer belastning. Dess konstruktion är komplex och medför vissa beteenden.

Den här artikeln förklarar vad **kapaciteten** är och hur den beter sig. Den visar hur du får åtkomst till **kapacitets** mått i Azure Portal och föreslår när du ska överväga att skala eller uppgradera API Management-instansen.

> [!IMPORTANT]
> Den här artikeln beskriver hur du kan övervaka och skala Azure API Management-instansen baserat på dess kapacitets mått. Det är dock lika viktigt att förstå vad som händer när en enskild API Management instans faktiskt har *nått* sin kapacitet. Azure API Management tillämpar inte någon begränsning på service nivå för att förhindra en fysisk överbelastning av instanserna. När en instans når sin fysiska kapacitet kommer den att fungera på samma sätt som alla överbelastade webb servrar som inte kan bearbeta inkommande begär Anden: fördröjningen ökar, anslutningar tas bort, timeout-fel uppstår osv. Det innebär att API-klienterna bör förberedas för att hantera den här risken på samma sätt som med andra externa tjänster (t. ex. genom att tillämpa principer för återförsök).

## <a name="prerequisites"></a>Förutsättningar

För att följa stegen i den här artikeln måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Vad är kapacitet

![Diagram som förklarar kapacitets måttet.](./media/api-management-capacity/capacity-ingredients.png)

**Kapacitet** är en indikator för belastningen på en API Management-instans. Den visar resursernas användning (processor, minne) och Kölängd-nätverk. PROCESSOR-och minnes användningen visar resurser förbrukningen genom att:

+ API Management data Plans tjänster, t. ex. bearbetning av begär Anden, som kan omfatta vidarebefordrande begär Anden eller köra en princip.
+ API Management Management plan-tjänster, till exempel hanterings åtgärder som tillämpas via Azure Portal eller ARM, eller belastning som kommer från [Developer-portalen](api-management-howto-developer-portal.md).
+ Valda operativ system processer, inklusive processer som inkluderar kostnaden för TLS-handskakning på nya anslutningar.

Total **kapacitet** är ett genomsnitt av sina egna värden från varje enhet i en API Management instans.

Även om **kapacitets måttet** är utformat för att visa problem med API Management-instansen, finns det fall när problem inte återspeglas i förändringar i **kapacitets måttet**.

## <a name="capacity-metric-behavior"></a>Beteende för kapacitets mått

På grund av sin konstruktion kan i Real livs **kapacitet** påverkas av många variabler, till exempel:

+ anslutnings mönster (ny anslutning på en begäran jämfört med den befintliga anslutningen)
+ storlek på en begäran och ett svar
+ principer som kon figurer ATS på varje API eller antal klienter som skickar begär Anden.

Ju mer komplexa åtgärder som begärs, desto högre är **kapacitets** förbrukningen. Till exempel använder komplexa omvandlings principer mycket mer processor än en enkel vidarebefordring av förfrågningar. Svar på långsamma Server dels tjänster ökar också.

> [!IMPORTANT]
> **Kapaciteten** är inte ett direkt mått på antalet begär Anden som bearbetas.

![Toppar i kapacitets mått](./media/api-management-capacity/capacity-spikes.png)

**Kapaciteten** kan också överföras med jämna mellanrum eller vara större än noll, även om det inte finns några begär Anden som bearbetas. Det inträffar på grund av system-eller plattformsspecifika åtgärder och bör inte beaktas när du bestämmer om du vill skala en instans.

Låg **kapacitets mått** innebär inte nödvändigt vis att API Management instansen inte drabbas av några problem.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Använd Azure Portal för att undersöka kapaciteten
  
![Kapacitetsmått](./media/api-management-capacity/capacity-metric.png)  

1. Navigera till din APIM-instans i [Azure Portal](https://portal.azure.com/).
2. Välj **Mått**.
3. I det lila avsnittet väljer du **kapacitets** mått från tillgängliga mått och **lämnar standard agg** regeringen.

    > [!TIP]
    > Du bör alltid titta på en **kapacitets** mått analys per plats för att undvika fel tolkning.

4. I avsnittet grönt väljer du **plats** för att dela måttet efter dimension.
5. Välj önskat tidsintervall från det översta fältet i avsnittet.

    Du kan ange en mått varning för att meddela dig när något oväntade inträffar. Du kan till exempel få meddelanden när APIM-instansen har överskridit den förväntade högsta kapaciteten under mer än 20 minuter.

    >[!TIP]
    > Du kan konfigurera aviseringar så att du vet när tjänsten håller på att få slut på kapacitet eller använder Azure Monitor automatisk skalnings funktion för att automatiskt lägga till en Azure API Management-enhet. Skalnings åtgärden kan ta cirka 30 minuter, så du bör planera reglerna på motsvarande sätt.  
    > Det går bara att skala huvud platsen.

## <a name="use-capacity-for-scaling-decisions"></a>Använd kapacitet för skalnings beslut

**Kapacitet** är måttet för att fatta beslut om att skala en API Management-instans för att hantera mer belastning. Tänk på att:

+ Titta på långsiktiga trender och genomsnitt.
+ Ignorera plötsliga toppar som troligen inte är relaterade till någon ökning i belastningen (se avsnittet "kapacitets måttets beteende" för förklaring).
+ Att uppgradera eller skala din instans när **kapacitet** svärdet överstiger 60% eller 70% under en längre tid (till exempel 30 minuter). Olika värden kan fungera bättre för din tjänst eller ditt scenario.

>[!TIP]  
> Om du kan beräkna din trafik i förväg testar du APIM-instansen på de arbets belastningar du förväntar dig. Du kan öka belastningen på begäran på din klient gradvis och övervaka vilket värde som kapacitets måttet motsvarar din högsta belastning. Följ stegen i föregående avsnitt för att använda Azure Portal för att förstå hur mycket kapacitet som används vid en specifik tidpunkt.

## <a name="next-steps"></a>Nästa steg

- [Skala eller uppgradera en Azure API Management-tjänstinstans](upgrade-and-scale.md)
- [Optimera och Spara på dina moln utgifter](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)