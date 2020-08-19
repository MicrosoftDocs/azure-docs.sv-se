---
title: Resurs typer som stöds genom Azure Resource Health | Microsoft Docs
description: Resurs typer som stöds via Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: b6420283f3d3c07182faa2cb2a82ba7b2d784055
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272662"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Resurs typer och hälso kontroller i Azure Resource Health
Nedan visas en fullständig lista över alla kontroller som utförs via resurs hälsa efter resurs typer.

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers
|Utförda kontroller|
|---|
|<ul><li>Är servern igång?</li><li>Har servern slut på minne?</li><li>Startar servern?</li><li>Återställs servern?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Utförda kontroller|
|---|
|<ul><li>Är API Management-tjänsten igång?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts
|Utförda kontroller|
|---|
|<ul><li>Är batch-kontot igång?</li><li>Har kvoten för poolen överskridits för det här batch-kontot?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis
|Utförda kontroller|
|---|
|<ul><li>Är alla cache-noder igång?</li><li>Kan cachen nås från data centret?</li><li>Har cachen nått det maximala antalet anslutningar?</li><li> Har cachen tömt sitt tillgängliga minne? </li><li>Uppstår det ett stort antal sidfel i cacheminnet?</li><li>Är cachen under tung belastning?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft. CDN/profil
|Utförda kontroller|
|---|
|<ul> <li>Är tilläggs portalen tillgänglig för CDN-konfigurations åtgärder?</li><li>Finns det kontinuerliga leverans problem med CDN-slutpunkter?</li><li>Kan användarna ändra konfigurationen för sina CDN-resurser?</li><li>Är konfigurations ändringar spridda enligt det förväntade priset?</li><li>Kan användare hantera CDN-konfigurationen med hjälp av Azure Portal, PowerShell eller API: et?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. classiccompute/virtualmachines
|Utförda kontroller|
|---|
|<ul><li>Är värd servern igång?</li><li>Har värddatorns OS-start slutförts?</li><li>Är den virtuella dator behållaren etablerad och påslagen?</li><li>Finns det någon nätverks anslutning mellan värden och lagrings kontot?</li><li>Har starten av gäst operativ systemet slutförts?</li><li>Finns det pågående planerat underhåll?</li><li>Är värd maskin varan försämrad och förväntas sluta att fungera snart?</li></ul>|

