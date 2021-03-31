---
title: Azure Resource Health vanliga frågor och svar
description: Översikt över Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: dc24688d8393b7a13e8c93d3f4d0171cce4c213c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570058"
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health vanliga frågor och svar
Lär dig svaren på vanliga frågor om Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Vad är Azure Resource Health?
Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.  

## <a name="what-is-the-resource-health-intended-for"></a>Vad är Resource Health avsett för?
När ett problem med en resurs har identifierats kan Resource Health hjälpa dig att diagnostisera rotor saken. Det ger hjälp för att minimera problemet och teknisk support när du behöver mer hjälp med problem med Azure-tjänsten.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Vilka hälso kontroller utförs av Resource Health?
Resurs hälsan utför olika kontroller baserat på [resurs typen](resource-health-checks-resource-types.md). Dessa kontroller är utformade för att implementera tre typer av problem: 
- Oplanerade händelser, till exempel en oväntad omstart av värden
- Planerade händelser, t. ex. schemalagda värd operativ system uppdateringar
- Händelser som utlöses av användar åtgärder, till exempel en användare som startar om en virtuell dator

## <a name="what-does-each-of-the-health-status-mean"></a>Vad betyder varje hälso status?
Det finns tre olika hälso status:
- Tillgängligt: det finns inga kända problem i Azure-plattformen som kan påverka den här resursen
- Ej tillgänglig: resurs hälsan har upptäckt problem som påverkar resursen
- Okänd: resurs hälsan kan inte fastställa hälso tillståndet för en resurs eftersom den inte längre tar emot information om den. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Vad betyder okänd status? Är något fel med min resurs?
Hälso status är inställd på okänd när Resource Health slutar att ta emot information om en angiven resurs. Även om denna status inte är en slutgiltig angivelse av resursens tillstånd, kan det bero på att det finns ett Azure-problem i de fall där problem uppstår.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hur kan jag få hjälp med en resurs som inte är tillgänglig?
Du kan skicka en support förfrågan från bladet Resource Health. Du behöver inget support avtal med Microsoft för att öppna en begäran när resursen inte är tillgänglig på grund av plattforms händelser.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Skiljer Resource Health mellan den tillgänglighet som orsakas av plattforms problem jämfört med något jag gjorde?
Ja, om en resurs inte är tillgänglig, kan Resource Health identifiera rotor saken inom en av följande kategorier: 
-   Åtgärd som initieras av användaren
-   Planerad händelse 
-   Oplanerad händelse

På portalen visas användaren initierade åtgärder med hjälp av en blå meddelande ikon, medan planerade och oplanerade händelser visas med en röd varnings ikon. Mer information finns i [Översikt över Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Kan jag integrera Resource Health med mina övervaknings verktyg?
Resource Health [stöder](resource-health-alert-arm-template-guide.md) aviseringar som baseras på aktivitets loggen. Aktivitets logg aviseringar använder [Åtgärds grupper](../azure-monitor/alerts/action-groups.md) för att meddela användare om att en avisering har utlösts. Åtgärds grupper har stöd för en rad aviserings kanaler, till exempel e-post, SMS, webhook och ITSM-åtgärder.

## <a name="where-do-i-find-resource-health"></a>Var hittar jag Resource Health?
När du har loggat in på Azure Portal kan du komma åt Resource Health på flera sätt:
- Navigera till din resurs. I det vänstra navigerings fältet väljer du **resurs hälsa**
- Gå till bladet Azure Service Health.  Välj **resurs hälsa** i det vänstra navigerings fältet.

Du kan också använda Resource Health-API: et för att få information om hälsan för dina resurser.

## <a name="is-resource-health-available-for-all-resource-types"></a>Är Resource Health tillgängligt för alla resurs typer?
Listan över hälso kontroller och resurs typer som stöds via Resource Health finns [här](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Vad ska jag göra om min resurs visas tillgänglig men jag tror att den inte är det? "
När du kontrollerar hälsan för en resurs, direkt under hälso status kan du klicka på **Rapportera felaktig hälso status**. Innan du skickar in rapporten har du möjlighet att ange ytterligare information om varför du tror att den aktuella hälso statusen är felaktig.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Är Resource Health tillgängligt för alla Azure-regioner? 
Resource Health är tillgängligt i alla Azure-geografiska områden.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Hur skiljer sig Resource Health från Azure-status eller Service Health instrument panelen?
Den information som tillhandahålls av Resource Health är mer detaljerad än vad som tillhandahålls av Azure-status eller Service Health instrument panel.

Medan [Azures status](https://status.azure.com) och service Health instrument panelen meddelar dig om tjänst problem som påverkar en rad olika kunder (till exempel en Azure-region), Resource Health visar mer detaljerade händelser som bara är relevanta för den specifika resursen. Om en värd t. ex. startar om, Resource Health bara de kunder vars virtuella datorer körs på värden.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Måste jag aktivera Resource Health för varje resurs?
Nej, hälso information är tillgänglig för alla resurs typer som är tillgängliga via Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Behöver vi aktivera Resource Health för min organisation?
Nej.  Azure Resource Health är tillgängligt i Azure Portal utan några installations krav.

## <a name="is-resource-health-available-free-of-charge"></a>Är Resource Health tillgängligt utan kostnad?
Ja.  Azure Resource Health är kostnads fri.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Vilka är rekommendationerna Resource Health tillhandahåller?
Baserat på hälso status Resource Health ger dig rekommendationer med målet att minska den tid som du har använt fel sökningen. För tillgängliga resurser fokuserar rekommendationerna på hur du kan lösa de vanligaste problem som kunder stöter på. Om resursen inte är tillgänglig på grund av ett oplanerat Azure-evenemang är fokus att hjälpa dig under och efter återställnings processen. 

## <a name="next-steps"></a>Nästa steg

Läs mer om Resource Health:
-  [Översikt över Azure Resource Health](Resource-health-overview.md)
-  [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)
