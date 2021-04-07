---
title: Identifiera avancerade hot med användar-och enhets beteende analys (UEBA) i Azure Sentinel | Microsoft Docs
description: Skapa beteende bas linjer för entiteter (användare, värdnamn, IP-adresser) och Använd dem för att identifiera avvikande beteende och identifiera avancerade beständiga hot på noll dagar (APT).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: bf7a17d96d31fd4214d5465a5739acc9ce9a9d53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455509"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Identifiera avancerade hot med användar-och enhets beteende analys (UEBA) i Azure Sentinel

> [!IMPORTANT]
>
> - Funktionerna UEBA och enhets sidor är nu **allmänt tillgängliga** i **_alla_** geografiska diagram och regioner i Azure Sentinel.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Vad är användar-och enhets beteende analys (UEBA)?

### <a name="the-concept"></a>Konceptet

Identifiera hot i din organisation och deras potentiella påverkan – om en komprometterad entitet eller en skadlig Insider-har alltid varit en tids krävande och arbets intensiv process. Genom att gå igenom aviseringar, ansluta punkterna och den aktiva jakten lägger du till upp till enorma mängder tid och ansträngning med minimala returer och risken för sofistikerade hot som bara evading identifiering. Särskilt Elusive hot, t. ex. noll dagar, riktade och avancerade hot, kan vara det som är mest skadligt för din organisation, vilket gör att identifieringen är viktig.

UEBA-funktionen i Azure Sentinel eliminerar drudgery från dina analytikers arbets belastningar och osäkerhet från deras ansträngningar, och levererar hög åter givning, användbar information, så att de kan fokusera på undersökningar och åtgärder.

När Azure Sentinel samlar in loggar och aviseringar från alla anslutna data källor, analyseras de och skapar bas linje beteende profiler för organisationens entiteter (till exempel användare, värdar, IP-adresser och program) för tids-och grupp horisont. Med hjälp av en mängd olika tekniker och maskin inlärnings funktioner kan Azure Sentinel identifiera avvikande aktivitet och hjälpa dig att avgöra om en till gång har komprometterats. Det är inte bara det, men det kan också ta reda på den relativa känsligheten för särskilda till gångar, identifiera peer-grupper med till gångar och utvärdera den potentiella effekten av en viss komprometterad till gång (dess "Mas radie"). Med den här informationen kan du på ett effektivt sätt prioritera din undersökning och incident hantering. 

### <a name="architecture-overview"></a>Översikt över arkitekturen

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Arkitektur för enhets beteende analys":::

### <a name="security-driven-analytics"></a>Säkerhets driven analys

Azure Sentinel har inspirerats av Gartners paradigm för UEBA-lösningar och tillhandahåller en "yttre" metod, baserat på tre referens ramar:

- **Användnings fall:** Genom att prioritera relevanta angrepps vektorer och scenarier baserade på säkerhets undersökningar som är justerade med MITRE till&vikt ramverk för taktiker, tekniker och undertekniker som placerar olika entiteter som offer, perpetrators eller Pivot Points i Kill-kedjan. Azure Sentinel fokuserar särskilt på de mest värdefulla loggar som varje data källa kan ge.

- **Data Källor:** Medan Azure-datakällor först och främst har stöd för, väljer Azure Sentinel data källor från tredje part för att tillhandahålla data som matchar våra Hot scenarier.

- **Analys:** Med hjälp av olika algoritmer för Machine Learning (ML) identifierar Azure Sentinel avvikande aktiviteter och visar tydligt och koncist bevis i form av sammanhangsbaserade berikade funktioner, några exempel på som visas nedan.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Beteende analys utanför-i-metod":::

I Azure Sentinel presenteras artefakter som hjälper dina säkerhetsanalytiker att få en tydlig förståelse för avvikande aktiviteter i sammanhang och i jämförelse med användarens bas linje profil. Åtgärder som utförs av en användare (eller en värd eller en adress) utvärderas kontextuellt, där resultatet "sant" indikerar en identifierad avvikelse:
- mellan geografiska platser, enheter och miljöer.
- över tids-och frekvens horisonter (jämfört med användarens egna historik).
- jämfört med peer-beteende.
- jämfört med organisationens beteende.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Enhets kontext":::


### <a name="scoring"></a>Resultat

Varje aktivitet får poäng enligt "bedömnings prioritet Poäng", som avgör sannolikheten för en speciell användare som utför en speciell aktivitet, baserat på beteende inlärning av användaren och deras peer-datorer. Aktiviteter som identifieras som mest onormala får högst poäng (på en skala på 0-10).

Se hur beteende analys används i [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) för ett exempel på hur det fungerar.

## <a name="entity-pages"></a>Enhets sidor

Lär dig mer om [entiteter i Azure Sentinel](entities-in-azure-sentinel.md) och se den fullständiga listan över [enheter som stöds och identifierare](entities-reference.md).

