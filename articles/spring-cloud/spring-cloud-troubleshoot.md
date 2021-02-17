---
title: Fel söknings guide för Azure våren Cloud | Microsoft Docs
description: Fel söknings guide för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 9350b5dbcedd2773e94ef9d35cfe26f97fbd97bc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571603"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Felsök vanliga problem med Azure våren Cloud

Den här artikeln innehåller anvisningar för fel sökning av problem med moln utveckling i Azure våren. Mer information finns i [vanliga frågor och svar om Azure våren Cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Tillgänglighets-, prestanda-och program problem

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Det går inte att starta programmet (till exempel slut punkten kan inte anslutas, eller så returnerar den en 502 efter några återförsök)

Exportera loggarna till Azure Log Analytics. Tabellen för program loggar med våren heter *AppPlatformLogsforSpring*. Mer information finns i [Analysera loggar och mått med diagnostikinställningar](diagnostic-services.md).

Följande fel meddelande kan visas i loggarna:

> "org. springframework. context. ApplicationContextException: det gick inte att starta webb server"

Meddelandet anger ett av två sannolika problem: 
* Något av bönorna eller något av dess beroenden saknas.
* En av egenskaperna för bönor saknas eller är ogiltig. I det här fallet kommer "Java. lang. IllegalArgumentException" troligen att visas.

Tjänst bindningar kan också orsaka fel i program starten. Använd nyckelord som är relaterade till de bundna tjänsterna för att fråga loggarna. Anta till exempel att ditt program har en bindning till en MySQL-instans som är inställd på lokal system tid. Om programmet inte startar kan följande fel meddelande visas i loggen:

> "Java. SQL. SQLException: Server tids zonens värde ' Coordinated Universal Time ' är okänt eller representerar mer än en tidszon."

Åtgärda felet genom att gå till `server parameters` MySQL-instansen och ändra `time_zone` värdet från *systemet* till *+ 0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Mina program kraschar eller genererar ett oväntat fel

När du felsöker program krascher startar du genom att kontrol lera programmets körnings status och identifierings status. Det gör du genom att gå till _app Management_ i Azure Portal för att se till att status för alla program _körs_ _och är igång._

* Om status är _igång_ men identifierings statusen inte är _upp_ går du till avsnittet ["det går inte att registrera programmet"](#my-application-cant-be-registered) .

* Om identifierings statusen är _upp_ går du till mått för att kontrol lera programmets hälso tillstånd. Kontrol lera följande mått:


  - `TomcatErrorCount` (_Tomcat. global. error_): alla våren Application-undantag räknas här. Om det här talet är stort går du till Azure Log Analytics för att kontrol lera program loggarna.

  - `AppMemoryMax` (_JVM. Memory. Max_): den maximala mängden minne som är tillgängligt för programmet. Beloppet kan vara odefinierat, eller så kan det ändras med tiden om det har definierats. Om den har definierats, är mängden använt och allokerat minne alltid mindre än eller lika med max. Men en minnesallokering kan Miss lyckas med ett `OutOfMemoryError` meddelande om tilldelningen försöker öka det använda minnet som *används > dedikerat*, även om det *används <= Max* är sant. I sådana fall kan du försöka öka den maximala heap-storleken genom att använda- `-Xmx` parametern.

  - `AppMemoryUsed` (_JVM. Memory. används_): mängden minne i byte som för närvarande används av programmet. För en normal inläsning av Java-program utgör den här mått serien ett *sågtandade* -mönster där minnes användningen ständigt ökar och minskar i små steg och plötsligt släpper ett parti, och sedan upprepas mönstret. Den här Mät serien inträffar på grund av skräp insamling i Java Virtual Machine, där samlings åtgärder representerar droppar i sågtandade-mönstret.
    
    Det här måttet är viktigt för att hjälpa till att identifiera minnes problem, till exempel:
    * En minnes nedbrytning i början.
    * Överspännings minnes tilldelning för en speciell logik Sök väg.
    * Gradvisa minnes läckor.
  Mer information finns i [mått](spring-cloud-concept-metrics.md).
  
* Om programmet inte kan starta kontrollerar du att programmet har giltiga JVM-parametrar. Om JVM-minnet är för högt kan följande fel meddelande visas i loggarna:

  >"nödvändigt minne 2728741K är större än 2000 tillgängligt för tilldelning"



Mer information om Azure Log Analytics finns [i kom igång med Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Mitt program använder stora mängder CPU- eller minnesresurser

Om ditt program upplever hög processor-eller minnes användning är något av två saker sanna:
* Alla App-instanser upplever hög processor-eller minnes användning.
* Några av app-instanserna upplever hög processor-eller minnes användning.

För att fastställa vilken situation som gäller gör du följande:

1. Gå till **mått** och välj antingen procent eller **tjänst minne som används** för **tjänstens processor användning** .
2. Lägg till ett **app =** filter för att ange vilket program som du vill övervaka.
3. Dela måtten efter **instans**.

Om *alla instanser* har hög processor-eller minnes användning måste du skala ut programmet eller skala upp processor-eller minnes användningen. Mer information finns i [Självstudier: skala ett program i Azure våren Cloud](spring-cloud-tutorial-scale-manual.md).

Om *vissa instanser* har hög processor-eller minnes användning kontrollerar du instans statusen och dess identifierings status.

Mer information finns i [mått för Azure våren Cloud](spring-cloud-concept-metrics.md).

Om alla instanser är igång går du till Azure Log Analytics för att skicka frågor till program loggarna och granska din kod logik. Detta hjälper dig att se om någon av dem kan påverka skalningen av skala. Mer information finns i [Analysera loggar och mått med diagnostikinställningar](diagnostic-services.md).

Mer information om Azure Log Analytics finns [i kom igång med Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md). Fråga loggarna genom att använda [Kusto-frågespråket](/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Check lista för att distribuera ditt våren-program till Azure våren Cloud

Innan du registrerar ditt program bör du kontrol lera att det uppfyller följande kriterier:

* Programmet kan köras lokalt med den angivna Java Runtime-versionen.
* Miljö konfigurationen (CPU/RAM/instanser) uppfyller minimi kravet som angetts av program leverantören.
* Konfigurations objekt har förväntade värden. Mer information finns i [konfigurations Server](spring-cloud-tutorial-config-server.md).
* Miljövariablerna har sina förväntade värden.
* JVM-parametrarna har sina förväntade värden.
* Vi rekommenderar att du inaktiverar eller tar bort den inbäddade _konfigurations servern_ och _tjänst registrerings_ tjänsterna från programpaketet.
* Om några Azure-resurser ska bindas via _tjänstbindning_ kontrollerar du att målresurserna är igång.

## <a name="configuration-and-management"></a>Konfiguration och hantering

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Jag har stött på ett problem med att skapa en Azure våren Cloud Service-instans

När du konfigurerar en Azure våren Cloud Service-instans med hjälp av Azure Portal, utför Azure våren Cloud verifieringen åt dig.

Men om du försöker konfigurera Azure våren Cloud Service-instansen med hjälp av [Azure CLI](/cli/azure/get-started-with-azure-cli) eller [Azure Resource Manager mall](../azure-resource-manager/index.yml), kontrollerar du att:

* Prenumerationen är aktiv.
* Platsen [stöds](spring-cloud-faq.md) av Azure våren Cloud.
* Resurs gruppen för instansen har redan skapats.
* Resurs namnet följer namngivnings regeln. Det får bara innehålla gemena bokstäver, siffror och bindestreck. Det första tecknet måste vara en bokstav. Det sista tecknet måste vara en bokstav eller en siffra. Värdet måste innehålla mellan 2 och 32 tecken.

Om du vill konfigurera Azure våren Cloud Service-instansen med hjälp av Resource Manager-mallen, se först till att [förstå strukturen och syntaxen för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md).

Namnet på Azure våren Cloud Service-instansen kommer att användas för att begära ett under domän namn under `azureapps.io` , så installationen Miss kommer att Miss förväntas om namnet står i konflikt med ett befintligt namn. Du kan hitta mer information i aktivitets loggarna.

### <a name="i-cant-deploy-a-net-core-app"></a>Jag kan inte distribuera en .NET Core-app

Du kan inte ladda upp en *zip* -fil för en .net Core Steeltoe-app med hjälp av Azure Portal eller Resource Manager-mallen.

När du distribuerar programpaketet med hjälp av [Azure CLI](/cli/azure/get-started-with-azure-cli)avsöker Azure CLI regelbundet distributions förloppet och i slutet visas distributions resultatet.

Kontrol lera att programmet är paketerat i rätt *. zip* -filformat. Om den inte paketeras korrekt kommer processen att sluta svara eller så visas ett fel meddelande.

### <a name="i-cant-deploy-a-jar-package"></a>Jag kan inte distribuera ett JAR-paket

Du kan inte ladda upp/source-paketet för Java Archive File (JAR) med hjälp av Azure Portal eller Resource Manager-mallen.

När du distribuerar programpaketet med hjälp av [Azure CLI](/cli/azure/get-started-with-azure-cli)avsöker Azure CLI regelbundet distributions förloppet och i slutet visas distributions resultatet.

Om avsökningen avbryts kan du fortfarande använda följande kommando för att hämta distributionsloggarna:

`az spring-cloud app show-deploy-log -n <app-name>`

Se till att programmet är paketerat i rätt [format för JAR-format](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Om den inte är korrekt paketerad visas ett fel meddelande som liknar följande:

> "Fel: ogiltig eller skadad jarfile-/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Jag kan inte distribuera ett käll paket

Du kan inte överföra JAR/source-paket med hjälp av Azure Portal eller Resource Manager-mallen.

När du distribuerar programpaketet med hjälp av [Azure CLI](/cli/azure/get-started-with-azure-cli)avsöker Azure CLI regelbundet distributions förloppet och i slutet visas distributions resultatet.

Om avsökningen avbryts kan du fortfarande använda följande kommando för att hämta bygg- och distributionsloggarna:

`az spring-cloud app show-deploy-log -n <app-name>`

Observera dock att en Azure våren Cloud Service-instans bara kan utlösa ett build-jobb för ett käll paket på en gång. Mer information finns i [distribuera ett program](spring-cloud-quickstart.md) och [Konfigurera en utvecklings miljö i Azure våren Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Det går inte att registrera mitt program

I de flesta fall uppstår den här situationen när *nödvändiga beroenden* och *tjänst identifiering* inte har kon figurer ATS korrekt i din Pom-fil (Project Object Model). När den har kon figurer ATS matas den inbyggda slut punkten för tjänst register servern in som en miljö variabel med ditt program. Programmen registrerar sig sedan med tjänstens register Server och identifierar andra beroende mikrotjänster.

Vänta minst två minuter innan en nyligen registrerad instans börjar ta emot trafik.

Om du migrerar en befintlig molnbaserade lösning till Azure måste du se till att dina Ad hoc- _tjänsteregister_ och _konfigurations Server_ instanser tas bort (eller inaktiverade) för att undvika konflikter med de hanterade instanser som tillhandahålls av Azure våren Cloud.

Du kan också kontrol lera klient loggarna för _tjänst registret_ i Azure Log Analytics. Mer information finns i [Analysera loggar och mått med diagnostikinställningar](diagnostic-services.md)

Mer information om Azure Log Analytics finns [i kom igång med Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md). Fråga loggarna genom att använda [Kusto-frågespråket](/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Jag vill kontrol lera miljö variabler för mitt program

Miljövariabler meddelar moln ramverket för Azure våren att se till att Azure förstår var och hur du konfigurerar de tjänster som utgör ditt program. Att se till att dina miljövariabler är korrekta är ett nödvändigt första steg i fel sökning av eventuella problem.  Du kan använda den fjädrande start manövrerings punkten för att granska miljövariablerna.  

> [!WARNING]
> Den här proceduren visar dina miljövariabler genom att använda test slut punkten.  Fortsätt inte om din test-slutpunkt är offentligt tillgänglig eller om du har tilldelat ett domän namn till ditt program.

1. Gå till `https://<your application test endpoint>/actuator/health`.  
    - Ett svar som liknar `{"status":"UP"}` anger att slut punkten har Aktiver ATS.
    - Om svaret är negativt inkluderar du följande beroende i *POM.xml* -filen:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. När Start punktens slut punkt är aktive rad går du till Azure Portal och letar efter konfigurations sidan för ditt program.  Lägg till en miljö variabel med namnet `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` och värdet `*` . 

1. Starta om programmet.

1. Gå till `https://<your application test endpoint>/actuator/env` och kontrol lera svaret.  Den bör se ut så här:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Leta upp den underordnade noden med namnet `systemEnvironment` .  Den här noden innehåller programmets miljövariabler.

> [!IMPORTANT]
> Kom ihåg att återställa miljö variablernas exponering innan du gör programmet tillgängligt för allmänheten.  Gå till Azure Portal, leta upp sidan konfiguration i programmet och ta bort den här miljövariabeln:  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Jag kan inte hitta mått eller loggar för mitt program

Gå till **app Management** för att se till att programmets status _körs_ _och är igång._

Kontrol lera att väder _JMX_ har Aktiver ATS i programpaketet. Den här funktionen kan aktive ras med konfigurations egenskapen `spring.jmx.enabled=true` .  

Kontrol lera om `spring-boot-actuator` beroendet är aktiverat i ditt programpaket och att det har startats.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Om dina program loggar kan arkiveras till ett lagrings konto men inte skickas till Azure Log Analytics, kontrollerar du om du [har konfigurerat arbets ytan på rätt sätt](../azure-monitor/logs/quick-create-workspace.md). Om du använder en kostnads fri nivå av Azure Log Analytics, Observera att [den kostnads fria nivån inte tillhandahåller något service nivå avtal (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).

## <a name="next-steps"></a>Nästa steg

* [Så här identifierar och löser du problem i Azure våren Cloud](spring-cloud-howto-self-diagnose-solve.md)