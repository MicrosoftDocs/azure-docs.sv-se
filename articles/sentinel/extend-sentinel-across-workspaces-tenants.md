---
title: Utöka Azure Sentinel över arbets ytor och klienter | Microsoft Docs
description: Använda Azure Sentinel för att fråga och analysera data mellan arbets ytor och klienter.
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
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 49b267d36fb6c365cf2125912c0d27fe7d669474
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585287"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Utöka Azure Sentinel för arbetsytor och klientorganisationer

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>Du behöver använda flera Azure Sentinel-arbetsytor

Azure Sentinel skapas ovanpå en Log Analytics-arbetsyta. Du ser att det första steget i onboarding Azure Sentinel är att välja den Log Analytics arbets yta som du vill använda för detta ändamål.

Du kan få den fullständiga fördelen av Azure Sentinel-upplevelsen när du använder en enskild arbetsyta. Även om det finns några omständigheter som kan kräva att du har flera arbets ytor. I följande tabell visas några av dessa situationer och, när det är möjligt, förslag på hur kravet kan uppfyllas med en enda arbets yta:

| Krav | Beskrivning | Sätt att minska antalet arbetsytor |
|-------------|-------------|--------------------------------|
| Efterlevnad och efterlevnad | En arbetsyta är kopplad till en specifik region. Om data måste behållas i olika [Azure-geografiska](https://azure.microsoft.com/global-infrastructure/geographies/) områden för att uppfylla myndighets krav, måste de delas upp i separata arbets ytor. |  |
| Dataägarskap | Gränserna för data ägarskap, till exempel av dotter bolag eller dotter bolag, är bättre avgränsade med separata arbets ytor. |  |
| Flera Azure-klienter | Azure Sentinel stöder data insamling från Microsoft-och Azure SaaS-resurser inom en egen Azure Active Directory (Azure AD)-klient gränsen. Därför kräver varje Azure AD-klientorganisation en separat arbetsyta. |  |
| Detaljerad åtkomstkontroll för data | En organisation kan behöva tillåta olika grupper i eller utanför organisationen för att få åtkomst till vissa av de data som samlas in av Azure Sentinel. Exempel:<br><ul><li>Resurs ägarens åtkomst till data som rör sina resurser</li><li>Regional eller dotter SOCs ' till gång till data som är relevanta för deras delar av organisationen</li></ul> | Använd [resurs Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) eller [tabell nivå Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Detaljerade inställningar för kvarhållning | Tidigare var flera arbets ytor det enda sättet att ange olika bevarande perioder för olika data typer. Detta behövs inte längre i många fall, tack vare införandet av inställningar för kvarhållning av tabell nivå. | Använd [Inställningar för kvarhållning av tabell nivå](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) eller automatisera [borttagning av data](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| Dela fakturering | Genom att placera arbets ytor i separata prenumerationer kan de faktureras till olika parter. | Användningsrapportering och korsdebitering |
| Äldre arkitektur | Användningen av flera arbets ytor kan leda till en historisk design som har tagit hänsyn till begränsningar eller bästa praxis som inte är längre än sant. Det kan även vara ett godtyckligt designval som kan ändras för att bättre tillgodose Azure Sentinel.<br><br>Exempel:<br><ul><li>Använda en standard arbets yta per prenumeration när du distribuerar Azure Security Center</li><li>Behovet av detaljerad åtkomst kontroll eller inställningar för kvarhållning, vilka lösningar som är relativt nya</li></ul> | Omarbeta arkitekturen för arbetsytor |

### <a name="managed-security-service-provider-mssp"></a>Hanterad säkerhets tjänst leverantör (MSSP)

Ett särskilt användnings fall som bestämmer flera arbets ytor är en MSSP Azure Sentinel-tjänst. I det här fallet är många om inte alla ovanstående krav gäller, vilket gör flera arbets ytor, mellan klienter, bästa praxis. MSSP kan använda [Azure Lighthouse](../lighthouse/overview.md) för att utöka Azure Sentinel-funktioner över flera arbets ytor mellan klienter.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Arkitektur för flera arbetsytor i Azure Sentinel

Som följer de krav som anges ovan, finns det fall där flera Azure Sentinel-arbetsytor, eventuellt över Azure Active Directory (Azure AD)-klienter, måste övervakas och hanteras centralt av en enda SOC.

- En MSSP Azure Sentinel-tjänst.

- En global SOC som betjänar flera dotter bolag, var och en med sin egen lokala SOC.

- En SOC övervakning av flera Azure AD-klienter i en organisation.

För att lösa detta krav erbjuder Azure Sentinel funktioner för flera arbets ytor som möjliggör central övervakning, konfiguration och hantering, vilket ger en enda ruta med glas över allt som täcks av SOC, enligt diagrammet nedan.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Arkitektur för kors arbets yta":::

Den här modellen ger avsevärda fördelar jämfört med en helt centraliserad modell där alla data kopieras till en enda arbets yta:

- Flexibel roll tilldelning till globala och lokala SOCs, eller till MSSP sina kunder.

- Färre utmaningar gällande data ägarskap, data sekretess och regelefterlevnad.

- Minimal svarstid i nätverk och minimala avgifter.

- Enkel onboarding och offboarding av nya dotter bolag eller kunder.

I följande avsnitt förklarar vi hur du använder den här modellen och särskilt hur du:

- Övervaka flera arbets ytor centralt, potentiellt mellan klienter, och tillhandahålla SOC med ett enda fönster i glaset.

- Konfigurera och hantera flera arbets ytor centralt, potentiellt mellan klienter, med Automation.

## <a name="cross-workspace-monitoring"></a>Övervakning mellan arbets ytor

### <a name="manage-incidents-on-multiple-workspaces"></a>Hantera incidenter på flera arbets ytor

Azure Sentinel stöder en [incident vy för flera arbets ytor](./multiple-workspace-view.md) som underlättar central incident övervakning och hantering av flera arbets ytor. I vyn centraliserad incident kan du hantera incidenter direkt eller öka detalj nivån transparent till incident informationen i kontexten för den ursprungliga arbets ytan.

### <a name="cross-workspace-querying"></a>Fråga om kors arbets ytor

Azure Sentinel stöder frågor till [flera arbets ytor i en enda fråga](../azure-monitor/logs/cross-workspace-query.md), så att du kan söka efter och korrelera data från flera arbets ytor i en enda fråga. 

- Använd [uttrycket arbets yta ()](../azure-monitor/logs/workspace-expression.md) för att referera till en tabell i en annan arbets yta. 
- Använd [union-operatorn](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) tillsammans med arbets ytan ()-uttrycket för att tillämpa en fråga över tabeller i flera arbets ytor.

Du kan använda sparade [funktioner](../azure-monitor/logs/functions.md) för att förenkla frågor över arbets ytor. Om en referens till en arbets yta till exempel är lång, kanske du vill spara uttrycket `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` som en funktion som kallas `SecurityEventCustomerA` . Du kan sedan skriva frågor som `SecurityEventCustomerA | where ...` .

En funktion kan också förenkla en union som används ofta. Du kan till exempel Spara följande uttryck som en funktion som kallas `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Du kan sedan skriva en fråga i båda arbets ytorna genom att börja med `unionSecurityEvent | where ...` .

#### <a name="cross-workspace-analytics-rules"></a>Analys regler för mellan arbets ytor<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Frågor över arbets ytor kan nu inkluderas i regler för schemalagd analys, med följande begränsningar:

- Upp till 20 arbets ytor kan inkluderas i en enda fråga.
- Azure Sentinel måste distribueras på varje arbets yta som refereras till i frågan.

> [!NOTE] 
> Att fråga flera arbets ytor i samma fråga kan påverka prestanda och rekommenderas därför endast när logiken kräver den här funktionen.

#### <a name="cross-workspace-workbooks"></a>Arbets böcker över arbets ytor<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
[Arbets böcker](./overview.md#workbooks) tillhandahåller instrument paneler och appar till Azure Sentinel. När du arbetar med flera arbets ytor tillhandahåller de övervakning och åtgärder mellan arbets ytor.

Arbets böcker kan tillhandahålla frågor över flera arbets ytor på en av tre metoder, som var och en omfattar olika nivåer av slut användar experter:

| Metod  | Beskrivning | När ska jag använda? |
|---------|-------------|--------------------|
| Skriv frågor mellan arbets ytor | Arbets bokens skapare kan skriva frågor över arbets ytor (beskrivs ovan) i arbets boken. | Med det här alternativet kan skapande av arbets boken skydda användaren helt från arbets ytans struktur. |
| Lägga till en väljare för arbets ytor i arbets boken | Arbets bokens skapare kan implementera en Workspace-selektor som en del av arbets boken, enligt beskrivningen [här](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357). | Det här alternativet ger användaren kontroll över arbets ytorna som visas i arbets boken, med hjälp av en användarvänlig listruta. |
| Redigera arbets boken interaktivt | En avancerad användare som ändrar en befintlig arbets bok kan redigera frågorna i den och välja mål arbets ytorna med hjälp av arbets ytans väljare i redigeraren. | Med det här alternativet kan en privilegie rad användare enkelt ändra befintliga arbets böcker och arbeta med flera arbets ytor. |
|

#### <a name="cross-workspace-hunting"></a>Jakt mellan arbets ytor

Azure Sentinel tillhandahåller förinstallerade fråge exempel utformade för att komma igång och du får bekanta dig med tabellerna och frågespråket. Dessa inbyggda jakt frågor utvecklas av Microsofts säkerhets forskare på en kontinuerlig basis, både nya frågor och fin justering av befintliga frågor, för att ge dig en start punkt för att söka efter nya identifieringar och identifiera tecken på intrång som kan ha försvunnit av dina säkerhets verktyg.  

Tilläggs funktioner mellan olika arbets ytor gör det möjligt för ditt hot Hunters att skapa nya jakt frågor, eller anpassa befintliga, för att visa flera arbets ytor, med hjälp av union-operatorn och arbets ytan ()-uttrycket som visas ovan.

## <a name="cross-workspace-management-using-automation"></a>Hantering över arbets ytor med Automation

Om du vill konfigurera och hantera flera Azure Sentinel-arbetsytor måste du automatisera användningen av Azure Sentinel Management-API: et. Mer information om hur du automatiserar distributionen av Azure Sentinel-resurser, inklusive varnings regler, jakt frågor, arbets böcker och spel böcker, finns i [utöka Azure Sentinel: API: er, integrering och hantering av automatisering](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Se även [distribuera och hantera Azure Sentinel som kod](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) och [kombinera Azure-Lighthouse med Azure Sentinel ' s DevOps-funktioner](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) för en sammanställd metod för att hantera Azure Sentinel som kod och för att distribuera och konfigurera resurser från en privat GitHub-lagringsplats. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Hantera arbets ytor över klient organisationer med Azure Lighthouse

Som nämnts ovan kan de olika Azure Sentinel-arbetsytorna i många scenarier finnas i olika Azure AD-klienter. Du kan använda [Azure-Lighthouse](../lighthouse/overview.md) för att utöka alla aktiviteter för flera arbets ytor över klient gränser, så att användare i hanteringen av klient organisationen kan arbeta på Azure Sentinel-arbetsytor över alla klienter. När [Azure-Lighthouse](../lighthouse/how-to/onboard-customer.md)har registrerats använder du [katalog-och prenumerations väljaren](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) på Azure Portal för att välja alla prenumerationer som innehåller arbets ytor som du vill hantera, för att se till att de kommer att vara tillgängliga i olika väljare för arbets ytor i portalen.

När du använder Azure Lighthouse rekommenderar vi att du skapar en grupp för varje Azure Sentinel-roll och delegerar behörigheter från varje klient till dessa grupper.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du kan utöka Azure Sentinel-funktionerna i flera arbets ytor och klient organisationer. Praktisk vägledning om hur du implementerar arkitekturen mellan arbets ytor i Azure Sentinel finns i följande artiklar:

- Lär dig hur du [arbetar med flera klienter](./multiple-tenants-service-providers.md) i Azure Sentinel med Azure Lighthouse.
- Lär dig att [Visa och hantera incidenter i flera arbets ytor](./multiple-workspace-view.md) sömlöst.