När du stöter på en entitet (som för närvarande är begränsad till användare och värdar) i en sökning, en avisering eller en undersökning, kan du välja entiteten och gå till en **entitet**, ett datablad som är fullt värdefullt information om entiteten. De typer av information som du hittar på den här sidan är grundläggande fakta om entiteten, en tids linje för viktiga händelser som är relaterade till den här entiteten och insikter om entitetens beteende.
 
Enhets sidor består av tre delar:
- Den vänstra panelen innehåller entitetens identifierings information som samlas in från data källor som Azure Active Directory, Azure Monitor, Azure Security Center och Microsoft Defender.

- Panelen i mitten visar en grafisk och text tids linje för viktiga händelser som är relaterade till entiteten, till exempel aviseringar, bok märken och aktiviteter. Aktiviteter är agg regeringar för viktiga händelser från Log Analytics. Frågorna som identifierar dessa aktiviteter utvecklas av Microsoft Security Research Team.

- Den högra panelen visar beteende insikter för entiteten. Dessa insikter hjälper till att snabbt identifiera avvikelser och säkerhetshot. Insikterna utvecklas av Microsoft Security Research Teams och baseras på avvikelse identifierings modeller.

### <a name="the-timeline"></a>Tids linjen

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Tids linje för enhets sidor":::

Tids linjen är en stor del av enhets sidans bidrag till beteende analys i Azure Sentinel. Den innehåller en berättelse om entiteter-relaterade händelser, som hjälper dig att förstå entitetens aktivitet inom en bestämd tidsram.

Du kan välja **tidsintervallet** bland flera förinställda alternativ (till exempel de *senaste 24 timmarna*) eller ange det som en anpassad tidsram. Dessutom kan du ange filter som begränsar informationen i tids linjen till vissa typer av händelser eller aviseringar.

Följande typer av objekt ingår i tids linjen:

- Aviseringar – alla varningar där entiteten definieras som en **mappad entitet**. Observera att om din organisation har skapat [anpassade aviseringar med analys regler](./tutorial-detect-threats-custom.md)bör du kontrol lera att enhets mappningen för reglerna är korrekt.

- Bok märken – alla bok märken som innehåller den angivna entiteten som visas på sidan.

- Aktiviteter – agg regering av viktiga händelser som är relaterade till entiteten. 
 
### <a name="entity-insights"></a>Entitets Insights
 
Entitet Insights är frågor som definieras av Microsofts säkerhets forskare för att hjälpa dina analyser att undersöka mer effektivt och effektivt. Insikterna presenteras som en del av sidan entitet och ger värdefull säkerhets information om värdar och användare i form av tabell data och diagram. Informationen här innebär att du inte behöver avLog Analytics. Insikterna innehåller data om inloggningar, grupp tillägg, avvikande händelser och mer, och innehåller avancerade ML-algoritmer för att identifiera avvikande beteende. 

Insikterna baseras på följande data Källor:
- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Pulsslag (Azure Monitor Agent)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>Använda enhets sidor

Entitetsformulär är utformade för att ingå i flera användnings scenarier och kan nås från incident hantering, undersöknings grafen, bok märken eller direkt från Sök sidan entitet under **enhets beteende analyser** på huvud menyn i Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Användnings fall för entitets sida":::

## <a name="data-schema"></a>Data schema

### <a name="behavior-analytics-table"></a>Beteende analys tabell

| Fält                     | Beskrivning                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | unikt ID-nummer för klient organisationen                                      |
| SourceRecordId            | unikt ID-nummer för EBA-händelsen                                   |
| TimeGenerated             | tidsstämpel för aktivitetens förekomst                              |
| TimeProcessed             | tidsstämpel för aktivitetens bearbetning av EBA-motorn            |
| ActivityType              | hög nivå kategori för aktiviteten                                 |
| ActionType                | normaliserat namn på aktiviteten                                     |
| Användarnamn                  | användar namn för den användare som initierade aktiviteten                    |
| UserPrincipalName         | fullständigt användar namn för den användare som initierade aktiviteten               |
| EventSource               | data källa som tillhandahöll den ursprungliga händelsen                        |
| SourceIPAddress           | IP-adress från vilken aktiviteten initierades                        |
| SourceIPLocation          | land som aktiviteten initierades från, som är omfattande från IP-adress |
| SourceDevice              | värdnamn för enheten som initierade aktiviteten                  |
| DestinationIPAddress      | IP-adress för aktivitetens mål                            |
| DestinationIPLocation     | land för aktivitetens mål, omfattande från IP-adress     |
| DestinationDevice         | namnet på mål enheten                                           |
| **UsersInsights**         | sammanhangsbaserade anrikning av berörda användare                            |
| **DevicesInsights**       | sammanhangsbaserade anrikning av berörda enheter                          |
| **ActivityInsights**      | kontext analys av aktivitet baserat på vår profilering              |
| **InvestigationPriority** | avvikelse poäng, mellan 0-10 (0 = oskadlighet, 10 = mycket avvikande)         |
|

