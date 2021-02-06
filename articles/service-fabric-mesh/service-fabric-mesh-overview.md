---
title: Översikt över Azure Service Fabric-nät
description: Lär dig mer om Azure Service Fabric Mesh. Med Service Fabric Mesh kan du distribuera och skala ditt program utan att behöva bekymra dig om programmets infrastrukturbehov.
author: georgewallace
ms.author: gwallace
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: 64880a9ac3d6d48ce6d39d0b3a7a3aff6587f328
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626985"
---
# <a name="what-is-service-fabric-mesh"></a>Vad är Service Fabric Mesh?

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här videon ger en snabb översikt över Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Program som finns i Service Fabric Mesh körs och skalas utan att du att behöver bekymra dig om den infrastruktur som driver det.  Service Fabric Mesh består av kluster med tusentals datorer.  Alla klusteråtgärder är dolda från utvecklaren. Ladda upp din kod och ange vilka resurser du behöver, tillgänglighets krav och resurs gränser.  Service Fabric Mesh allokerar automatiskt infrastrukturen och hanterar infrastrukturfel, så att dina program har hög tillgänglighet. Du behöver bara bry dig om programmets hälsotillstånd och svarstid, inte infrastrukturen.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Den här artikeln innehåller en översikt över de främsta fördelarna med Service Fabric Mesh.

## <a name="great-developer-experience"></a>Utmärkta funktioner för utvecklare

Service Fabric Mesh stöder alla programmeringsspråk och ramverk som kan köras i en container. Stöd för Visual Studio 2019 och Visual Studio Code är en kraftfull redigerings-och fel söknings upplevelse för .NET-och .NET Core-program. 

Med Service Fabric Mesh kan du:

- ”Lift and shift” befintliga program till containrar för att modernisera och köra dina aktuella program i stor skala.
- Skapa och distribuera nya mikrotjänstprogram skalenligt i Azure.  Integrera med andra Azure-tjänster eller befintliga program som körs i containrar. Varje mikrotjänst ingår i ett säkert, isolerat nätverks program. Mikrotjänsten har resurs styrnings principer som definierats för processor kärnor, minne, disk utrymme och mycket annat.
- Integrera med och utöka befintliga program utan att ändra dessa program. Använda ditt egna virtuella nätverk för att ansluta befintliga program till det nya programmet.  
- Modernisera dina befintliga Cloud Services-program genom att migrera till Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Enkel driftslivscykel

Hantera enkelt program som körs, övervaka program och fel sökning i produktions miljöer. Den här hanteringen omfattar program uppgraderingar och versions hantering. Dessa program kan bestå av en enda mikrotjänst eller flera mikrotjänster som är isolerade inom sina egna nätverk. Program körs effektivt med snabb distribution, placering och redundanstid.

Med Service Fabric Mesh kan du:

- Distribuera och hantera program utan att explicit etablera och hantera infrastruktur.  Service Fabric Mesh etablerar, uppgraderar, korrigeringar och underhåller den underliggande infrastrukturen åt dig.
- Konfigurera kontinuerlig integrering med integrerade verktyg för att enkelt paketera och distribuera program.
- Utnyttja alla funktioner i Azure Resource Manager-resurser. Exempel på dessa funktioner är Gransknings logg och [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md)). Alla resurser som du distribuerar till Service Fabric nät tjänsten i Azure är Azure Resource Manager resurser. Dessa resurser omfattar program, tjänster, hemligheter och så vidare.
- Distribuera och hantera resurser med hjälp av [Azure-portalen](https://portal.azure.com), Resource Manager-mallar eller Azure CLI/PowerShell-bibliotek.
- Konfigurera driftsövervakning och -avisering med hjälp av [Application Insights](/azure/application-insights/) (eller valfritt verktyg) för att registrera spårningar för drifts- och diagnostikdata från plattformen.
- Få diagnostikinformation för program som skickas ut från programmodellen med hjälp av [Application Insights](/azure/application-insights/) eller valfritt verktyg.
- Optimera resursanvändningen genom att ange regler för automatisk skalning för tjänster i programdefinitionen.

## <a name="mission-critical-platform-capabilities"></a>Uppdragskritiska plattformsfunktioner

Service Fabric Mesh skapar en samling av kluster som sträcker sig över [Azure-tillgänglighetszoner](../availability-zones/az-overview.md) och/eller geopolitiska regionala gränser. Service Fabric nät beskriver program med en uppsättning syften som skalning, maskin varu krav, hållbarhets krav och säkerhets principer.  När programmet distribueras hittar Service Fabric Mesh den optimala platsen för att köra det.

Med Service Fabric Mesh kan du:

- Dra nytta av hög tillgänglighet, skalning in och ut, orkestrering, meddelandehantering, tillförlitliga meddelanden, uppgraderingar utan nedtid, hantering av säkerhet/hemligheter, katastrofåterställning, tillståndshantering, konfigurationshantering och distribuerade transaktioner.
- Välj mellan flera programmodeller när du skapar program.
- Använd plattformsfunktioner som exponeras via REST-slutpunkter genom att förbruka språkspecifika bindningar som genereras med Swagger.
- Distribuera program över [tillgänglighetszoner](../availability-zones/az-overview.md) och flera regioner för geotillförlitlighet.
- Använd alla säkerhets- och efterlevnadsfunktioner som Azure tillhandahåller.

## <a name="next-steps"></a>Nästa steg

Det behövs bara några få steg för att distribuera ett exempelprojekt med Visual Studio. Mer information finns i på sidan om att [skapa en ASP.NET Core-webbplats](service-fabric-mesh-quickstart-dotnet-core.md). 

Få svar på [vanliga frågor](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
