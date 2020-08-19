---
title: Azure Security Center till gångs lager
description: Lär dig mer om Azure Security Center till gångs hantering som ger fullständig insyn i alla Security Center övervakade resurser.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 7fab15ae4e9e58af58a4490beb2d512379976d1c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264007"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Utforska och hantera dina resurser med till gångs inventering och hanterings verktyg

Sidan till gångs inventeringen för Azure Security Center ger en enda sida för att Visa säkerhets position för de resurser som du har anslutit till Security Center. 

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen.

När en resurs har utestående rekommendationer visas de i inventeringen.

Använd den här vyn och dess filter för att åtgärda sådana frågor som:

- Vilken av mina standard prenumerationer på standard nivån har enastående rekommendationer?
- Vilka av mina datorer med taggen "produktion" saknas Log Analytics agenten?
- Hur många av mina datorer, taggade med en speciell tagg har du enastående rekommendationer?
- Hur många resurser i en speciell resurs grupp har säkerhets resultat från en sårbarhets bedömnings tjänst?

Till gångs hanterings möjligheterna för det här verktyget är väsentliga och fortsätter att växa. 


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Förhandsgranskning|
|Priset|Ingenting|
|Nödvändiga roller och behörigheter:|Alla användare|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Nej](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Vilka är huvud funktionerna i till gångs inventering?

Inventerings sidan innehåller följande verktyg:

- **Sammanfattningar** – innan du definierar några filter visas en framträdande remsa med värden överst i vyn inventering:

    - **Totalt antal resurser**: det totala antalet resurser som är anslutna till Security Center.
    - **Felaktiga resurser**: resurser med aktiva säkerhets rekommendationer. [Läs mer om säkerhets rekommendationer](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - **Oövervakade resurser**: resurser med agent övervaknings problem – de har Log Analytics agenten distribueras, men agenten skickar inte data eller har andra hälso problem.

- **Filter** – flera filter överst på sidan ger ett sätt att snabbt förfina listan över resurser enligt den fråga som du försöker besvara. Om du till exempel vill svara på frågan *vilka av mina datorer med taggen "produktion" saknas Log Analytics agenten?* du kan kombinera **agent övervaknings** filtret med **tagg** filtret som du ser i följande klipp:

    ![Filtrering av produktions resurser som inte övervakas](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    När du har tillämpat filter uppdateras sammanfattnings värdena så att de relaterar till frågeresultaten. 

- **Export alternativ** – inventering ger möjlighet att exportera resultaten av de valda filter alternativen till en CSV-fil. Dessutom kan du exportera själva frågan till Azure Resource Graph Explorer för att ytterligare förfina, Spara eller ändra KQL-frågan.

    ![Lager export alternativ](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > KQL-dokumentationen innehåller en databas med några exempel data tillsammans med några enkla frågor för att få "känsla" för språket. [Läs mer i den här självstudien om KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- Med **alternativ för till gångs hantering** – med lager kan du utföra komplexa identifierings frågor. När du har hittat de resurser som matchar dina frågor tillhandahåller inventeringen genvägar för åtgärder som:

    - Tilldela taggar till de filtrerade resurserna – Markera kryss rutorna bredvid de resurser som du vill tagga
    - Publicera nya servrar till Security Center – Använd verktygsfälts knappen **Lägg till icke-Azure-servrar**


## <a name="how-does-asset-inventory-work"></a>Hur fungerar inventarie Inventory?

Till gångs lagret använder sig av ett [arg (Azure Resource Graph)](https://docs.microsoft.com/azure/governance/resource-graph/), en Azure-tjänst som ger möjlighet att fråga Security Centers säkerhets position data över flera prenumerationer.

ARG är utformat för att tillhandahålla effektiv resurs utforskning med möjlighet att fråga i stor skala.

Med hjälp av [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)kan till gångs inventering snabbt producera djup insikter genom att korsa referenser med andra resurs egenskaper.


## <a name="how-to-use-asset-inventory"></a>Använda till gångs inventering

1. Välj **inventering**i Security Center marginal List.

1. Om du vill visa en speciell resurs anger du namnet i rutan **Filtrera efter namn** .

1. Välj de relevanta alternativen i filtren för att skapa en speciell fråga som du vill utföra.

    ![Inventerings filter](./media/asset-inventory/inventory-filters.png)

    Som standard sorteras resurserna efter antalet aktiva säkerhets rekommendationer.

    > [!IMPORTANT]
    > Alternativen i varje filter är speciella för resurserna i de markerade prenumerationerna **och** dina val i de andra filtren.
    >
    > Om du till exempel har valt endast en prenumeration och prenumerationen inte har några resurser med utestående säkerhets rekommendationer för att åtgärda (0 resurser som inte är felfria), har inte **rekommendations** filtret några alternativ. 

1. Om du vill använda **säkerhets resultaten innehåller** filter anger du FRITEXT från ID, säkerhets kontroll eller CVE namn på ett säkerhets problem som söker efter filter till de berörda resurserna:

    !["Säkerhets resultaten innehåller" filter](./media/asset-inventory/security-findings-contain-elements.png)

1. Om du vill använda filtret för **pris nivå** väljer du ett eller flera alternativ (ledig, delvis eller standard):

    - **Kostnads fria** resurser som finns på den kostnads fria pris nivån
    - **Standard** – resurser som finns på standard pris nivån
    - **Delvis** – detta gäller för prenumerationer på standard pris nivån men har några av de valfria säkerhets planerna inaktiverade. Följande prenumeration är till exempel på standard nivån men har fem element på standard nivån inaktive rad. 

        ![Prenumeration på standard pris nivå (delvis)](./media/asset-inventory/pricing-tier-partial.png)

1. Om du vill undersöka resultatet av din fråga ytterligare väljer du de resurser som intresserar dig.

1. Du kan också välja **Visa i resurs diagram Utforskaren** för att öppna frågan i resurs diagram Utforskaren.

    ![Inventerings fråga i ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Om du har definierat vissa filter och lämnat sidan öppen kan Security Center inte uppdatera resultatet automatiskt. Eventuella ändringar i resurserna påverkar inte de resultat som visas om du inte manuellt laddar om sidan eller väljer **Uppdatera**.


## <a name="faq---inventory"></a>Vanliga frågor och svar – inventering

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Varför visas inte alla mina prenumerationer, datorer, lagrings konton osv.

I vyn lager visas dina resurser från ett CSPM-perspektiv (Cloud Security position Management). Filtren returnerar inte alla resurser i miljön. endast de med de utestående (eller "aktiva") rekommendationerna. 

Om du till exempel har nio prenumerationer men bara åtta för närvarande har rekommendationer, och du filtrerar efter **resurs typ = prenumerationer** , visas bara de åtta prenumerationerna med aktiva rekommendationer:

![Inget retur resultat returnerades när det inte finns några aktiva rekommendationer](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Varför visar några av mina resurser tomma värden i kolumnerna priser eller agent övervakning?

Alla Security Center övervakade resurser har inte agenter. Till exempel Azure Storage konton eller PaaS resurser som diskar, Logic Apps, Data Lake analys och Event Hub.

När priser eller agent övervakning inte är relevanta för en resurs, visas inget i dessa kolumner i inventeringen.

![Vissa resurser visar tom information i kolumner för agent övervakning eller pris sättning](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs sidan till gångs lager i Azure Security Center.

Mer information om relaterade verktyg finns på följande sidor:

- [Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)