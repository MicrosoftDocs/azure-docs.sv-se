---
title: Översikt
description: Ta reda på hur Azure App Service hjälper dig att utveckla och hantera webbappar.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 771c79f56a31c83f2152886ad6cf68367776f83f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767236"
---
# <a name="app-service-overview"></a>Översikt över App Service

*Azure App Service* är en HTTP-baserad tjänst som är värd för webbprogram, REST API:er och mobila serverdelsappar. Du kan utveckla i det språk du föredrar, oavsett om det är .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python. Program körs och skalas enkelt i både Windows- [och Linux-baserade](#app-service-on-linux)miljöer.

App Services lägger inte bara till kraften hos Microsoft Azure i ditt program, t.ex. säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan också dra nytta av dess DevOps-funktioner, till exempel kontinuerlig distribution från Azure DevOps, GitHub, Docker Hub och andra källor, pakethantering, mellanlagringsmiljöer, anpassad domän och TLS/SSL-certifikat. 

Med App Service betalar du för de Azure-beräkningsresurer du använder. De beräkningsresurser som du använder bestäms av _App Service plan_ som du kör dina appar på. Mer information finns i [Översikt över Azure App Service planer](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Varför använda Azure Apptjänst?

Här följer några funktioner i App Service:

* **Flera språk och ramverk** – App Service har förstklassigt stöd för ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP och Python. Du kan också köra [PowerShell och andra skript och körbara filer](webjobs-create.md) som bakgrundstjänster.
* **Hanterad produktionsmiljö** – App Service [automatiskt korrigeringar och underhåller operativsystemet och språkramverken](overview-patch-os-runtime.md) åt dig. Ägna tid åt att skriva bra appar och låt Azure bekymra sig om plattformen.
* **Containerisering och Docker –** Dockerisera din app och var värd för en anpassad Windows- eller Linux-container i App Service. Kör appar med flera containrar med Docker Compose. Migrera dina Docker-kunskaper direkt till App Service.
* **DevOps-optimering** – Konfigurera [kontinuerlig integrering och distribution](deploy-continuous-deployment.md) med Azure DevOps, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Flytta upp uppdateringar via [test- och mellanlagringsmiljöer](deploy-staging-slots.md). Hantera dina appar i App Service med [Azure PowerShell](/powershell/azure/) eller det [plattformsoberoende kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli).
* **Global skala med hög tillgänglighet** – skala [upp](manage-scale-up.md) och [ned](../azure-monitor/autoscale/autoscale-get-started.md) manuellt och automatiskt. Hantera appar var som helst i Microsofts globala datacenterinfrastruktur med hög tillgänglighet tack vare [serviceavtalet](https://azure.microsoft.com/support/legal/sla/app-service/) för App Service.
* **Anslutningar till SaaS-plattformar och lokala data** – välj bland fler än 50 [anslutningsappar](../connectors/apis-list.md) för företagssystem (till exempel SAP), SaaS-tjänster (till exempel Salesforce) och internettjänster (till exempel Facebook). Åtkomst till lokala data via [hybridanslutningar ](app-service-hybrid-connections.md) och [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Säkerhet och efterlevnad** – App Service [uppfyller ISO, SOC och PCI](https://www.microsoft.com/en-us/trustcenter). Autentisera användare med [Azure Active Directory,](configure-authentication-provider-aad.md) [Google](configure-authentication-provider-google.md), [Facebook,](configure-authentication-provider-facebook.md) [Twitter](configure-authentication-provider-twitter.md)eller [Microsoft-konto](configure-authentication-provider-microsoft.md). Skapa [IP-adressbegränsningar](app-service-ip-restrictions.md) och [hantera tjänstidentiteter](overview-managed-identity.md).
* **Programmallar** – välj från en omfattande lista med programmallar i [Azure Marketplace](https://azure.microsoft.com/marketplace/), till exempel WordPress, Joomla och Drupal.
* **Visual Studio och Visual Studio-kodintegrering** – Dedikerade verktyg i Visual Studio och Visual Studio Code effektiviserar arbetet med att skapa, distribuera och felsöka.
* **API och mobila funktioner** – App Service ger nyckelfärdigt CORS-stöd för RESTful API-scenarier, och förenklar mobilappscenarier genom att möjliggöra autentisering, synkronisering av offlinedata, push-meddelanden med mera.
* **Serverlös kod** – kör ett kodfragment eller skript på begäran utan att explicit behöva etablera eller hantera infrastruktur, och betala bara för den beräkningstid som koden faktiskt använder (se [Azure Functions](../azure-functions/index.yml)).

Förutom App Service erbjuder Azure andra tjänster som kan användas till att hantera webbplatser och webbappar. För de flesta scenarier är App Service det bästa valet.  För mikrotjänstarkitektur bör du [överväga Azure Spring-Cloud Service](../spring-cloud/index.yml) eller [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Om du behöver mer kontroll över de virtuella datorer som koden körs på kan du använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Mer information om hur du väljer mellan dessa Azure-tjänster hittar du i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>App Service på Linux

App Service kan också vara värdar för webbappar inbyggt i Linux för programstackar som stöds. Den kan också köra anpassade Linux-containrar (kallas även Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Inbyggda språk och ramverk

App Service på Linux har stöd för ett antal språkspecifika inbyggda avbildningar. Distribuera bara din kod. Språk som stöds är: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core och Ruby. Kör [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes) för att visa de senaste språken och de versioner som stöds. Om den körning som programmet kräver inte stöds i de inbyggda avbildningarna kan du distribuera den med en anpassad container.

Inaktuella körningar tas regelbundet bort från Web Apps blad för att skapa och konfigurera i portalen. Dessa körningar är dolda från portalen när de är inaktuella av den underhållande organisationen eller om de har betydande säkerhetsrisker. De här alternativen är dolda för att vägleda kunderna till de senaste körningarna där de blir mest framgångsrika. 

När en inaktuell körning är dold från portalen fortsätter alla dina befintliga webbplatser som använder den versionen att köras. Om en körning tas bort helt från App Service plattformen får dina Azure-prenumerationsägare ett e-postmeddelande innan borttagningen.

Om du behöver skapa en annan webbapp med en inaktuell körningsversion som inte längre visas på portalen kan du läsa språkkonfigurationsguiderna för instruktioner om hur du hämtar körningsversionen av webbplatsen. Du kan använda Azure CLI för att skapa en annan plats med samma körning. Du kan också använda knappen Exportera mall **på** webbappbladet i portalen för att exportera en ARM-mall för webbplatsen. Du kan återanvända den här mallen för att distribuera en ny plats med samma körning och konfiguration.

### <a name="limitations"></a>Begränsningar

- App Service på Linux stöds inte på [den delade](https://azure.microsoft.com/pricing/details/app-service/plans/) prisnivån. 
- Du kan inte blanda Windows- och Linux-appar i samma App Service plan.  
- Tidigare kan du inte blanda Windows- och Linux-appar i samma resursgrupp. Alla resursgrupper som skapats den 21 januari 2021 eller senare har dock stöd för det här scenariot. För resursgrupper som skapats före den 21 januari 2021 kommer möjligheten att lägga till blandade plattformsdistributioner snart att distribueras i Azure-regioner (inklusive nationella molnregioner).
- I Azure Portal endast funktioner som för närvarande fungerar för Linux-appar. När funktioner är aktiverade aktiveras de på portalen.
- När de distribueras till inbyggda avbildningar allokeras din kod och ditt innehåll till en lagringsvolym för webbinnehåll som backas upp av Azure Storage. Diskfördröjningen för den här volymen är högre och mer varierande än containerfilsystemets svarstid. Appar som kräver omfattande skrivskyddade åtkomst till innehållsfiler kan dra nytta av alternativet för anpassad container, som placerar filer i containerfilsystemet i stället för på innehållsvolymen.

## <a name="next-steps"></a>Nästa steg

Skapa din första webbapp.

> [!div class="nextstepaction"]
> [ASP.NET Core (i Windows eller Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (i Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (i Windows eller Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (på Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (i Windows eller Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (i Windows eller Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (på Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (i Windows eller Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Anpassad container (Windows eller Linux)](tutorial-custom-container.md)
