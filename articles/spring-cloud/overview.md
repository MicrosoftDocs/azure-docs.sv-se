---
title: Introduktion till Azure våren Cloud
description: Lär dig om funktionerna och fördelarna med Azure våren Cloud för att distribuera och hantera Java våren-program i Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 12/02/2020
ms.author: brendm
ms.custom: devx-track-java, contperf-fy21q2
customer intent: As an Azure Cloud user, I want to deploy, run, and monitor Spring Boot microservices.
ms.openlocfilehash: a92e535cb1edeb0eeaa285e442b4b24766e20ead
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879312"
---
# <a name="what-is-azure-spring-cloud"></a>Vad är Azure Spring Cloud?

Azure våren Cloud gör det enkelt att distribuera våren Boot mikroservice-program till Azure utan några kod ändringar.  Tjänsten hanterar infrastrukturen hos våren Cloud-program så att utvecklare kan fokusera på sin kod.  Azure våren Cloud tillhandahåller livs cykel hantering med omfattande övervakning och diagnostik, konfigurations hantering, tjänst identifiering, CI/CD-integrering, blå-grön distributioner med mera.

## <a name="why-use-azure-spring-cloud"></a>Varför ska jag använda Azure våren Cloud?

Distribution av program till Azure våren Cloud har många fördelar.  Du kan:
* Migrera effektivt befintliga våren-appar och hantera skalning och kostnader i molnet.
* Modernisera appar med fjäder moln mönster för att förbättra flexibiliteten och hastigheten på leveransen.
* Kör java i moln skala och öka användningen utan en komplicerad infrastruktur.
* Utveckla och distribuera snabbt utan skapa behållare-beroenden.
* Övervaka produktions arbets belastningar effektivt och smidigt.

Azure våren Cloud stöder både Java [våren start](https://spring.io/projects/spring-boot) och ASP.net Core [Steeltoe](https://steeltoe.io/) -appar. Steeltoe-stöd erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan du experimentera med nya funktioner före den officiella versionen. Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion. Mer information finns i [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) om fil [supportbegäran](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="service-overview"></a>Tjänstöversikt

Som en del av Azures eko system gör Azure våren Cloud enkel bindning till andra Azure-tjänster, inklusive lagring, databaser, övervakning och mycket annat.  

  ![Översikt över Azure våren Cloud](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Azure våren Cloud är en fullständigt hanterad tjänst för våren Boot-appar som hjälper dig att fokusera på att skapa och köra appar utan att behöva hantera infrastrukturen.

* Du behöver bara distribuera din jar v7 eller din kod, och Azure våren-molnet kommer automatiskt att ansluta dina appar till vår livs cykel för vår tjänst och inbyggd program livs cykel.

* Övervakning är enkelt. Efter distributionen kan du övervaka app-prestanda, åtgärda fel och snabbt förbättra programmen. 

* Fullständig integrering med Azures eko system och tjänster.

* Azure våren Cloud är företags klart med fullständigt hanterad infrastruktur, inbyggd livs cykel hantering och enkel övervakning.

## <a name="documentation-overview"></a>Dokumentations översikt
Den här dokumentationen innehåller avsnitt som förklarar hur du kommer igång och utnyttjar Azure våren Cloud Services.

* Kom igång
    * [Lansera din första app](spring-cloud-quickstart.md)
    * [Etablera en moln tjänst för Azure våren](spring-cloud-quickstart-provision-service-instance.md)
    * [Konfigurera konfigurations servern]()
    * [Bygg och distribuera appar](spring-cloud-quickstart-deploy-apps.md)
    * [Använd loggar mått och spårning](spring-cloud-quickstart-logs-metrics-tracing.md)
* Så här gör du
    * [Utveckla](how-to-prepare-app-deployment.md): Förbered ett befintligt Java våren-program för distribution till Azure våren Cloud. När Azure våren Cloud har kon figurer ATS korrekt tillhandahåller robusta tjänster för att övervaka, skala och uppdatera Java våren Cloud-program.
    * [Distribuera](spring-cloud-howto-staging-environment.md): så här konfigurerar du en mellanlagrings distribution med hjälp av ett blått-grönt distributions mönster i Azure våren Cloud. Blå/grön distribution är ett mönster för kontinuerlig leverans för Azure DevOps förlitar sig på att hålla en befintlig (blå) version live, medan en ny (grön) distribueras.
    * [Konfigurera appar](spring-cloud-howto-start-stop-delete.md): starta, stoppa och ta bort dina Azure våren Cloud-program. Ändra programmets tillstånd i Azure våren-molnet med hjälp av antingen Azure Portal eller Azure CLI.
    * [Skala](spring-cloud-howto-scale-manual.md): skala alla mikrotjänstprogram med hjälp av moln instrument panelen för Azure våren i Azure Portal eller genom att använda inställningar för autoskalning. Offentliga IP-adresser är tillgängliga för att kommunicera med externa resurser, till exempel databaser, lagring och nyckel valv.
    * [Övervaka appar](spring-cloud-howto-distributed-tracing.md): distribuerade spårnings verktyg för att enkelt felsöka och övervaka komplexa problem. Azure våren Cloud integrerar våren Cloud Sleuth med Azures Application Insights. Den här integrationen ger kraftfull, distribuerad spårnings funktion från Azure Portal.
    * [Säkra appar](spring-cloud-howto-enable-system-assigned-managed-identity.md): Azure-resurser ger en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod.
    * [Integrering med andra Azure-tjänster](spring-cloud-howto-bind-cosmos.md): i stället för att manuellt konfigurera dina våren Boot-program kan du automatiskt binda valda Azure-tjänster till dina program, till exempel att binda ditt program till en Azure Cosmos DB databas.
    * [Automatisera](spring-cloud-howto-cicd.md): kontinuerlig integrering och kontinuerliga leverans verktyg gör att du snabbt kan distribuera uppdateringar till befintliga program med minimal ansträngning och risk. Azure DevOps hjälper dig att ordna och kontrol lera dessa viktiga uppgifter. 
    * [Felsök](spring-cloud-howto-self-diagnose-solve.md): Azure våren Cloud Diagnostics ger en interaktiv upplevelse som hjälper dig att felsöka appar. Ingen konfiguration krävs. När du hittar problem identifierar Azure våren Cloud Diagnostics problem och vägleder dig till information som hjälper dig att felsöka och lösa problem.
    * [Migrera](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): hur du migrerar ett befintligt moln program eller våren Boot-program som ska köras i Azure våren Cloud.

 För att komma igång, se:

> [!div class="nextstepaction"]
> [Snabb start för fjäder moln](spring-cloud-quickstart.md)

Exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).