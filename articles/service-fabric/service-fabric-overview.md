---
title: Översikt över Azure Service Fabric
description: Service Fabric är en distribuerad system plattform för att skapa skalbara, pålitliga och enkelt hanterade mikrotjänster.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: b6a14ba3f905fbef3fc3796b616cd594941a3d60
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97028639"
---
# <a name="overview-of-azure-service-fabric"></a>Översikt över Azure Service Fabric

Azure Service Fabric är en [distribuerad system plattform](#container-orchestration) som gör det enkelt att paketera, distribuera och hantera skalbara och pålitliga mikrotjänster och behållare. Service Fabric också åtgärdar de stora utmaningarna med att [utveckla och hantera](#application-lifecycle-management) inbyggda Cloud-program.

En viktig differentiering av Service Fabric är det starkt fokuserat på att skapa tillstånds känsliga tjänster. Du kan använda Service Fabric [programmerings modellen](#stateless-and-stateful-microservices) eller köra behållar tillstånds känsliga tjänster skrivna på valfritt språk eller kod. Du kan skapa [Service Fabric kluster var som helst](#any-os-any-cloud), inklusive Windows Server och Linux lokalt och andra offentliga moln, förutom Azure.

![Service Fabrics plattformen tillhandahåller livs cykel hantering, tillgänglighet, dirigering, programmerings modeller, hälso tillstånd och övervakning, verktyg för utveckling och hantering och automatisk skalning – i Azure, lokalt, i andra moln och på din utvecklings dator][Image1]

Service Fabric används idag för många Microsoft-tjänster, inklusive Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype for Business och många fler Azure-tjänster.

## <a name="container-orchestration"></a>Orkestrering av containrar

Service Fabric är Microsofts [behållar Orchestrator](service-fabric-cluster-resource-manager-introduction.md) för att distribuera och hantera mikrotjänster i ett kluster med datorer, vilket drar nytta av de lektioner som har lärt oss att köra Microsoft-tjänster i enorm skala. Service Fabric kan distribuera program på några sekunder med hög densitet med hundratals eller tusentals program eller behållare per dator. Med Service Fabric kan du blanda båda tjänsterna i processer och tjänster i behållare i samma program.

[Lär dig mer om att Service Fabric](service-fabric-content-roadmap.md) Core-koncept, programmerings modeller, program livs cykel, testning, kluster och hälso övervakning.

## <a name="stateless-and-stateful-microservices"></a>Tillstånds lösa och tillstånds känsliga mikrotjänster

Service Fabric ger en avancerad och lätt körning som stöder tillstånds lösa och tillstånds känsliga mikrotjänster. En viktig differentiering av Service Fabric är dess stabila stöd för att skapa tillstånds känsliga tjänster, antingen med Service Fabric [inbyggda programmerings modeller](service-fabric-choose-framework.md) eller tillstånds känsliga tjänster för behållare.

Lär dig mer om [program scenarier](service-fabric-application-scenarios.md) som drar nytta av Service Fabric tillstånds känsliga tjänster.

## <a name="application-lifecycle-management"></a>Application Lifecycle Management

Service Fabric ger stöd för den fullständiga program livs cykeln och CI/CD för moln program, inklusive behållare: utveckling genom distribution, daglig övervakning, hantering och underhåll för att utföra inaktive ring. Service Fabric är integrerat med CI/CD-verktyg som [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) och [Octopus Deploy](https://octopus.com/), och kan användas med valfritt annat populärt CI/CD-verktyg.

Mer information om hantering av programlivscykel finns i [Programlivscykel](service-fabric-application-lifecycle.md). Information om hur du distribuerar befintliga program till Service Fabric finns i [distribuera en körbar gäst](service-fabric-deploy-existing-app.md).

## <a name="any-os-any-cloud"></a>Alla OS, alla moln

Du kan skapa kluster för Service Fabric i många miljöer, inklusive [Azure eller lokalt](service-fabric-deploy-anywhere.md), på [Windows Server eller Linux](service-fabric-linux-windows-differences.md). Du kan till och med skapa kluster i andra offentliga moln. Utvecklings miljön i Service Fabric SDK är identisk med produktions miljön, utan att några emulatorer är inblandade. Vad som händer i det lokala utvecklings klustret är vad som körs på dina kluster i andra miljöer.

För [Windows-utveckling](service-fabric-get-started.md)är Service Fabric .NET SDK integrerat med Visual Studio och PowerShell. För [Linux-utveckling](service-fabric-get-started-linux.md)är Service Fabric Java SDK integrerat med Sol förmörkelse och Yeoman används för att skapa mallar för Java, .net Core och behållar program.

## <a name="compliance"></a>Efterlevnad

Azure Service Fabric Resource Provider är tillgänglig i alla Azure-regioner och är kompatibel med alla Azure Compliance-certifieringar, inklusive: SOC, ISO, PCI DSS, HIPAA och GDPR. En fullständig lista finns i [Microsoft Compliance-erbjudanden](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Nästa steg

Skapa och distribuera ditt första program på Azure Service Fabric:

> [!div class="nextstepaction"]
> [Service Fabric snabb start][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
