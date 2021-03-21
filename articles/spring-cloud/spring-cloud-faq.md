---
title: Vanliga frågor och svar om Azure våren Cloud | Microsoft Docs
description: I den här artikeln får du svar på vanliga frågor om Azure våren Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 50c8a36b943abb24f2c31bfc2bc33e646a74524a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596622"
---
# <a name="azure-spring-cloud-faq"></a>Vanliga frågor och svar om Azure våren Cloud

I den här artikeln får du svar på vanliga frågor om Azure våren Cloud.

## <a name="general"></a>Allmänt

### <a name="why-azure-spring-cloud"></a>Varför Azure våren Cloud?

Azure våren Cloud tillhandahåller en PaaS (Platform as a Service) för vår moln utvecklare. Azure våren Cloud hanterar din program infrastruktur så att du kan fokusera på program kod och affärs logik. Kärn funktioner som är inbyggda i Azure våren Cloud inkluderar Eureka, konfigurations Server, tjänst register Server, pivotal build-tjänst, blå-grön distribution med mera. Den här tjänsten gör det också möjligt för utvecklare att binda sina program till andra Azure-tjänster, till exempel Azure Cosmos DB, Azure Database for MySQL och Azure cache för Redis.

Azure våren Cloud förbättrar programdiagnostikens upplevelse för utvecklare och operatörer genom att integrera Azure Monitor, Application Insights och Log Analytics.

### <a name="how-secure-is-azure-spring-cloud"></a>Hur säkert är Azure våren Cloud?

Säkerhet och sekretess är bland de viktigaste prioriteringarna för Azure-och Azure våren Cloud-kunder. Azure hjälper till att säkerställa att endast kunder har åtkomst till program data, loggar eller konfigurationer genom att kryptera alla dessa data på ett säkert sätt. 

* Tjänst instanserna i Azure våren Cloud är isolerade från varandra.
* Azure våren Cloud tillhandahåller fullständig TLS/SSL och certifikat hantering.
* Viktiga säkerhets uppdateringar för OpenJDK och våren Cloud runtime används i Azure våren Cloud så snart som möjligt.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>I vilka regioner är Azure våren Cloud tillgängligt?

