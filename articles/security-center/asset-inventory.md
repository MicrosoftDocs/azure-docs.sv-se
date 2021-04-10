---
title: Azure Security Center till gångs lager
description: Lär dig mer om Azure Security Center till gångs hantering som ger fullständig insyn i alla Security Center övervakade resurser.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/10/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 873fdba1d24db55b3269cc2c13f0140da4a9b4e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393367"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory"></a>Utforska och hantera dina resurser med till gångs inventering

Sidan till gångs inventeringen för Azure Security Center ger en enda sida för att Visa säkerhets position för de resurser som du har anslutit till Security Center. 

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen.

När en resurs har utestående rekommendationer visas de i inventeringen.

Använd den här vyn och dess filter för att åtgärda sådana frågor som:

- Vilka av mina prenumerationer med Azure Defender aktiverat har du enastående rekommendationer?
- Vilka av mina datorer med taggen "produktion" saknas Log Analytics agenten?
- Hur många av mina datorer som är taggade med en speciell tagg har du enastående rekommendationer?
- Hur många resurser i en speciell resurs grupp har säkerhets resultat från en sårbarhets bedömnings tjänst?

Till gångs hanterings möjligheterna för det här verktyget är väsentliga och fortsätter att växa. 

> [!TIP]
> Säkerhets rekommendationerna på sidan till gångs inventering är samma som på sidan **rekommendationer** , men de visas i enlighet med den berörda resursen. Information om hur du löser rekommendationer finns [i implementera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md).


## <a name="availability"></a>Tillgänglighet
|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kostnadsfri|
|Nödvändiga roller och behörigheter:|Alla användare|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Vilka är huvud funktionerna i till gångs inventering?
Inventerings sidan innehåller följande verktyg:

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Huvud funktionerna på sidan till gångs inventering i Azure Security Center" lightbox="media/asset-inventory/highlights-of-inventory.png":::


### <a name="1---summaries"></a>1 – sammanfattningar
Innan du definierar några filter, visar en framträdande remsa med värden överst i vyn inventering:

- **Totalt antal resurser**: det totala antalet resurser som är anslutna till Security Center.
- **Felaktiga resurser**: resurser med aktiva säkerhets rekommendationer. [Läs mer om säkerhets rekommendationer](security-center-recommendations.md).
- **Oövervakade resurser**: resurser med agent övervaknings problem – de har Log Analytics agenten distribueras, men agenten skickar inte data eller har andra hälso problem.
- **Oregistrerade prenumerationer**: alla prenumerationer i det valda omfånget som ännu inte har anslutits till Azure Security Center.

### <a name="2---filters"></a>2-filter
De flera filtren överst på sidan ger ett sätt att snabbt förfina listan över resurser enligt den fråga som du försöker besvara. Om du till exempel vill svara på frågan *vilka av mina datorer med taggen "produktion" saknas Log Analytics agenten?* du kan kombinera **agent övervaknings** filtret med filtret **taggar** .

När du har tillämpat filter uppdateras sammanfattnings värdena så att de relaterar till frågeresultaten. 

### <a name="3---export-and-asset-management-tools"></a>3 – export-och till gångs hanterings verktyg

**Export alternativ** – inventering innehåller ett alternativ för att exportera resultaten av de valda filter alternativen till en CSV-fil. Du kan också exportera själva frågan till Azure Resource Graph Explorer för att ytterligare förfina, Spara eller ändra KQL-frågan (Query Language) i Kusto.

