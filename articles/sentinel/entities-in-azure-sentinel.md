---
title: Använd entiteter för att klassificera och analysera data i Azure Sentinel | Microsoft Docs
description: Tilldela enhets klassificeringar (användare, värdnamn, IP-adresser) till data objekt i Azure Sentinel och Använd dem för att jämföra, analysera och korrelera data från flera källor.
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
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456339"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Klassificera och analysera data med entiteter i Azure Sentinel

## <a name="what-are-entities"></a>Vad är entiteter?

När aviseringar skickas till eller genereras av Azure Sentinel, innehåller de data objekt som Sentinel kan identifiera och klassificera i kategorier som **entiteter**. När Azure Sentinel förstår vilken typ av entitet som ett visst data objekt representerar, vet det rätt frågor att fråga om det, och det kan sedan jämföra insikter om detta objekt i hela serien av data källor och enkelt spåra det och referera till det i hela kontroll miljön – analys, undersökning, reparation, jakt och så vidare. Några vanliga exempel på entiteter är användare, värdar, filer, processer, IP-adresser och URL: er.

### <a name="entity-identifiers"></a>Enhets identifierare

Azure Sentinel stöder en mängd olika entitetstyper. Varje typ har sina egna unika attribut, inklusive vissa som kan användas för att identifiera en viss entitet. Dessa attribut representeras som fält i entiteten och kallas **identifierare**. Se den fullständiga listan över entiteter som stöds och deras identifierare nedan.

#### <a name="strong-and-weak-identifiers"></a>Starka och svaga identifierare

Som anges ovan för varje typ av entitet finns det fält eller fält uppsättningar som kan identifiera den. Dessa fält eller fält uppsättningar kan kallas **starka identifierare** om de unikt kan identifiera en entitet utan tvetydighet eller som **svaga identifierare** om de kan identifiera en entitet under vissa omständigheter, men inte garanterar att unikt identifiera en entitet i samtliga fall. I många fall kan även ett urval av svaga identifierare kombineras för att skapa en stark identifierare.

Användar konton kan till exempel identifieras som **konto** enheter på fler än ett sätt: med hjälp av en enda **stark identifierare** som ett Azure AD-kontos numeriska identifierare ( **GUID** -fältet) eller dess **UPN-värde (User Principal Name)** eller alternativt kan du använda en kombination av **svaga identifierare** som dess **namn** och **NTDomain** fält. Olika data källor kan identifiera samma användare på olika sätt. När Azure Sentinel påträffar två entiteter som kan identifieras som samma entitet baserat på deras identifierare, sammanfogas de två entiteterna till en enda entitet, så att den kan hanteras korrekt och konsekvent.

Om en av dina resurs leverantörer däremot skapar en avisering i vilken en entitet inte är tillräckligt identifierad, t. ex. genom att bara använda en enda **svag identifierare** som ett användar namn utan domän namns kontexten, kan inte entiteten användare sammanfogas med andra instanser av samma användar konto. De andra instanserna identifieras som separata entiteter och dessa två entiteter förblir separata i stället för Unified.

För att minimera risken för detta händer bör du kontrol lera att alla dina aviserings leverantörer identifierar enheterna korrekt i de aviseringar som de skapar. Dessutom kan synkronisering av entiteter för användar konton med Azure Active Directory skapa en enhetlig katalog som kan slå samman entiteter för användar konton.

#### <a name="supported-entities"></a>Entiteter som stöds

Följande typer av entiteter identifieras för närvarande i Azure Sentinel:

- Användar konto
- Värd
- IP-adress
- Skadlig kod
- Fil
- Process
- Moln program
- Domännamn
- Azure-resurs
- Filhash-värde
- Registernyckel
- Registervärde
- Säkerhets grupp
- URL
- IoT-enhet
- Mailbox
- E-postkluster
- E-postmeddelande
- Skicka e-post

Du kan visa dessa entiteters identifierare och annan relevant information i [entitets referensen](entities-reference.md).

## <a name="entity-mapping"></a>Entitetsmappning

Hur identifierar Azure Sentinel en del av data i en avisering som identifierar en entitet?

Nu ska vi titta på hur data bearbetningen görs i Azure Sentinel. Data matas in från olika källor via [anslutningar](connect-data-sources.md), oavsett om tjänst-till-tjänst, agent-baserad eller användning av en syslog-tjänst och en logg vidarebefordrare. Data lagras i tabeller i din Log Analytics-arbetsyta. Tabellerna frågas sedan regelbundet enligt de analys regler som du har definierat och aktiverat. En av de många åtgärder som vidtas av dessa analys regler är mappningen av data fält i tabellerna till Azure Sentinel-identifierade entiteter. Enligt mappningar som du definierar i analys reglerna tar Azure Sentinel med fält från de resultat som returneras av din fråga, identifierar dem genom de identifierare som du har angett för varje entitetstyp och gäller den entitetstyp som identifieras av dessa identifierare.

Vad är den här punkten?

När Azure Sentinel kan identifiera entiteter i aviseringar från olika typer av data källor, och särskilt om det kan göra det med hjälp av starka identifierare som är gemensamma för varje data källa eller till ett tredje schema, kan det sedan enkelt korrelera mellan alla dessa aviseringar och data källor. Dessa korrelationer hjälper till att skapa en omfattande butik av information och insikter om entiteterna, vilket ger dig en solid grund för dina säkerhets åtgärder.

Lär dig hur du [mappar data fält till entiteter](map-data-fields-to-entities.md).

Lär dig [vilka identifierare som starkt identifierar en entitet](entities-reference.md).

## <a name="entity-pages"></a>Enhets sidor

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

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du arbetar med entiteter i Azure Sentinel. Praktisk vägledning om implementering och hur du använder de insikter som du har fått finns i följande artiklar:

- [Aktivera enhets beteende analys](./enable-entity-behavior-analytics.md) i Azure Sentinel.
- [Jakt efter säkerhetshot](./hunting.md).
