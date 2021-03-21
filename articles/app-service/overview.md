---
title: Översikt
description: Ta reda på hur Azure App Service hjälper dig att utveckla och hantera webbappar.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 668988ae34c2f97f3baca3f360c13e3ec3e30731
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586358"
---
# <a name="app-service-overview"></a>Översikt över App Service

*Azure App Service* är en HTTP-baserad tjänst som är värd för webb program, REST API: er och mobila Server delar. Du kan utveckla i det språk du föredrar, oavsett om det är .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python. Program körs och skalas enkelt i både Windows-och [Linux](#app-service-on-linux)-baserade miljöer.

App Services lägger inte bara till kraften hos Microsoft Azure i ditt program, t.ex. säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan också dra nytta av dess DevOps-funktioner, till exempel kontinuerlig distribution från Azure DevOps, GitHub, Docker Hub och andra källor, paket hantering, mellanlagrings miljöer, anpassad domän och TLS/SSL-certifikat. 

Med App Service betalar du för de Azure-beräkningsresurer du använder. De beräknings resurser som du använder bestäms av _App Service plan_ som du kör apparna på. Mer information finns i [Översikt över Azure App Service planer](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Varför använda Azure Apptjänst?

Här följer några funktioner i App Service:

* **Flera språk och ramverk** – App Service har förstklassigt stöd för ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP och Python. Du kan också köra [PowerShell och andra skript och körbara filer](webjobs-create.md) som bakgrundstjänster.
* **Hanterad produktions miljö** – App Service automatiskt [korrigering och underhåll av operativ system-och språk ramverk](overview-patch-os-runtime.md) . Ägna tid åt att skriva Fantastiska appar och låta Azure bekymra dig om plattformen.
* **Skapa behållare och Docker** – hantera din app och vara värd för en anpassad Windows-eller Linux-behållare i App Service. Köra appar med flera behållare med Docker Compose. Migrera dina Docker-färdigheter direkt till App Service.
* **DevOps-optimering** – Konfigurera [kontinuerlig integrering och distribution](deploy-continuous-deployment.md) med Azure DevOps, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Flytta upp uppdateringar via [test- och mellanlagringsmiljöer](deploy-staging-slots.md). Hantera dina appar i App Service med [Azure PowerShell](/powershell/azure/) eller det [plattformsoberoende kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli).
* **Global skala med hög tillgänglighet** – skala [upp](manage-scale-up.md) och [ned](../azure-monitor/autoscale/autoscale-get-started.md) manuellt och automatiskt. Hantera appar var som helst i Microsofts globala datacenterinfrastruktur med hög tillgänglighet tack vare [serviceavtalet](https://azure.microsoft.com/support/legal/sla/app-service/) för App Service.
* **Anslutningar till SaaS-plattformar och lokala data** – välj bland fler än 50 [anslutningsappar](../connectors/apis-list.md) för företagssystem (till exempel SAP), SaaS-tjänster (till exempel Salesforce) och internettjänster (till exempel Facebook). Åtkomst till lokala data via [hybridanslutningar ](app-service-hybrid-connections.md) och [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Säkerhet och efterlevnad** – App Service [uppfyller ISO, SOC och PCI](https://www.microsoft.com/en-us/trustcenter). Autentisera användare med [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md)eller [Microsoft-konto](configure-authentication-provider-microsoft.md). Skapa [IP-adressbegränsningar](app-service-ip-restrictions.md) och [hantera tjänstidentiteter](overview-managed-identity.md).
* **Programmallar** – välj från en omfattande lista med programmallar i [Azure Marketplace](https://azure.microsoft.com/marketplace/), till exempel WordPress, Joomla och Drupal.
* **Visual Studio och Visual Studio Code integration** – dedikerade verktyg i Visual Studio och Visual Studio Code effektiviserar arbetet med att skapa, distribuera och felsöka.
* **API och mobila funktioner** – App Service ger nyckelfärdigt CORS-stöd för RESTful API-scenarier, och förenklar mobilappscenarier genom att möjliggöra autentisering, synkronisering av offlinedata, push-meddelanden med mera.
* **Serverlös kod** – kör ett kodfragment eller skript på begäran utan att explicit behöva etablera eller hantera infrastruktur, och betala bara för den beräkningstid som koden faktiskt använder (se [Azure Functions](../azure-functions/index.yml)).

Förutom App Service erbjuder Azure andra tjänster som kan användas till att hantera webbplatser och webbappar. För de flesta scenarier är App Service det bästa valet.  För arkitektur för mikrotjänster bör du tänka på [Azure Spring-Cloud service](../spring-cloud/index.yml) eller [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Om du behöver mer kontroll över de virtuella datorer som koden körs på, bör du tänka på [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Mer information om hur du väljer mellan dessa Azure-tjänster hittar du i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>App Service på Linux

App Service kan också vara värd för webbappar internt i Linux för program stackar som stöds. Den kan också köra anpassade Linux-behållare (kallas även Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Inbyggda språk och ramverk

App Service på Linux stöder ett antal språkspecifika inbyggda avbildningar. Distribuera bara din kod. Språk som stöds är: Node.js, Java (JRE 8 & JRE 11), PHP, python, .NET Core och Ruby. Kör [`az webapp list-runtimes --linux`](/cli/azure/webapp#az-webapp-list-runtimes) för att visa de senaste språken och de versioner som stöds. Om körningen av programmet kräver att programmet inte stöds i de inbyggda avbildningarna kan du distribuera det med en anpassad behållare.

Föråldrade körningar tas regelbundet bort från Web Apps skapa och konfigurera-blad i portalen. Dessa körningar är dolda från portalen när de är föråldrade av den underhålla organisationen eller har fått betydande sårbarheter. De här alternativen är dolda för att hjälpa kunder till de senaste körningarna där de är mest framgångs rika. 

När en föråldrad körning är dold från portalen kommer alla befintliga webbplatser som använder den versionen att fortsätta att köras. Om en runtime tas bort helt från App Services plattformen får dina Azure-Prenumerationens ägare ett e-postmeddelande innan borttagningen.

Om du behöver skapa en annan webbapp med en inaktuell runtime-version som inte längre visas på portalen, se språk konfigurations guiderna för instruktioner om hur du hämtar körnings versionen för din webbplats. Du kan använda Azure CLI för att skapa en annan plats med samma körnings tid. Du kan också använda knappen **Exportera mall** på bladet webbapp på portalen för att exportera en arm-mall på platsen. Du kan återanvända den här mallen för att distribuera en ny plats med samma körning och konfiguration.

### <a name="limitations"></a>Begränsningar

- App Service på Linux stöds inte på den [delade](https://azure.microsoft.com/pricing/details/app-service/plans/) pris nivån. 
- Du kan inte mixa Windows-och Linux-appar i samma App Service plan.  
- Tidigare kan du inte blanda Windows-och Linux-appar i samma resurs grupp. Men alla resurs grupper som skapats den 21 januari 2021 har stöd för det här scenariot. För resurs grupper som skapats före den 21 januari 2021 kommer möjligheten att lägga till blandade plattforms distributioner att distribueras i Azure-regioner (inklusive nationella moln regioner) snart.
- Azure Portal visar endast funktioner som för närvarande fungerar för Linux-appar. När funktionerna är aktiverade är de aktiverade på portalen.
- När den distribueras till inbyggda avbildningar allokeras din kod och ditt innehåll till en lagrings volym för webb innehåll, som backas upp av Azure Storage. Disk fördröjningen för den här volymen är högre och mer variabel än svars tiden för behållar systemet. Appar som kräver tung skrivskyddad åtkomst till innehållsfiler kan dra nytta av alternativet för anpassad behållare, som placerar filer i behållarens fil system i stället för på innehålls volymen.

## <a name="next-steps"></a>Nästa steg

Skapa din första webbapp.

> [!div class="nextstepaction"]
> [ASP.NET Core (på Windows eller Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (i Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (på Windows eller Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (på Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (på Windows eller Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (på Windows eller Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (på Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (i Windows eller Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Anpassad behållare (Windows eller Linux)](tutorial-custom-container.md)
