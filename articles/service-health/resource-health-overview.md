---
title: Översikt över Azure Resource Health
description: Lär dig hur Azure Resource Health hjälper dig att diagnostisera och få support för tjänst problem som påverkar dina Azure-resurser.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 25425eadfaf54ba56cde3b377a44cd85c2b68959
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90967871"
---
# <a name="resource-health-overview"></a>Översikt över Resource Health
 
Azure Resource Health hjälper dig att diagnostisera och få support för tjänst problem som påverkar dina Azure-resurser. Den rapporterar om resursernas aktuella och tidigare hälso tillstånd.

[Azures status](https://status.azure.com) rapporter om tjänst problem som påverkar en rad olika Azure-kunder. Resource Health ger dig en anpassad instrument panel med hälso tillståndet för dina resurser. Resource Health visar alla tider som resurserna har varit otillgängliga på grund av problem med Azure-tjänsten. Dessa data gör det enkelt för dig att se om ett SLA bröts.

## <a name="resource-definition-and-health-assessment"></a>Resurs definition och hälso bedömning

En *resurs* är en angiven instans av en Azure-tjänst, till exempel en virtuell dator, en webbapp eller en SQL Database. Resource Health förlitar sig på signaler från olika Azure-tjänster för att bedöma om en resurs är felfri. Om en resurs inte är felfri analyserar Resource Health ytterligare information för att fastställa orsaken till problemet. Den rapporterar även om åtgärder som Microsoft vidtar för att åtgärda problemet och identifierar saker som du kan göra för att åtgärda det.

Mer information om hur hälsa utvärderas finns i listan över resurs typer och hälso kontroller på [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Hälsostatus

Hälso tillståndet för en resurs visas som en av följande status värden.

### <a name="available"></a>Tillgänglig

*Tillgängligt* innebär att det inte finns några händelser som påverkar resursens hälso tillstånd. I de fall då resursen återställdes från oplanerade drift stopp under de senaste 24 timmarna visas meddelandet "nyligen löst".

![Status för * tillgängligt * för en virtuell dator som har meddelandet "nyligen löst"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Inte tillgänglig

*Otillgänglig* innebär att tjänsten har identifierat en pågående plattforms-eller icke-plattforms händelse som påverkar resursens hälso tillstånd.

#### <a name="platform-events"></a>Plattformshändelser

Plattforms händelser utlöses av flera komponenter i Azure-infrastrukturen. De omfattar både schemalagda åtgärder (till exempel planerat underhåll) och oväntade incidenter (t. ex. en oplanerad värd omstart eller degraderad värd maskin vara som är förväntad för att bli misslyckad efter ett angivet tids fönster).

Resource Health ger ytterligare information om händelsen och återställnings processen. Du kan också kontakta Microsoft Support även om du inte har ett aktivt support avtal.

![Status för * ej tillgängligt * för en virtuell dator på grund av en plattforms händelse](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Händelser som inte är plattformar

Händelser som inte är plattformar utlöses av användar åtgärder. Exempel är att stoppa en virtuell dator eller nå maximalt antal anslutningar till Azure cache för Redis.

![Status för "ej tillgänglig" för en virtuell dator på grund av en icke-plattforms händelse](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Okänt

*Okänd* innebär att Resource Health inte har tagit emot information om resursen i mer än 10 minuter. Även om denna status inte är en slutgiltig indikation på resursens tillstånd, är det en viktig data punkt för fel sökning.

Om resursen körs som förväntat ändras statusen för resursen till *tillgänglig* efter några minuter.

Om du får problem med resursen kan den *okända* hälso statusen innebära att en händelse i plattformen påverkar resursen.

![Status för * okänd * för en virtuell dator](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degraderad

*Degraderad* innebär att din resurs har upptäckt prestanda förlust, även om den fortfarande är tillgänglig för användning.

Olika resurser har sina egna kriterier när de rapporterar att de försämras.

![Status * degraderat * för en virtuell dator](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Rapportera felaktig status

Om du tror att den aktuella hälso statusen är felaktig kan du berätta oss genom att välja **Rapportera felaktig hälso status**. I de fall där ett Azure-problem påverkar dig rekommenderar vi att du kontaktar supporten från Resource Health.

![Formulär för att skicka in information om felaktig status](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Historik information

Du kan komma åt upp till 30 dagars historik i avsnittet **hälso historik** i Resource Health.

![Lista över Resource Health händelser under de senaste två veckorna](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Kom igång

Öppna Resource Health för en resurs:

1. Logga in på Azure-portalen.
2. Bläddra till resursen.
3. Välj **resurs hälsa** på resurs menyn i det vänstra fönstret.

![Öppna Resource Health från resursvyn](./media/resource-health-overview/from-resource-blade.png)

Du kan också komma åt Resource Health genom att välja **alla tjänster** och skriva **resurs hälsa** i text rutan filter. Välj [resurs hälsa](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)i rutan **Hjälp + Support** .

![Öppnar Resource Health från "alla tjänster"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Nästa steg

Ta en titt på dessa referenser om du vill veta mer om Resource Health:
-  [Resurs typer och hälso kontroller i Azure Resource Health](resource-health-checks-resource-types.md)
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)