Östra USA, östra USA 2, centrala USA, södra centrala USA, norra centrala USA, västra USA, västra USA 2, västra Europa, norra Europa, Storbritannien, södra, Sydostasien, östra Australien, centrala Kanada, Nord Förenade Arabemiraten, centrala Indien, Korea, centrala, Asien, östra och Kina, östra 2 (Mooncake). [Läs mer](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>Lagras alla kund data utanför den angivna regionen?

Azure våren Cloud är en regional tjänst. All kund information i Azure våren-molnet lagras i flera regioner inom samma geografiska område för redundans. Mer information om geo och region finns i [data placering i Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Vilka är de kända begränsningarna i Azure våren Cloud?

Azure våren Cloud har följande kända begränsningar:
    
* `spring.application.name` kommer att åsidosättas av det program namn som används för att skapa varje program.
* `server.port` Standardvärdet är port 1025. Om något annat värde används kommer det att åsidosättas. Respektera även den här inställningen och ange inte Server porten i koden.
* Azure Portal-och Azure Resource Manager-mallarna stöder inte överföring av programpaket. Du kan bara överföra program paket genom att distribuera programmet via Azure CLI.

### <a name="what-pricing-tiers-are-available"></a>Vilka pris nivåer är tillgängliga? 
Vilken av dem ska jag använda och vilka är gränserna inom varje nivå?
* Azure våren Cloud erbjuder två pris nivåer: Basic och standard. Basic-nivån är avsedd för utveckling/testning och testar Azure våren Cloud. Standard nivån är optimerad för att köra generell användning av produktions trafiken. Se [pris information för Azure våren Cloud](https://azure.microsoft.com/pricing/details/spring-cloud/) för gränser och jämförelse av funktions nivåer.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hur kan jag ge feedback och rapportera problem?

Om du stöter på problem med Azure våren Cloud kan du skapa en [support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Om du vill skicka en funktions förfrågan eller lämna feedback går du till [Azure feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Utveckling

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jag är en moln utvecklare som är våren men som är ny i Azure. Vad är det snabbaste sättet för mig att lära sig hur man utvecklar ett Azure våren Cloud-program?

För det snabbaste sättet att komma igång med Azure våren Cloud följer du anvisningarna i [snabb start: starta ett Azure våren Cloud-program med hjälp av Azure Portal](spring-cloud-quickstart.md).

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Vilken Java-körning stöder Azure våren Cloud?

Azure våren Cloud har stöd för Java 8 och 11. Se [Java Runtime och OS-versioner](#java-runtime-and-os-versions)

### <a name="is-spring-boot-24x-supported"></a>Finns det stöd för våren boot 2.4. x?
Vi har identifierat ett problem med våren boot 2,4 och arbetar för närvarande med vår våren för att lösa problemet. Under tiden kan du inkludera dessa två beroenden för att aktivera TLS-autentisering mellan dina appar och Eureka.

```xml
<dependency> 
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Var kan jag se mina vår moln program loggar och mått?

Hitta mått på fliken Översikt över appen och fliken [Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer) .

Azure våren Cloud stöder export av vår moln program loggar och mät värden till Azure Storage, EventHub och [Log Analytics](../azure-monitor/logs/data-platform-logs.md). Tabell namnet i Log Analytics är *AppPlatformLogsforSpring*. Information om hur du aktiverar det finns i [Diagnostic Services](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Stöder Azure våren Cloud Distributed tracing?

Ja. Mer information finns i [Självstudier: använda distribuerad spårning med Azure våren Cloud](spring-cloud-tutorial-distributed-tracing.md).

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Vilka resurs typer stöder tjänst bindning?

Tre tjänster stöds för närvarande:
* Azure Cosmos DB
* Azure Database for MySQL
* Azure cache för Redis.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Kan jag visa, lägga till eller flytta beständiga volymer inifrån mina program?

Ja.

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Hur många utgående offentliga IP-adresser har en Azure våren-moln instans?

Antalet utgående offentliga IP-adresser kan variera beroende på nivåerna och andra faktorer. 

| Instans typ för Azure våren Cloud | Standard antal utgående offentliga IP-adresser |
| -------------------------------- | ---------------------------------------------- |
| Basic-nivå instanser             | 1                                              |
| Instans på standard nivå          | 2                                              |
| Instanser av VNet-injektering         | 1                                              |


### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>Kan jag öka antalet utgående offentliga IP-adresser?

Ja, du kan öppna ett [support ärende](https://azure.microsoft.com/support/faq/)  för att begära fler utgående offentliga IP-adresser.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>När jag tar bort/flyttar en Azure våren Cloud-tjänstinstans, kommer dess tilläggs resurser att tas bort/flyttas också?

Det beror på logiken för de resurs leverantörer som äger tilläggs resurserna. Tilläggs resurserna för en `Microsoft.AppPlatform` instans tillhör inte samma namnrymd, så beteendet varierar av resurs leverantören. Till exempel överlappar inte åtgärden ta bort/flytta till resurserna för **diagnostikinställningar** . Om en ny Azure våren Cloud-instans är etablerad med samma resurs-ID som den borttagna, eller om den tidigare Azure våren-moln instansen flyttas tillbaka, fortsätter de tidigare **inställningarna för diagnostikinställningar** att utökas.

Du kan ta bort de diagnostiska inställningarna för våren-molnet med hjälp av Azure CLI:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Java Runtime och OS-versioner

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Vilka versioner av Java Runtime stöds i Azure våren Cloud?

Azure våren Cloud har stöd för Java LTS-versioner med de senaste build-versionerna, för närvarande 2020, Java 8 och Java 11. Se [installera JDK för Azure och Azure Stack](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Vem har skapat dessa Java-körningar?

Azul-system. Azul Zulu för Azure – Enterprise Edition JDK-versioner är en kostnadsfri produktionsklar distribution med flera plattformar av OpenJDK för Azure och Azure Stack som backas upp av Microsoft- och Azul-system. De innehåller alla komponenter för att skapa och köra Java SE-program.

### <a name="how-often-will-java-runtimes-get-updated"></a>Hur ofta kommer Java-körningar att uppdateras?

LTS-och MTS JDK-versioner har kvartals Visa säkerhets uppdateringar, fel korrigeringar och viktiga out-of-band-uppdateringar och korrigerings program efter behov. Det här stödet omfattar backports till Java 7 och 8 säkerhets uppdateringar och fel korrigeringar som rapporteras i senare versioner av Java, som Java 11.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Hur länge kommer Java 8-och Java 11 LTS-versioner att stödjas?

Se [stöd för Java-långtids för Azure och Azure Stack](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Java 8-LTS kommer att stödjas till och med 2030 december.
* Java 11-LTS kommer att stödjas fram till den 2027 september.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Hur kan jag hämta en Java-körning som stöds för lokal utveckling?

Se [installera JDK för Azure och Azure Stack](/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Vad är borttagnings principen för äldre Java-körningar?

Offentligt meddelande kommer att skickas till 12 månader innan en gammal körnings version dras tillbaka. Du kommer att ha 12 månader för att migrera till en senare version.

* Prenumerations administratörer får ett e-postmeddelande när vi kommer att dra tillbaka en Java-version.
* Informationen om indragningen kommer att publiceras i dokumentationen.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Hur kan jag få support för problem på Java Runtime-nivån?

Du kan öppna ett support ärende med Azure-supporten.  Se [så här skapar du en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Vad är drift systemet för att köra Mina appar?

Den senaste versionen av Ubuntu LTS används, för närvarande [Ubuntu 20,04 LTS (fokus fossa)](https://releases.ubuntu.com/focal/) är standard operativ systemet.

### <a name="how-often-are-os-security-patches-applied"></a>Hur ofta tillämpas säkerhets korrigeringar för operativ systemet?

Säkerhets korrigeringar som gäller Azure våren Cloud distribueras till produktion varje månad.
Viktiga säkerhets korrigeringar (CVE Poäng >= 9) som är tillämpliga på Azure våren Cloud lanseras så snart som möjligt.
::: zone-end

## <a name="deployment"></a>Distribution

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Stöder Azure våren Cloud blå-grön distribution?
Ja. Mer information finns i [Konfigurera en mellanlagrings miljö](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan jag få åtkomst till Kubernetes för att ändra mina program behållare?

Nej.  Azure våren Cloud är en sammanfattning av utvecklaren från den underliggande arkitekturen, så att du kan koncentrera dig på program kod och affärs logik.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Har Azure våren Cloud stöd för att skapa behållare från källa?

Ja. Mer information finns i [starta ditt våren Cloud-program från käll koden](spring-cloud-quickstart.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Stöder Azure våren Cloud automatisk skalning i App-instanser?

Ja.  Mer information finns i [Konfigurera autoskalning](spring-cloud-tutorial-setup-autoscale.md).

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Vilka är bästa praxis för att migrera befintliga våren Cloud-mikrotjänster till Azure våren Cloud?

När du migrerar befintliga våren Cloud-mikrotjänster till Azure våren-molnet är det en bra idé att Observera följande bästa praxis:
* Alla program beroenden måste lösas.
* Förbered dina konfigurations poster, miljövariabler och JVM-parametrar så att du kan jämföra dem med distributionen i Azure våren Cloud.
* Om du vill använda tjänst bindningen går du igenom dina Azure-tjänster och kontrollerar att du har angett rätt åtkomst behörighet.
* Vi rekommenderar att du tar bort eller inaktiverar inbäddade tjänster som kan vara i konflikt med tjänster som hanteras av Azure våren Cloud, till exempel tjänst identifierings tjänsten, konfigurations servern och så vidare.
* Vi rekommenderar att du använder officiella, stabila, vridnings fjäder-bibliotek. Icke-officiella, beta-eller förgrenings versioner av pivotal våren-bibliotek har inget stöd för service avtal (SLA).

Efter migreringen övervakar du dina CPU/RAM-mått och nätverks trafik för att säkerställa att program instanserna skalas på rätt sätt.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core-versioner

### <a name="which-net-core-versions-are-supported"></a>Vilka .NET Core-versioner stöds?

.NET Core 3,1 och senare versioner.

### <a name="how-long-will-net-core-31-be-supported"></a>Hur lång tid kommer .NET Core 3,1 att stödjas?

Till dec 3, 2022. Se [.net Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Felsökning

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Vilka konsekvenser för tjänst registret är sällan otillgängliga?

I vissa scenarier som sällan inträffade kan du se vissa fel som 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
från dina program loggar. Det här problemet introducerades av våren-ramverket med mycket låg taxa på grund av instabilt nätverk eller andra nätverks problem. 

Det bör inte uppstå någon påverkan på användar upplevelsen, Eureka-klienten har både pulsslags-och återförsöks princip för att ta hand om detta. Du kan betrakta det som ett tillfälligt fel och hoppa över det på ett säkert sätt.

Vi kommer att förbättra den här delen och undvika det här felet från användarnas program i kort framtid.


## <a name="next-steps"></a>Nästa steg

Om du har fler frågor kan du läsa [fel söknings guiden för Azure våren Cloud](spring-cloud-troubleshoot.md).
