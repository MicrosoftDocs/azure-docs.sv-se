---
title: Skillnader mellan Cloud Services och Service Fabric
description: En översikt över hur du migrerar program från Cloud Services till Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: c7e7d346b5a39a262d1d55265becadb1c718cc04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575779"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Lär dig mer om skillnaderna mellan Cloud Services och Service Fabric innan du migrerar program.
Microsoft Azure Service Fabric är nästa generations moln program plattform för mycket skalbara, mycket pålitliga distribuerade program. Den introducerar många nya funktioner för paketering, distribution, uppgradering och hantering av distribuerade moln program. 

Det här är en introduktions guide för att migrera program från Cloud Services till Service Fabric. Den fokuserar främst på arkitektur och design skillnader mellan Cloud Services och Service Fabric.

## <a name="applications-and-infrastructure"></a>Program och infrastruktur
En grundläggande skillnad mellan Cloud Services och Service Fabric är relationen mellan virtuella datorer, arbets belastningar och program. En arbets belastning här definieras som den kod som du skriver för att utföra en viss uppgift eller tillhandahålla en tjänst.

* **Cloud Services är om att distribuera program som virtuella datorer.** Koden som du skriver är nära kopplad till en virtuell dator instans, till exempel en webb-eller arbets roll. För att distribuera en arbets belastning i Cloud Services är att distribuera en eller flera virtuella dator instanser som kör arbets belastningen. Det finns ingen åtskillnad mellan program och virtuella datorer, och därför finns det ingen formell definition av ett program. Ett program kan ses som en uppsättning webb-eller arbets Rolls instanser i en Cloud Services distribution eller som en hel Cloud Services distribution. I det här exemplet visas ett program som en uppsättning roll instanser.

![Cloud Services program och topologi][1]

* **Service Fabric är om att distribuera program till befintliga virtuella datorer eller datorer som kör Service Fabric på Windows eller Linux.** De tjänster som du skriver är helt fristående från den underliggande infrastrukturen, som är sammankopplade av Service Fabric program plattform, så att ett program kan distribueras till flera miljöer. En arbets belastning i Service Fabric kallas för en "tjänst" och en eller flera tjänster grupperas i ett formellt definierat program som körs på Service Fabric-programplattformen. Flera program kan distribueras till ett enda Service Fabric-kluster.

![Service Fabric program och topologi][2]

Service Fabric sig själv är ett program plattforms lager som körs på Windows eller Linux, medan Cloud Services är ett system för att distribuera Azure-hanterade virtuella datorer med arbets belastningar anslutna.
Service Fabric program modellen har ett antal fördelar:

* Snabba distributions tider. Det kan ta lång tid att skapa VM-instanser. I Service Fabric distribueras de virtuella datorerna bara en gång för att bilda ett kluster som är värd för Service Fabric program plattformen. Från och med nu kan programpaket distribueras till klustret mycket snabbt.
* Hög densitets värd. I Cloud Services är en VM-roll värd för en arbets belastning. I Service Fabric separeras programmen från de virtuella datorer som kör dem, vilket innebär att du kan distribuera ett stort antal program till ett litet antal virtuella datorer, vilket kan sänka den totala kostnaden för större distributioner.
* Service Fabrics plattformen kan köras var som helst som har Windows Server-eller Linux-datorer, oavsett om det är Azure eller lokalt. Plattformen ger ett abstraktions lager över den underliggande infrastrukturen så att ditt program kan köras i olika miljöer. 
* Hantering av distribuerade program. Service Fabric är en plattform som inte bara är värd för distribuerade program, utan även hjälper till att hantera deras livs cykel oberoende av den virtuella datorns eller livs cykelns värd.

## <a name="application-architecture"></a>Programmets arkitektur
Arkitekturen i ett Cloud Services program innehåller vanligt vis många beroenden för externa tjänster, till exempel Service Bus, Azure Table och Blob Storage, SQL, Redis och andra för att hantera tillstånd och data för ett program och kommunikation mellan webb-och arbets roller i en Cloud Services distribution. Ett exempel på ett komplett Cloud Services program kan se ut så här:  

![Cloud Services arkitektur][9]

Service Fabric program kan också välja att använda samma externa tjänster i ett fullständigt program. Med hjälp av det här exemplet Cloud Services-arkitektur, är den enklaste sökvägen från Cloud Services till Service Fabric bara att ersätta Cloud Services-distributionen med ett Service Fabric program och samtidigt behålla den övergripande arkitekturen. Web-och Worker-rollerna kan hamna i Service Fabric tillstånds lösa tjänster med minimala kod ändringar.

![Service Fabric arkitektur efter enkel migrering][10]

I det här skedet bör systemet fortsätta att fungera på samma sätt som innan. Genom att dra nytta av Service Fabric tillstånds känsliga funktioner kan externa tillstånds lager vara inaktiverade som tillstånds känsliga tjänster i förekommande fall. Detta är mer involverad än en enkel migrering av webb-och arbets roller för att Service Fabric tillstånds lösa tjänster, eftersom det krävs att du skriver anpassade tjänster som ger motsvarande funktioner i programmet som de externa tjänsterna gjorde tidigare. Fördelarna med att göra detta är: 