> [!TIP]
> KQL-dokumentationen innehåller en databas med några exempel data tillsammans med några enkla frågor för att få "känsla" för språket. [Läs mer i den här självstudien om KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

Med **alternativ för till gångs hantering** – med lager kan du utföra komplexa identifierings frågor. När du har hittat de resurser som matchar dina frågor tillhandahåller inventeringen genvägar för åtgärder som:

- Tilldela taggar till de filtrerade resurserna – Markera kryss rutorna bredvid de resurser som du vill tagga.
- Publicera nya servrar till Security Center – Använd verktygsfälts knappen **Lägg till icke-Azure-servrar** .
- Automatisera arbets belastningar med Azure Logic Apps – Använd knappen **Utlös Logic app** för att köra en Logic app på en eller flera resurser. Dina Logi Kap par måste förberedas i förväg och acceptera relevant utlösare (HTTP-begäran). [Läs mer om Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="how-does-asset-inventory-work"></a>Hur fungerar inventarie Inventory?

Till gångs lagret använder sig av ett [arg (Azure Resource Graph)](../governance/resource-graph/index.yml), en Azure-tjänst som ger möjlighet att fråga Security Centers säkerhets position data över flera prenumerationer.

ARG är utformat för att tillhandahålla effektiv resurs utforskning med möjlighet att fråga i stor skala.

Med hjälp av [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/)kan till gångs inventering snabbt producera djup insikter genom att korsa referenser med andra resurs egenskaper.


## <a name="how-to-use-asset-inventory"></a>Använda till gångs inventering

1. Välj **inventering** i Security Center marginal List.

1. Använd rutan **Filtrera efter namn** för att visa en speciell resurs eller använd filtren enligt beskrivningen nedan.

1. Välj de relevanta alternativen i filtren för att skapa en speciell fråga som du vill utföra.

    Som standard sorteras resurserna efter antalet aktiva säkerhets rekommendationer.

    > [!IMPORTANT]
    > Alternativen i varje filter är speciella för resurserna i de markerade prenumerationerna **och** dina val i de andra filtren.
    >
    > Om du till exempel har valt endast en prenumeration och prenumerationen inte har några resurser med utestående säkerhets rekommendationer för att åtgärda (0 resurser som inte är felfria), har inte **rekommendations** filtret några alternativ. 

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Använd filter alternativen i Azure Security Center till gångs lager för att filtrera resurser till produktions resurser som inte övervakas":::

1. Om du vill använda **säkerhets resultaten innehåller** filter anger du FRITEXT från ID, säkerhets kontroll eller CVE namn på ett säkerhets problem som söker efter filter till de berörda resurserna:

    !["Säkerhets resultaten innehåller" filter](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **Säkerhets resultaten innehåller** och **taggarna** filtrerar bara ett enda värde. Om du vill filtrera efter mer än ett använder du **Lägg till filter**.

1. Om du vill använda **Azure Defender** -filtret väljer du ett eller flera alternativ (av, på eller delvis):

    - **Av** -resurser som inte skyddas av en Azure Defender-plan. Du kan högerklicka på någon av dessa och uppgradera dem:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Uppgradera en resurs till Azure Defender från höger klick" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    -  Resurser som skyddas av en Azure Defender-plan
    - **Delvis** – detta gäller **prenumerationer** som har vissa men inte alla Azure Defender-planer inaktiverade. Följande prenumeration har till exempel fem Azure Defender-planer inaktiverade. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Prenumeration delvis i Azure Defender":::

1. Om du vill undersöka resultatet av din fråga ytterligare väljer du de resurser som intresserar dig.

1. Om du vill visa de aktuella valda filter alternativen som en fråga i resurs diagram Utforskaren väljer du **Öppna fråga**.

    ![Inventerings fråga i ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Köra en tidigare definierad Logic-app med 

1. Om du har definierat vissa filter och lämnat sidan öppen kan Security Center inte uppdatera resultatet automatiskt. Eventuella ändringar i resurserna påverkar inte de resultat som visas om du inte manuellt laddar om sidan eller väljer **Uppdatera**.


## <a name="faq---inventory"></a>Vanliga frågor och svar – inventering

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Varför visas inte alla mina prenumerationer, datorer, lagrings konton osv.

I vyn lager visas dina Security Center anslutna resurser från ett CSPM-perspektiv (Cloud Security position Management). Filtren returnerar inte alla resurser i miljön. endast de med de utestående (eller "aktiva") rekommendationerna. 

Följande skärm bild visar till exempel en användare med åtkomst till 38-prenumerationer, men endast 10 har för närvarande rekommendationer. Så när de filtrerar efter **resurs typ = prenumerationer** visas endast de 10 prenumerationer med aktiva rekommendationer i inventeringen:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Inget retur resultat returnerades när det inte finns några aktiva rekommendationer":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>Varför visar några av mina resurser tomma värden i kolumnerna för Azure Defender eller agent övervakning?

Alla Security Center övervakade resurser har inte agenter. Till exempel Azure Storage konton eller PaaS resurser som diskar, Logic Apps, Data Lake analys och Event Hub.

När priser eller agent övervakning inte är relevanta för en resurs, visas inget i dessa kolumner i inventeringen.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Vissa resurser visar tom information i agent övervakning eller Azure Defender-kolumner":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs sidan till gångs lager i Azure Security Center.

Mer information om relaterade verktyg finns på följande sidor:

- [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)