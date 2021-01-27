---
title: Kom igång-guide för utvecklare på Azure | Microsoft Docs
description: Den här artikeln innehåller viktig information för utvecklare som vill komma igång med Microsoft Azure-plattformen för deras utvecklings behov.
author: ggailey777
ms.service: azure
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: 1c28ed90c041a65b2ff8dd08a3aac799795171eb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897467"
---
# <a name="get-started-guide-for-azure-developers"></a>Utvecklarens startguide för Azure

## <a name="what-is-azure"></a>Vad är Azure?

Azure är en komplett moln plattform som kan vara värd för dina befintliga program och effektivisera nya program utveckling. Azure kan även förbättra lokala program. Azure integrerar de moln tjänster som du behöver för att utveckla, testa, distribuera och hantera dina program, samtidigt som du drar nytta av effektiviteten hos molnbaserad data behandling.

Genom att vara värd för dina program i Azure kan du starta små och enkelt skala ditt program när din kund efter frågan växer. Azure erbjuder också den tillförlitlighet som krävs för program med hög tillgänglighet, till exempel även redundans mellan olika regioner. Med [Azure Portal](https://portal.azure.com) kan du enkelt hantera alla dina Azure-tjänster. Du kan också hantera dina tjänster program mässigt med hjälp av tjänste/regionsspecifika API: er och mallar.

Den här guiden är en introduktion till Azure-plattformen för programutvecklare. Det ger vägledning och riktning som du behöver för att börja skapa nya program i Azure eller migrera befintliga program till Azure.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Med alla tjänster som Azure erbjuder kan det vara en skrämma uppgift för att ta reda på vilka tjänster du behöver för att stödja din lösnings arkitektur. I det här avsnittet beskrivs de Azure-tjänster som utvecklare ofta använder. En lista över alla Azure-tjänster finns i [Azure-dokumentationen](../../index.yml).

Först måste du bestämma hur du ska vara värd för ditt program i Azure. Behöver du hantera hela infrastrukturen som en virtuell dator (VM)? Kan du använda de plattforms hanterings funktioner som Azure tillhandahåller? Kanske behöver du en server lös Ramverks struktur för att endast köra kod på värden?

Ditt program behöver moln lagring, som Azure tillhandahåller flera alternativ för. Du kan dra nytta av Azures företagsspecifika autentisering. Det finns också verktyg för molnbaserad utveckling och övervakning, och de flesta värd tjänster erbjuder DevOps-integrering.

Nu ska vi titta på några av de tjänster som vi rekommenderar att du undersöker för dina program.

### <a name="application-hosting"></a>Program värd

Azure tillhandahåller flera molnbaserade beräknings erbjudanden för att köra ditt program så att du inte behöver bekymra dig om infrastruktur informationen. Du kan enkelt skala upp eller skala ut dina resurser när din program användning växer.

Azure erbjuder tjänster som har stöd för dina program utvecklings-och värd behov. Azure tillhandahåller IaaS (Infrastructure as a Service) för att ge dig fullständig kontroll över dina program värdar. Azures PaaS-erbjudanden (Platform as a Service) tillhandahåller de fullständigt hanterade tjänsterna som krävs för att sätta dina appar. Det finns även sant Server lös värd i Azure där allt du behöver göra är att skriva koden.

![Azure Application hosting-alternativ](./media/azure-developer-guide/azure-developer-hosting-options.png)

#### <a name="azure-app-service"></a>Azure App Service

När du vill att den snabbaste sökvägen ska publicera dina webbaserade projekt bör du överväga Azure App Service. App Service gör det enkelt att utöka dina webbappar till att stödja dina mobila klienter och publicera enkelt förbrukade REST-API: er. Den här plattformen tillhandahåller autentisering med hjälp av sociala leverantörer, principbaserad automatisk skalning, testning i produktion och kontinuerliga och containerbaserade distributioner.

Du kan skapa webbappar, Server delar för mobilappar och API Apps.

Eftersom alla tre typer av appar delar App Service runtime, kan du vara värd för en webbplats, stödja mobila klienter och exponera dina API: er i Azure, allt från samma projekt eller lösning. Mer information om App Service finns i [Vad är Azure Web Apps](../../app-service/overview.md).

App Service har utformats med DevOps i åtanke. Det stöder olika verktyg för publicering och kontinuerlig integrering av distributioner. Dessa verktyg omfattar GitHub Webhooks, Jenkins, Azure DevOps, TeamCity och andra.

Du kan migrera dina befintliga program till App Service med hjälp av [verktyget online-migrering](https://appmigration.microsoft.com/).

> **När du ska använda**: Använd App Service när du migrerar befintliga webb program till Azure och när du behöver en fullständigt hanterad värd plattform för dina webb program. Du kan också använda App Service när du behöver stöd för mobila klienter eller exponera REST-API: er med din app.
>
> **Kom igång**: App Service gör det enkelt att skapa och distribuera din första [webbapp](../../app-service/quickstart-dotnetcore.md), [mobilapp](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)eller [API-app](../../app-service/app-service-web-tutorial-rest-api.md).
>
> **Prova nu**: App Service gör att du kan etablera en app med kort livs längd för att testa plattformen utan att behöva registrera dig för ett Azure-konto. Testa plattformen och [skapa din Azure App Service-app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Som en IaaS-Provider (Infrastructure as a Service) kan du använda Azure för att distribuera till eller migrera ditt program till virtuella Windows-eller Linux-datorer. Tillsammans med Azure Virtual Network stöder Azure Virtual Machines distributionen av virtuella Windows-eller Linux-datorer till Azure. Med virtuella datorer har du total kontroll över datorns konfiguration. När du använder virtuella datorer ansvarar du för all serverprogram vara installation, konfiguration, underhåll och operativ system uppdateringar.

På grund av den kontroll nivå som du har med virtuella datorer kan du köra ett brett utbud av Server arbets belastningar på Azure som inte passar in i en PaaS modell. Dessa arbets belastningar omfattar databas servrar, Windows Server Active Directory och Microsoft SharePoint. Mer information finns i Virtual Machines-dokumentationen för [Linux](../../virtual-machines/linux/index.yml) eller [Windows](../../virtual-machines/windows/index.yml).

> **När du ska använda**: Använd Virtual Machines när du vill ha fullständig kontroll över din program infrastruktur eller migrera lokala program arbets belastningar till Azure utan att behöva göra några ändringar.
>
> **Kom igång**: skapa en virtuell [Linux-dator](../../virtual-machines/linux/quick-create-portal.md) eller en [virtuell Windows-dator](../../virtual-machines/windows/quick-create-portal.md) från Azure Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (utan server)

I stället för att oroa dig för att skapa och hantera ett helt program eller infrastrukturen för att köra din kod, vad gör du om du bara skulle kunna skriva din kod och låta den köras som svar på händelser eller enligt ett schema?  [Azure Functions](../../azure-functions/functions-overview.md) är ett "Server lös"-format som du kan använda för att skriva enbart den kod du behöver. Med Functions kan du utlösa kod körning med HTTP-förfrågningar, Webhooks, moln tjänst händelser eller enligt ett schema. Du kan koda i det utvecklings språk som du väljer, till exempel C \# , F \# , Node.js, python eller php. Med förbruknings-baserad fakturering betalar du bara för den tid som koden körs och Azure skalas efter behov.

> **När du ska använda**: Använd Azure Functions när du har kod som utlöses av andra Azure-tjänster, via webbaserade händelser eller enligt ett schema. Du kan också använda funktioner när du inte behöver lägga till ett komplett värdbaserade projekt eller om du bara vill betala för den tid som koden körs. Läs mer i [Azure Functions översikt](../../azure-functions/functions-overview.md).
>
> **Kom igång**: Följ själv studie kursen om funktionen Functions för att [skapa din första funktion](../../azure-functions/functions-get-started.md) från portalen.
>
> **Prova nu**: Azure Functions gör att du kan köra din kod utan att behöva registrera dig för ett Azure-konto. Testa nu och [skapa din första Azure-funktion](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric är en distribuerad system plattform. Den här plattformen gör det enkelt att bygga, paketera, distribuera och hantera skalbara och pålitliga mikrotjänster. Den innehåller också omfattande funktioner för program hantering, till exempel:

* Etablering
* Deploy
* Övervakning
* Uppgradering/uppdatering
* Tas bort

Appar som körs på en delad pool med datorer kan starta små och skala till hundratals eller tusentals datorer efter behov.

Service Fabric stöder WebAPI med Open Web Interface för .NET (OWIN) och ASP.NET Core. Den innehåller SDK: er för att skapa tjänster i Linux i både .NET Core och Java. Mer information om Service Fabric finns i Service Fabric- [dokumentationen](../../service-fabric/index.yml).

> **När du ska använda:** Service Fabric är ett bra val när du skapar ett program eller skriver om ett befintligt program för att använda en arkitektur för mikrotjänster. Använd Service Fabric när du behöver mer kontroll över eller direkt åtkomst till, den underliggande infrastrukturen.
>
> **Kom igång:** [skapa ditt första Azure Service Fabric-program](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md).

#### <a name="azure-spring-cloud"></a>Azure Spring Cloud

Azure våren Cloud är en plattform för Server lös mikrotjänster som gör att du kan bygga, distribuera, skala och övervaka dina program i molnet. Använd våren Cloud för att ta moderna mikrotjänst mönster till våren Boot Apps, vilket eliminerar en exempel kod för att snabbt bygga robusta Java-appar.

* Använd hanterade versioner av våren Cloud Service Discovery and config server, medan vi ser till att dessa kritiska komponenter körs i optimala skick.
* Fokusera på att skapa din affärs logik och vi tar hand om din tjänst körning med säkerhets korrigeringar, efterlevnads standarder och hög tillgänglighet.
* Hantera programmets livs cykel (t. ex.: distribuera, starta, stoppa, skala) ovanpå Azure Kubernetes-tjänsten.
* Bind enkelt anslutningar mellan dina appar och Azure-tjänster som Azure Database for MySQL och Azure cache för Redis.
* Övervaka och Felsök mikrotjänster och program med enhetliga övervaknings verktyg i företags klass som erbjuder djupgående insikter om program beroenden och operationell telemetri.

> **När du ska använda:** Som ett fullständigt hanterat service Azure våren-moln är ett bra val när du minimerar drifts kostnaden som kör våren start/våren-baserade mikrotjänster på Azure. 
>
> **Kom igång:** [distribuera ditt första Azure våren Cloud-program](../../spring-cloud/spring-cloud-quickstart.md).


### <a name="enhance-your-applications-with-azure-services"></a>Förbättra dina program med Azure-tjänster

Tillsammans med program värd tillhandahåller Azure tjänst erbjudanden som kan förbättra funktionerna. Azure kan också förbättra utvecklingen och underhållet av dina program, både i molnet och lokalt.

#### <a name="hosted-storage-and-data-access"></a>Värdbaserad lagring och data åtkomst

De flesta program måste lagra data, så du kan välja att vara värd för ditt program i Azure, överväga en eller flera av följande lagrings-och data tjänster.

* **Azure Cosmos DB**: en globalt distribuerad databas tjänst för flera data modeller. Med den här databasen kan du på ett flexibelt sätt skala data flöde och lagring i valfritt antal geografiska regioner med ett omfattande service avtal.

  > **När du ska använda:** När ditt program behöver dokument-, tabell-eller graf-databaser, inklusive MongoDB-databaser, med flera väldefinierade konsekvens modeller.
  >
  > **Kom igång**: [Bygg en Azure Cosmos DB-webbapp](../../cosmos-db/create-sql-api-dotnet.md). Om du är en MongoDB-utvecklare kan du läsa mer i [bygga en MongoDB-webbapp med Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

* **Azure Storage**: erbjuder hållbar lagring med hög tillgänglighet för blobbar, köer, filer och andra typer av ej relationella data. Storage tillhandahåller lagrings bas för virtuella datorer.

  > **När du ska använda**: när din app lagrar ej relationella data, till exempel nyckel/värde-par (tabeller), blobbar, fil resurser eller meddelanden (köer).
  >
  > **Kom igång**: Välj någon av följande typer av lagring: [blobbar](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [tabeller](../../cosmos-db/tutorial-develop-table-dotnet.md), [köer](../../storage/queues/storage-dotnet-how-to-use-queues.md)eller [filer](../../storage/files/storage-dotnet-how-to-use-files.md).

* **Azure SQL Database**: en Azure-baserad version av Microsoft SQL Server motor för att lagra Relations tabell data i molnet. SQL Database ger förutsägbar prestanda, skalbarhet utan drift avbrott, verksamhets kontinuitet och data skydd.

  > **När du ska använda**: när ditt program kräver data lagring med referens integritet, transaktions stöd och stöd för tsql-frågor.
  >
  > **Kom igång**: [skapa en databas i Azure SQL Database på några minuter med hjälp av Azure Portal](../../azure-sql/database/single-database-create-quickstart.md).


Du kan använda [Azure Data Factory](../../data-factory/introduction.md) för att flytta befintliga lokala data till Azure. Om du inte är redo att flytta data till molnet kan [hybridanslutningar](../../app-service/app-service-hybrid-connections.md) i Azure App Service ansluta din app service värdbaserade app till lokala resurser. Du kan också ansluta till Azure data-och lagrings tjänster från dina lokala program.

#### <a name="docker-support"></a>Docker-stöd

Docker-behållare, en form av OS-virtualisering, gör att du kan distribuera program på ett mer effektivt och förutsägbart sätt. Ett program i behållare fungerar i produktion på samma sätt som i utvecklings-och test systemen. Du kan hantera behållare med hjälp av standard Docker-verktyg. Du kan använda dina befintliga kunskaper och populära verktyg med öppen källkod för att distribuera och hantera behållare baserade program på Azure.

Azure tillhandahåller flera olika sätt att använda behållare i dina program.


* **Azure Kubernetes service**: låter dig skapa, konfigurera och hantera ett kluster med virtuella datorer som är förkonfigurerade för att köra program i behållare. Mer information om Azure Kubernetes-tjänsten finns i [Introduktion till Azure Kubernetes service](../../aks/intro-kubernetes.md).

  > **När du ska använda**: när du behöver skapa produktions klara, skalbara miljöer som tillhandahåller ytterligare schemaläggnings-och hanterings verktyg, eller när du distribuerar ett Docker Swarm-kluster.
  >
  > **Kom igång**: [distribuera ett Kubernetes-tjänst kluster](../../aks/tutorial-kubernetes-deploy-cluster.md).

* **Docker-dator**: låter dig installera och hantera en Docker-motor på virtuella värdar med hjälp av Docker Machine-kommandon.

  >**När du ska använda**: när du snabbt behöver prototyp av en app genom att skapa en enda Docker-värd.

* **Anpassad Docker-avbildning för App Service**: låter dig använda Docker-behållare från ett behållar register eller en kund behållare när du distribuerar en webbapp i Linux.

  > **När du ska använda**: när du distribuerar en webbapp på Linux till en Docker-avbildning.
  >
  > **Kom igång**: [Använd en anpassad Docker-avbildning för App Service på Linux](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux).

### <a name="authentication"></a>Autentisering

Det är viktigt att inte bara veta vem som använder dina program, utan även för att förhindra obehörig åtkomst till dina resurser. Azure tillhandahåller flera olika sätt att autentisera dina app-klienter.

* **Azure Active Directory (Azure AD)**: Microsoft-klient, molnbaserad identitets-och åtkomst hanterings tjänst. Du kan lägga till enkel inloggning (SSO) till dina program genom att integrera med Azure AD. Du kan komma åt katalog egenskaper med hjälp av Azure AD Graph API direkt eller Microsoft Graph-API: et. Du kan integrera med Azure AD-stöd för OAuth 2.0 Authorization Framework och öppna ID Connect genom att använda interna HTTP/REST-slutpunkter och Azure AD-autentiseringspaket.

  > **När du ska använda**: när du vill skapa en SSO-miljö arbetar du med diagrambaserade data eller autentiserar domänbaserade användare.
  >
  > **Kom igång**: Mer information finns i [Azure Active Directory Developer ' s guide](../../active-directory/develop/v2-overview.md).

* **App Service autentisering**: när du väljer app service som värd för din app får du även inbyggt stöd för autentisering för Azure AD, tillsammans med sociala identitets leverantörer – inklusive Facebook, Google, Microsoft och Twitter.

  > **När du ska använda**: om du vill aktivera autentisering i en app service-app med hjälp av Azure AD, sociala identitets leverantörer eller båda.
  >
  > **Kom igång**: Mer information om autentisering i App Service finns i [autentisering och auktorisering i Azure App Service](../../app-service/overview-authentication-authorization.md).

Mer information om rekommenderade säkerhets metoder i Azure finns i [metod tips och mönster för Azure-säkerhet](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Övervakning

Med ditt program igång i Azure måste du övervaka prestanda, titta efter problem och se hur kunderna använder din app. Azure tillhandahåller flera övervaknings alternativ.

* **Application Insights**: en Azure-värdbaserad utöknings bar analys tjänst som integreras med Visual Studio för att övervaka dina Live-webbprogram. Det ger dig de data du behöver för att förbättra prestanda och användbarhet för dina appar kontinuerligt. Den här förbättringen gäller om du är värd för dina program på Azure eller inte.

  > **Kom igång**: Följ [Application Insights själv studie kursen](../../azure-monitor/app/app-insights-overview.md).

* **Azure Monitor**: en tjänst som hjälper dig att visualisera, fråga, cirkulera, arkivera och agera på de mått och loggar som du genererar med din Azure-infrastruktur och dina resurser. Övervakaren är en enda källa för övervakning av Azure-resurser och innehåller de datavyer som visas i Azure Portal.

  > **Kom igång**: [kom igång med Azure Monitor](../../azure-monitor/overview.md).

### <a name="devops-integration"></a>DevOps-integrering

Oavsett om det är etablering av virtuella datorer eller om du publicerar dina webbappar med kontinuerlig integrering, integreras Azure med de flesta populära DevOps-verktygen. Du kan arbeta med de verktyg som du redan har och maximera din befintliga upplevelse med stöd för verktyg som:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Kom igång**: om du vill se DevOps-alternativ för en app service app, se [kontinuerlig distribution till Azure App Service](../../app-service/deploy-continuous-deployment.md).
>
> **Prova nu:** [prova flera av DevOps-integreringarna](https://azure.microsoft.com/try/devops/).

## <a name="azure-regions"></a>Azure-regioner

Azure är en global moln plattform som är allmänt tillgänglig i många regioner runtom i världen. När du etablerar en tjänst, ett program eller en virtuell dator i Azure uppmanas du att välja en region. Den här regionen representerar ett speciellt Data Center där ditt program körs eller var data lagras. Dessa regioner motsvarar vissa platser som publiceras på sidan [Azure-regioner](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>Välj den bästa regionen för ditt program och dina data

En av fördelarna med att använda Azure är att du kan distribuera dina program till olika data center runtom i världen. Den region som du väljer kan påverka programmets prestanda. Till exempel är det bättre att välja en region som är närmare för de flesta kunder för att minska svars tiden i nätverks förfrågningar. Du kanske också vill välja din region för att uppfylla de juridiska kraven för att distribuera din app i vissa länder/regioner. Det är alltid bästa praxis att lagra program data i samma data Center eller i ett Data Center så nära som möjligt till data centret som är värd för ditt program.

### <a name="multi-region-apps"></a>Appar med flera regioner

Även om det är osannolikt är det inte omöjligt för ett helt data Center att gå offline på grund av en händelse som en natur katastrof eller ett Internet haveri. Vi rekommenderar att du är värd för viktiga affärs program i mer än ett Data Center för att få maximal tillgänglighet. Att använda flera regioner kan också minska svars tiden för globala användare och ge ytterligare möjligheter till flexibilitet vid uppdatering av program.

Vissa tjänster, till exempel virtuella datorer och App Services, använder [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) för att möjliggöra stöd för flera regioner med redundans mellan regioner för att stödja företags program med hög tillgänglighet. Ett exempel finns i [referens arkitektur för Azure: kör ett webb program i flera regioner](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**När du ska använda**: när du har företags program och program med hög tillgänglighet som drar nytta av redundans och replikering.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hur hanterar jag mina program och projekt?

Azure innehåller en omfattande uppsättning upplevelser som du kan använda för att skapa och hantera dina Azure-resurser, program och projekt – både program mässigt och i [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Kommando rads gränssnitt och PowerShell

Azure tillhandahåller två sätt att hantera dina program och tjänster från kommando raden. Du kan använda verktyg som bash, Terminal, kommando tolken eller kommando rads verktyget som du väljer. Normalt kan du utföra samma uppgifter från kommando raden som i Azure Portal, till exempel skapa och konfigurera virtuella datorer, virtuella nätverk, webbappar och andra tjänster.

* Med hjälp av [azure Command-Line Interface (CLI)](/cli/azure/install-azure-cli): kan du ansluta till en Azure-prenumeration och program olika uppgifter mot Azure-resurser från kommando raden.

* [Azure PowerShell](/powershell/azure/): innehåller en uppsättning moduler med cmdletar som gör att du kan hantera Azure-resurser med hjälp av Windows PowerShell.

### <a name="azure-portal"></a>Azure-portalen

[Azure Portal](https://portal.azure.com) är ett webbaserat program. Du kan använda Azure Portal för att skapa, hantera och ta bort Azure-resurser och-tjänster. Den innehåller:

* En konfigurerbar instrument panel
* Azure Resource Management-verktyg
* Åtkomst till prenumerations inställningar och fakturerings information. Mer information finns i [Översikt över Azure Portal](https://azure.microsoft.com/features/azure-portal/).

### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API: er som stöder Azure Portal gränssnittet. De flesta av dessa REST API: er stöds också för att låta dig program mässigt etablera och hantera dina Azure-resurser och-program från valfri enhet med Internet. En fullständig uppsättning REST API-dokumentation finns i [Azure rest SDK-referensen](/rest/api/).

### <a name="apis"></a>API:er

Tillsammans med REST API: er kan många Azure-tjänster också hantera resurser från dina program program mässigt med hjälp av plattformsspecifika Azure-SDK: er, inklusive SDK: er för följande utvecklings plattformar:

* [.NET](/dotnet/api/)
* [Node.js](/azure/developer/javascript/)
* [Java](/java/azure)
* [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
* [Python](/azure/python/)
* [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
* [Kör](/azure/go)

Tjänster som [Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) och [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) tillhandahålla SDK: er på klient sidan så att du kan komma åt tjänster från webb-och mobilappar.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Att köra din app på Azure innebär troligen att du arbetar med flera Azure-tjänster. Dessa tjänster följer samma livs cykel och kan betraktas som en logisk enhet. Till exempel kan en webbapp använda Web Apps, SQL Database, lagring, Azure cache för Redis och Azure Content Delivery Network-tjänster. Med [Azure Resource Manager](../../azure-resource-manager/management/overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i en enda, koordinerad åtgärd.

Tillsammans med gruppering och hantering av relaterade resurser i logisk grupp, innehåller Azure Resource Manager distributions funktioner som gör att du kan anpassa distributionen och konfigurationen av relaterade resurser. Du kan till exempel använda Resource Manager-distribution och konfigurera ett program. Det här programmet kan bestå av flera virtuella datorer, en belastningsutjämnare och en databas i Azure SQL Database som en enskild enhet.

Du utvecklar dessa distributioner med hjälp av en Azure Resource Manager mall, som är ett JSON-formaterat dokument. Med mallar kan du definiera en distribution och hantera dina program med hjälp av deklarativ mallar i stället för skript. Dina mallar kan användas i olika miljöer, till exempel testning, mellanlagring och produktion. Du kan till exempel använda mallar för att lägga till en knapp till en GitHub-lagrings platsen som distribuerar koden i lagrings platsen till en uppsättning Azure-tjänster med ett enda klick.

> **När du ska använda**: använder du Resource Manager-mallar när du vill ha en mall-baserad distribution för din app som du kan hantera program mässigt med hjälp av REST API: er, Azure CLI och Azure PowerShell.
>
> **Kom igång**: för att komma igång med mallar, se [Redigera Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Förstå konton, prenumerationer och fakturering

Som utvecklare vill vi gå in i koden och försöka komma igång så snabbt som möjligt med att köra våra program. Vi vill gärna att du ska börja arbeta i Azure så enkelt som möjligt. Azure erbjuder en [kostnads fri utvärderings version](https://azure.microsoft.com/free/)för att hjälpa till att göra det enkelt. Vissa tjänster har även funktionen "Testa gratis", t. ex. [Azure App Service](https://tryappservice.azure.com/), som inte kräver att du ens skapar ett konto. Så roligt som det är att koda och distribuera ditt program till Azure är det också viktigt att ta en stund att förstå hur Azure fungerar. Mer specifikt bör du förstå hur det fungerar från ett syn sätt för användar konton, prenumerationer och fakturering.

### <a name="what-is-an-azure-account"></a>Vad är ett Azure-konto?

Om du vill skapa eller arbeta med en Azure-prenumeration måste du ha ett Azure-konto. Ett Azure-konto är bara en identitet i Azure AD eller i en katalog, t. ex. en arbets-eller skol organisation, som Azure AD litar på. Om du inte tillhör en sådan organisation kan du alltid skapa en prenumeration med ditt Microsoft-konto, som är betrott av Azure AD. Mer information om hur du integrerar lokala Windows Server-Active Directory med Azure AD finns i [integrera dina lokala identiteter med Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Mer information finns i [hur Azure-prenumerationer är associerade med Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Samt definiera enskilda Azure-konto identiteter, även kallade *användare*, kan du definiera *grupper* i Azure AD. Att skapa användar grupper är ett bra sätt att hantera åtkomst till resurser i en prenumeration med hjälp av rollbaserad åtkomst kontroll (RBAC). Information om hur du skapar grupper finns [i skapa en grupp i Azure Active Directory för hands version](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Du kan också skapa och hantera grupper med [hjälp av PowerShell](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Hantera dina prenumerationer

En prenumeration är en logisk gruppering av Azure-tjänster som är länkad till ett Azure-konto. Ett enda Azure-konto kan innehålla flera prenumerationer. Faktureringen för Azure-tjänster görs per prenumeration. En lista över tillgängliga prenumerations erbjudanden per typ finns i [Microsoft Azure erbjudande information](https://azure.microsoft.com/support/legal/offer-details/). Azure-prenumerationer har en konto administratör som har fullständig kontroll över prenumerationen. De har också en tjänst administratör som har kontroll över alla tjänster i prenumerationen. Information om klassiska prenumerations administratörer finns i [lägga till eller ändra Azure-prenumerations administratörer](../../cost-management-billing/manage/add-change-subscription-administrator.md). Enskilda konton kan beviljas detaljerad kontroll över Azure-resurser med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Resursgrupper

När du etablerar nya Azure-tjänster gör du det i en specifik prenumeration. Enskilda Azure-tjänster, som även kallas resurser, skapas i kontexten för en resurs grupp. Resurs grupper gör det enklare att distribuera och hantera dina programs resurser. En resurs grupp ska innehålla alla resurser för ditt program som du vill arbeta med som en enhet. Du kan flytta resurser mellan resurs grupper och till och med olika prenumerationer. Mer information om hur du flyttar resurser finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Resource Explorer är ett bra verktyg för visualisering av de resurser som du redan har skapat i din prenumeration. Mer information finns i [använda Azure Resource Explorer för att visa och ändra resurser](/rest/api/).

#### <a name="grant-access-to-resources"></a>Bevilja åtkomst till resurser

När du tillåter åtkomst till Azure-resurser är det alltid en bra idé att ge användarna den minsta behörighet som krävs för att utföra en specifik uppgift.

* **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)**: i Azure kan du bevilja åtkomst till användar konton (huvud konton) i en angiven omfattning: prenumeration, resurs grupp eller enskilda resurser. Med Azure RBAC kan du distribuera resurser till en resurs grupp och bevilja behörigheter till en speciell användare eller grupp. Du kan också begränsa åtkomsten till de resurser som tillhör mål resurs gruppen. Du kan också bevilja åtkomst till en enskild resurs, till exempel en virtuell dator eller ett virtuellt nätverk. Om du vill bevilja åtkomst tilldelar du en roll till användaren, gruppen eller tjänstens huvud namn. Det finns många fördefinierade roller och du kan också definiera egna anpassade roller. Mer information finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md).

  > **När du ska använda**: när du behöver detaljerade åtkomst hantering för användare och grupper eller när du behöver göra en användare till ägare av en prenumeration.
  >
  > **Kom igång**: Mer information finns i [lägga till eller ta bort Azure Role-tilldelningar med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- **Tjänst huvud objekt**: tillsammans med ger åtkomst till användarens huvud namn och grupper kan du ge samma åtkomst till ett huvud namn för tjänsten.

  > **När du ska använda**: när du program mässigt hanterar Azure-resurser eller beviljar åtkomst för program. Mer information finns i [skapa Active Directory-program och tjänstens huvud namn](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Taggar

Med Azure Resource Manager kan du tilldela anpassade taggar till enskilda resurser. Taggar, som är nyckel/värde-par, kan vara användbara när du behöver organisera resurser för fakturering eller övervakning. Taggar ger dig ett sätt att spåra resurser över flera resurs grupper. Du kan tilldela taggar på följande sätt:

* I portalen
* I Azure Resource Manager mall
* Använda REST API
* Använda Azure CLI
* Använda PowerShell

Du kan tilldela flera taggar till varje resurs. Mer information finns i [använda taggar för att ordna dina Azure-resurser](../../azure-resource-manager/management/tag-resources.md).

### <a name="billing"></a>Fakturering

I flytten från lokal data behandling till molnbaserade tjänster är det viktigt att spåra och uppskatta tjänst användning och relaterade kostnader. Det är viktigt att uppskatta vilka nya resurser som kostar att köras per månad. Du kan också projicera hur faktureringen ser ut för en månad baserat på de aktuella utgifterna.

#### <a name="get-resource-usage-data"></a>Hämta resursanvändnings data

Azure tillhandahåller en uppsättning fakturerings REST-API: er som ger åtkomst till resursförbrukning och metadatainformation för Azure-prenumerationer. Dessa API:er för fakturering ger dig möjlighet att förutse och hantera Azure-kostnader. Du kan spåra och analysera utgifter i steg om varje timme och skapa utgifts aviseringar. Du kan också förutsäga framtida fakturering baserat på aktuella användnings trender.

>**Kom igång**: Mer information om hur du använder API:er för fakturering finns i [Översikt över Azure FÖRBRUKNINGs-API](../../cost-management-billing/manage/consumption-api-overview.md)

#### <a name="predict-future-costs"></a>Förutsäg framtida kostnader

Även om det är svårt att uppskatta kostnader i förväg, har Azure verktyg som kan hjälpa dig. Den har en [pris kalkylator](https://azure.microsoft.com/pricing/calculator/) som hjälper till att beräkna kostnaden för distribuerade resurser. Du kan också använda fakturerings resurserna i portalen och fakturerings REST-API: erna för att beräkna framtida kostnader baserat på aktuell förbrukning.

>**Kom igång**: se [Översikt över Azure förbruknings-API](../../cost-management-billing/manage/consumption-api-overview.md).