Du kan se den fullständiga uppsättningen sammanhangsbaserade berikade referenser i **UsersInsights**, **DevicesInsights** och **ActivityInsights** i [referens dokumentet för UEBA-berikade referenser](ueba-enrichments.md).

### <a name="querying-behavior-analytics-data"></a>Fråga om beteende analys data

Med hjälp av [KQL](/azure/data-explorer/kusto/query/)kan vi fråga beteende analys tabellen.

Exempel: om vi vill hitta alla fall av en användare som inte kunde logga in på en Azure-resurs, där det var användarens första försök att ansluta från ett angivet land, och anslutningarna från det landet är ovanliga även för användarens kollegor, kan vi använda följande fråga:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Användarens peer-metadata – tabell och Notebook

Metadata för användar-peers har viktigt sammanhang i Hot identifieringar, vid undersökning av en incident och i jakt för ett potentiellt hot. Säkerhetsanalytiker kan observera normala aktiviteter i en användares kollegor för att avgöra om användarens aktiviteter är ovanliga jämfört med de som tillhör sina kollegor.

Azure Sentinel beräknar och rangordnar en användares peer-datorer baserat på användarens medlemskap i Azure AD-säkerhetsgruppen, e-postlistan, et-bokningen och lagrar peer-datorerna rankade 1-20 i tabellen **UserPeerAnalytics** . Skärm bilden nedan visar schemat för UserPeerAnalytics-tabellen och visar de åtta mest rankade peer-datorerna i användaren Kendall Collins. I Azure Sentinel används *termen frekvens-algoritm för omvänd dokument frekvens* (tf-IDF) för att normalisera vägningen vid beräkning av rangordningen: ju mindre gruppen är, desto högre vikt. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Skärm bild som visar användar-peers metadata tabell":::

Du kan använda den [bärbara datorn Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) som finns i Azure Sentinel GitHub-lagringsplatsen för att visualisera användarnas peer-metadata. Detaljerade anvisningar om hur du använder antecknings boken finns i den [interaktiva analys-och säkerhets listan över användarens metadata](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="permission-analytics---table-and-notebook"></a>Behörighets analys-tabell och Notebook

Med behörighets analys kan du ta reda på den potentiella effekten av en organisations till gångs intrång av en angripare. Den här effekten kallas även för till gångens "Mas radie". Säkerhets analytiker kan använda den här informationen för att prioritera undersökningar och incident hantering.

Azure Sentinel avgör de direkta och transitiva åtkomst rättigheter som innehas av en specifik användare till Azure-resurser genom att utvärdera de Azure-prenumerationer som användaren kan komma åt direkt eller via grupper eller tjänstens huvud namn. Den här informationen, samt den fullständiga listan över användarens medlemskap i Azure AD-säkerhetsgruppen, lagras sedan i **UserAccessAnalytics** -tabellen. Skärm bilden nedan visar en exempel rad i UserAccessAnalytics-tabellen för användaren Alex Johnson. **Källentiteten** är användarens eller tjänstens huvud konto och **målentiteten** är den resurs som käll enheten har åtkomst till. Värdena för **åtkomst nivå** och **åtkomst typ** beror på åtkomst kontrollens modell för målentiteten. Du kan se att Alex har deltagar åtkomst till Azure-prenumerationen *contoso hotell-klient*. Åtkomst kontroll modellen för prenumerationen är Azure RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Skärm bild som visar användar åtkomst Analytics-tabellen":::

Du kan använda den [bärbara datorn Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (samma bärbara dator som nämns ovan) från Azure Sentinel GitHub-lagringsplatsen för att visualisera behörighets analys data. Detaljerade anvisningar om hur du använder antecknings boken finns i den [interaktiva analys-och säkerhets listan över användarens metadata](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="hunting-queries-and-exploration-queries"></a>Jakt frågor och utforsknings frågor

Azure Sentinel tillhandahåller färdiga frågor, utforsknings frågor och en arbets bok som baseras på BehaviorAnalytics-tabellen. Dessa verktyg visar omfattande data, fokuserat på vissa användnings fall som indikerar avvikande beteende. 

Lär dig mer om [jakt-och undersöknings diagrammet](./hunting.md) i Azure Sentinel.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig om Azure Sentinels funktioner för enhets beteende analys. Praktisk vägledning om implementering och hur du använder de insikter som du har fått finns i följande artiklar:

- [Aktivera enhets beteende analys](./enable-entity-behavior-analytics.md) i Azure Sentinel.
- [Jakt efter säkerhetshot](./hunting.md).
