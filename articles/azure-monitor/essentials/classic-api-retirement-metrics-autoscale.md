---
title: 'Dra tillbaka distributions-API: er för Azure Monitor mått och autoskalning'
description: 'Mått och autoskalning av klassiska API: er, även kallade Azure Service Management (ASM) eller RDFE distributions modell som dras tillbaka'
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 0169de93b92de179c0ae9a36ff8dba3b7b1dc0fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049040"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor pensionering av klassiska API: er för distributions modeller för mått och autoskalning

Azure Monitor (tidigare Azure Insights när den första versionen släpptes) erbjuder för närvarande möjlighet att skapa och hantera inställningar för autoskalning för och använda mått från klassiska virtuella datorer och klassiska Cloud Services. Den ursprungliga uppsättningen av klassiska distributions modellbaserade API: er kommer att **dras tillbaka efter den 30 juni 2019** i alla offentliga Azure-och privata moln i alla regioner.   

Samma åtgärder har stödts via en uppsättning Azure Resource Manager baserade API: er under ett år. I Azure Portal används nya REST-API: er för både autoskalning och mått. En ny SDK, PowerShell och CLI baserat på dessa Resource Manager-API: er är också tillgängliga. Våra partner tjänster för övervakning använder de nya Resource Manager-baserade REST-API: erna i Azure Monitor.  

## <a name="who-is-not-affected"></a>Som inte påverkas

Om du hanterar autoskalning via Azure Portal, är den [nya Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)-, PowerShell-, CLI-eller Resource Manager-mallar ingen åtgärd nödvändig.  

Om du använder mått via Azure Portal eller via olika [övervaknings partner tjänster](../partners.md)krävs ingen åtgärd. Microsoft arbetar med övervaknings partners för att migrera till de nya API: erna.

## <a name="who-is-affected"></a>Som påverkas

Den här artikeln gäller om du använder följande komponenter:

- **Klassisk Azure Insights SDK** – om du använder den [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)växlar du till att använda den nya Azure Monitor SDK för [.net](https://github.com/azure/azure-libraries-for-net#download) eller [Java](https://github.com/azure/azure-libraries-for-java#download). Hämta [Azure Monitor SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klassisk autoskalning** – om du anropar de [klassiska API: erna för autoskalning](/previous-versions/azure/reference/mt348562(v=azure.100)) från dina anpassade verktyg eller med hjälp av den [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), bör du växla till att använda [Resource Manager Azure Monitor REST API](/rest/api/monitor/autoscalesettings).

- **Klassiska mått** – om du använder mått med de [klassiska REST-API: erna](/previous-versions/azure/reference/dn510374(v=azure.100)) eller  [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) från anpassade verktyg, bör du byta till [Resource Manager Azure Monitor REST API](/rest/api/monitor/autoscalesettings). 

Om du är osäker på om din kod eller anpassade verktyg anropar de klassiska API: erna kan du titta på följande:

- Granska den URI som refereras i din kod eller ditt verktyg. De klassiska API: erna använder URI: n https://management.core.windows.net . Du bör använda den nyare URI: n för Resource Manager-baserade API: er som börjar med `https://management.azure.com/` .

- Jämför sammansättnings namnet på din dator. Den äldre klassiska sammansättningen finns på  https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/ .

- Om du använder certifikatautentisering för att få åtkomst till mått eller autoskalning av API: er, använder du en klassisk slut punkt och ett bibliotek. De nyare Resource Manager-API: erna kräver Azure Active Directory autentisering via ett huvud namn för tjänsten eller användarens huvud namn.

- Om du använder anrop som refereras till i dokumentationen på någon av följande länkar, använder du de äldre klassiska API: erna.

  - [Klass bibliotek för Windows. Azure. Management. övervaknings klass](/previous-versions/azure/dn510414(v=azure.100))

  - [Övervakning (klassisk) .NET](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations-gränssnitt](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Varför du bör byta

Alla befintliga funktioner för autoskalning och mått kommer att fortsätta att fungera via de nya API: erna.  

Migrering över till nyare API: er levereras med Resource Manager-baserade funktioner, till exempel stöd för konsekvent Azure-rollbaserad åtkomst kontroll (Azure RBAC) i alla övervaknings tjänster. Du får också ytterligare funktioner för mått: 

- stöd för dimensioner
- konsekvent 1-minuters Metric-kornig het i alla tjänster 
- bättre frågor
- högre datakvarhållning (93 dagar av mått eller 30 dagar) 

Precis som alla andra tjänster i Azure ger Resource Manager-baserade Azure Monitor API: er bättre prestanda, skalbarhet och tillförlitlighet. 

## <a name="what-happens-if-you-do-not-migrate"></a>Vad händer om du inte migrerar

### <a name="before-retirement"></a>Innan du drar tillbaka

Det sker ingen direkt påverkan på dina Azure-tjänster eller deras arbets belastningar.  

### <a name="after-retirement"></a>Efter pensionering

Alla anrop till de klassiska API: erna som listas tidigare kommer att Miss Miss läge och returnera fel meddelanden liknande följande:

För autoskalning: *detta API är föråldrat. Använd Azure Portal, Azure Monitor SDK, PowerShell, CLI eller Resource Manager-mallar för att hantera inställningar för autoskalning*.  

För mått: *detta API är inaktuellt. Använd Azure Portal, Azure Monitor SDK, PowerShell, CLI för att fråga efter mått*.

## <a name="email-notifications"></a>E-postmeddelanden

Ett meddelande om indragningen skickades till e-postadresser för följande konto roller: 

- Konto-och tjänst administratörer
- Medadministratörer  

Om du har några frågor kan du kontakta oss på MonitorClassicAPIhelp@microsoft.com .  

## <a name="references"></a>Referenser

- [Nyare REST-API: er för Azure Monitor](/rest/api/monitor/) 
- [Nyare Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