* Tar bort externa beroenden 
* Enhetliga modeller för distribution, hantering och uppgradering. 

Ett exempel på en utöknings arkitektur för att göra dessa tjänster kan se ut så här:

![Service Fabric arkitektur efter fullständig migrering][11]

## <a name="communication-and-workflow"></a>Kommunikation och arbets flöde
De flesta moln tjänst program består av mer än en nivå. På samma sätt består ett Service Fabric program av mer än en tjänst (vanligt vis många tjänster). Två vanliga kommunikations modeller är direkt kommunikation och via en extern varaktig lagring.

### <a name="direct-communication"></a>Direkt kommunikation
Med direkt kommunikation kan nivåer kommunicera direkt genom slut punkt som exponeras av varje nivå. I tillstånds lösa miljöer, till exempel Cloud Services, innebär detta att välja en instans av en virtuell dator roll, antingen slumpmässigt eller Round-Robin för att utjämna belastningen och ansluta till slut punkten direkt.

![Cloud Services direkt kommunikation][5]

 Direkt kommunikation är en gemensam kommunikations modell i Service Fabric. Den viktigaste skillnaden mellan Service Fabric och Cloud Services är att i Cloud Services du ansluter till en virtuell dator, medan du i Service Fabric ansluter till en tjänst. Detta är en viktig skillnad för ett par orsaker:

* Tjänster i Service Fabric är inte kopplade till de virtuella datorer som är värdar för dem. tjänsterna kan flyttas runt i klustret, och i själva verket förväntas gå vidare av olika anledningar: resurs balansering, redundans, program-och infrastruktur uppgraderingar samt placerings-eller belastnings begränsningar. Det innebär att en tjänst instanss adress kan ändras när som helst. 
* En virtuell dator i Service Fabric kan vara värd för flera tjänster, var och en med unika slut punkter.

Service Fabric tillhandahåller en funktion för identifiering av tjänst, som kallas Naming Service, som kan användas för att lösa slut punkts adresser för tjänster. 

![Diagram som visar hur Service Fabric tillhandahåller en funktion för identifiering av tjänst, som kallas Naming Service, som kan användas för att lösa slut punkts adresser för tjänster.][6]

### <a name="queues"></a>Köer
En gemensam kommunikations mekanism mellan nivåer i tillstånds lösa miljöer som Cloud Services är att använda en extern lagrings kö för att varaktigt lagra arbets uppgifter från en nivå till en annan. Ett vanligt scenario är en webb nivå som skickar jobb till en Azure-kö eller Service Bus där arbets Rolls instanser kan ta ur kö och bearbeta jobben.

![Cloud Services Queue-kommunikation][7]

Samma kommunikations modell kan användas i Service Fabric. Detta kan vara användbart när du migrerar ett befintligt Cloud Services-program till Service Fabric. 

![Service Fabric direkt kommunikation][8]

## <a name="parity"></a>Paritet
[Cloud Services liknar Service Fabric i grad av kontroll och lätt att använda, men det är nu en äldre tjänst och Service Fabric rekommenderas för ny utveckling](/azure/architecture/guide/technology-choices/compute-decision-tree). följande är en API-jämförelse:


| **Cloud Service-API** | **Service Fabric-API** | **Kommentarer** |
| --- | --- | --- |
| RoleInstance. GetID | FabricRuntime. GetNodeContext. NodeId eller. NodeName | ID är en egenskap för nodnamn |
| RoleInstance. GetFaultDomain | FabricClient. QueryManager. GetNodeList | Filtrera på nodnamn och Använd egenskapen FD |
| RoleInstance. GetUpgradeDomain | FabricClient. QueryManager. GetNodeList | Filtrera på nodnamn och Använd egenskapen Upgrade |
| RoleInstance. GetInstanceEndpoints | FabricRuntime. GetActivationContext eller Naming (ResolveService) | CodePackageActivationContext som tillhandahålls både av FabricRuntime. GetActivationContext och inom replikerna via ServiceInitializationParameters. CodePackageActivationContext som angavs under. Initialize |
| RoleEnvironment.GetRoles | FabricClient. QueryManager. GetNodeList | Om du vill göra samma sortering av filtrering efter typ kan du hämta listan över nodtyper från kluster manifestet via FabricClient. ClusterManager. GetClusterManifest och ta bort roll-/nodtypen därifrån. |
| RoleEnvironment.GetIsAvailable | Connect-WindowsFabricCluster eller skapa en FabricRuntime som pekar på en viss nod | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext. log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext. log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext. log/Temp/Work | * |
| Roll. GetInstances | FabricClient. QueryManager. GetNodeList eller ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime. GetActivationContext eller Naming (ResolveService) | * |

## <a name="next-steps"></a>Nästa steg
Den enklaste sökvägen från Cloud Services till Service Fabric är att bara ersätta Cloud Services-distributionen med ett Service Fabric program, så att programmets övergripande arkitektur är ungefär samma. Följande artikel innehåller en guide som hjälper dig att konvertera en webb-eller arbets roll till en Service Fabric tillstånds lös tjänst.

* [Enkel migrering: konvertera en webb-eller arbets roll till en Service Fabric tillstånds lös tjänst](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png