## <a name="microsoftclassiccomputedomainnames"></a>Microsoft. classiccompute/domän namn
|Utförda kontroller|
|---|
|<ul><li>Är moln tjänsten det värdbaserade namnet igång?</li><li>Har värddatorns OS-start slutförts?</li><li>Är den virtuella dator behållaren etablerad och påslagen?</li><li>Finns det någon nätverks anslutning mellan värden och lagrings kontot?</li><li>Har starten av gäst operativ systemet slutförts?</li><li>Finns det pågående planerat underhåll?</li><li>Är värd maskin varan försämrad och förväntas sluta att fungera snart?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. cognitiveservices/konton
|Utförda kontroller|
|---|
|<ul><li>Kan kontot nås från data centret?</li><li>Är Cognitive Services Resource Provider tillgänglig?</li><li>Är kognitiv tjänst tillgänglig i lämplig region?</li><li>Kan Läs åtgärder utföras på det lagrings konto som innehåller resursens metadata?</li><li>Har API-anropet kvot nåtts?</li><li>Har API-anropets Read-Limit nåtts?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft. Compute/hostgroups/hosts
|Utförda kontroller|
|---|
|<ul><li>Är värden igång</li><li>Är värd maskin varan försämrad?</li><li>Är värden friallokerad?</li><li>Har värd maskin varu tjänsten fått till annan maskin vara?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft. Compute/virtualmachines
|Utförda kontroller|
|---|
|<ul><li>Är den server som är värd för den här virtuella datorn igång?</li><li>Har värddatorns OS-start slutförts?</li><li>Är den virtuella dator behållaren etablerad och påslagen?</li><li>Finns det någon nätverks anslutning mellan värden och lagrings kontot?</li><li>Har starten av gäst operativ systemet slutförts?</li><li>Finns det pågående planerat underhåll?</li><li>Är värd maskin varan försämrad och förväntas sluta att fungera snart?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker
|Utförda kontroller|
|---|
|<ul><li>Har det uppstått pipelines körnings problem?</li><li>Är klustret värd för Data Factory felfritt?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. datalakeanalytics/konton
|Utförda kontroller|
|---|
|<ul><li>Har användarna problem med att skicka eller lista sina Data Lake Analytics jobb?</li><li>Kan Data Lake Analytics-jobb inte slutföras på grund av systemfel?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. datalakestore/konton
|Utförda kontroller|
|---|
|<ul><li>Har användare stött på problem med att ladda upp data till Data Lake Store?</li><li>Har användare stött på problem med att hämta data från Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft. data migration/Services
|Utförda kontroller|
|---|
|<ul><li>Har Database migration service inte etableras?</li><li>Har Database migration service stoppats på grund av inaktivitet eller Användarbegäran?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/konton
|Utförda kontroller|
|---|
|<ul><li>Är data resurs kontot igång?</li><li>Är klustret som är värd för data resursen tillgängligt?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers
|Utförda kontroller|
|---|
|<ul><li>Är servern inte tillgänglig på grund av underhåll?</li><li>Är servern inte tillgänglig på grund av omkonfiguration?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers
|Utförda kontroller|
|---|
|<ul><li>Är servern inte tillgänglig på grund av underhåll?</li><li>Är servern inte tillgänglig på grund av omkonfiguration?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers
|Utförda kontroller|
|---|
|<ul><li>Är servern inte tillgänglig på grund av underhåll?</li><li>Är servern inte tillgänglig på grund av omkonfiguration?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/iothubs
|Utförda kontroller|
|---|
|<ul><li>Är IoT Hub igång?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Utförda kontroller|
|---|
|<ul><li>Har det funnits några databaser eller samlings begär Anden som inte hanteras på grund av en Azure Cosmos DB tjänsten inte är tillgänglig?</li><li>Har det inte kunnat hanteras några dokument begär Anden på grund av att en Azure Cosmos DB tjänsten inte är tillgänglig?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/Namespaces
|Utförda kontroller|
|---|
|<ul><li>Är Event Hubs namnrymden med användare skapade fel?</li><li>Håller Event Hubs-namnrymden för närvarande att uppgraderas?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster
|Utförda kontroller|
|---|
|<ul><li>Är kärn tjänster tillgängliga i HDInsight-klustret?</li><li>Kan HDInsight-klustret komma åt nyckeln för BYOK-kryptering i vila?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv
|Utförda kontroller|
|---|
|<ul><li>Går det inte att utföra nyckel valv på grund av problem med Azure Key Vault-plattformen?</li><li>Är förfrågningar till nyckel valv begränsade på grund av för många förfrågningar som kunden har gjort?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft. MachineLearning/WebServices
|Utförda kontroller|
|---|
|<ul><li>Är webb tjänsten igång och körs?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services
|Utförda kontroller|
|---|
|<ul><li>Är medie tjänsten igång?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationgateways
|Utförda kontroller|
|---|
|<ul><li>Är prestanda för Application Gateway försämrade?</li><li>Är Application Gateway tillgängligt?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections
|Utförda kontroller|
|---|
|<ul><li>Är VPN-tunneln ansluten?</li><li>Finns det några konfigurations konflikter i anslutningen?</li><li>Är de i förväg delade nycklarna korrekt konfigurerade?</li><li>Går det att komma åt den lokala VPN-enheten?</li><li>Finns det fel i IPSec/IKE-säkerhetsprincipen?</li><li>Är S2S VPN-anslutningen korrekt etablerad eller i felaktigt tillstånd?</li><li>Är VNET-till-VNET-anslutningen korrekt etablerad eller i ett felaktigt tillstånd?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressroutecircuits
|Utförda kontroller|
|---|
|<ul><li>Är ExpressRoute-kretsen felfritt?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors
|Utförda kontroller|
|---|
|<ul><li>Svarar front dörrens bak sidor med fel i hälso avsökningar?</li><li>Är konfigurations ändringar fördröjda?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare
|Utförda kontroller|
|---|
|<ul><li>Är belastnings Utjämnings slut punkter tillgängliga?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways
|Utförda kontroller|
|---|
|<ul><li>Kan VPN-gatewayen anslutas från Internet?</li><li>Är VPN Gateway i vänte läge?</li><li>Körs VPN-tjänsten på gatewayen?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft. NotificationHubs/namnrymd
|Utförda kontroller|
|---|
|<ul><li>Kan körnings åtgärder som registrering, installation eller sändning utföras i namn området?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. operationalinsights/arbets ytor
|Utförda kontroller|
|---|
|<ul><li>Finns det några indexerings fördröjningar för arbets ytan?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet
|Utförda kontroller|
|---|
|<ul><li>Är kapacitets resursen igång?</li><li>Är alla arbets belastningar igång?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft. PowerBI/workspaceCollections
|Utförda kontroller|
|---|
|<ul><li>Är värd operativ systemet igång?</li><li>Går det att få åtkomst till workspaceCollection utanför data centret?</li><li>Är Power BI Resource Provider tillgänglig?</li><li>Är tjänsten Power BI tillgänglig i rätt region?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices
|Utförda kontroller|
|---|
|<ul><li>Kan diagnostiska åtgärder utföras på klustret?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder
|Utförda kontroller|
|---|
|<ul><li>Har kunder som har genererat en användare genererat Service Bus fel?</li><li>Beror det på att det finns en ökning av de tillfälliga felen på grund av ett Service Bus namn områdes uppgradering?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft. ServiceFabric/kluster
|Utförda kontroller|
|---|
|<ul><li>Är Service Fabric klustret igång?</li><li>Kan Service Fabric klustret hanteras via Azure Resource Manager?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/-databaser
|Utförda kontroller|
|---|
|<ul><li>Är databasen igång?</li></ul>|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser
|Utförda kontroller|
|---|
|<ul><li>Har det loggats in på databasen?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts
|Utförda kontroller|
|---|
|<ul><li>Går det inte att läsa data från lagrings kontot på grund av Azure Storage plattforms problem?</li><li>Går det inte att skriva data till lagrings kontot på grund av Azure Storage plattforms problem?</li><li>Är lagrings klustret där lagrings kontot finns otillgängligt?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs
|Utförda kontroller|
|---|
|<ul><li>Är alla värdar där jobbet körs och körs?</li><li>Gick det inte att starta jobbet?</li><li>Finns det kontinuerliga körnings uppgraderingar?</li><li>Är jobbet i förväntat tillstånd (till exempel körs eller stoppas av kunden)?</li><li>Har jobbet fått slut på minnes undantag?</li><li>Finns det pågående schemalagda beräknings uppdateringar?</li><li>Är körnings hanteraren (kontroll plan) tillgänglig?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/Server grupper
|Utförda kontroller|
|---|
|<ul><li>Är värd servern igång?</li><li>Körs Internet Information Services?</li><li>Körs belastningsutjämnaren?</li><li>Kan App Service planen nås från data centret?</li><li>Är lagrings kontot som är värd för webbplatsens innehåll för Server klustret tillgängligt?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft. Web/Sites
|Utförda kontroller|
|---|
|<ul><li>Är värd servern igång?</li><li>Körs Internet Information Server?</li><li>Körs belastningsutjämnaren?</li><li>Kan webb programmet nås från data centret?</li><li>Är lagrings kontot som är värd för plats innehållet tillgängligt?</li></ul>|

## <a name="next-steps"></a>Efterföljande moment
-  Se [Introduktion till Azure Service Health instrument panel](service-health-overview.md) och [Introduktion till Azure Resource Health](resource-health-overview.md) om du vill veta mer om dem. 
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)
- Konfigurera aviseringar så att du får ett meddelande om hälso problem. Mer information finns i [Konfigurera aviseringar för service Health-händelser](./alerts-activity-log-service-notifications-portal.md). 
