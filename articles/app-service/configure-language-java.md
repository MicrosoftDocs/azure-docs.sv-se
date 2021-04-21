---
title: Konfigurera Java-appar
description: Lär dig hur du konfigurerar Java-appar så att de körs Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
keywords: azure app service, web app, windows, oss, java, tomcat, jboss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 134ac04c4f6fb5f0e38a868adc735fc816fbc875
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829520"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Konfigurera en Java-app för Azure App Service

Azure App Service Java-utvecklare att snabbt skapa, distribuera och skala sina Java SE-, Tomcat- och JBoss EAP-webbprogram på en fullständigt hanterad tjänst. Distribuera program med Maven-plugin-program, från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder App Service. Om du aldrig har använt Azure App Service bör du läsa igenom [Java-snabbstarten](quickstart-java.md) först. Allmänna frågor om användning App Service som inte är specifika för Java-utveckling besvaras i vanliga [frågor App Service vanliga frågor och svar.](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [Plugin-programmet Azure Web App för Maven för](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) att distribuera dina WAR- eller .jar-filer. Distribution med populära IDE:er stöds också med [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) eller [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Annars beror distributionsmetoden på din arkivtyp:

### <a name="java-se"></a>Java SE

Om du vill distribuera .jar-filer till Java SE använder `/api/zipdeploy/` du kudu-webbplatsens slutpunkt. Mer information om det här API:et finns i [den här dokumentationen.](./deploy-zip.md#rest) 

> [!NOTE]
>  Ditt .jar-program måste `app.jar` namnges för App Service för att identifiera och köra ditt program. Maven-plugin-programmet (nämns ovan) byter automatiskt namn på ditt program åt dig under distributionen. Om du inte vill byta namn på jar-filen till *app.jar* kan du ladda upp ett kommandoskript med kommandot för att köra .jar-appen. Klistra in den absoluta sökvägen till skriptet [i textrutan Startfil](faq-app-service-linux.md#built-in-images) i avsnittet Konfiguration i portalen. Startskriptet körs inte från den katalog där den placeras. Använd därför alltid absoluta sökvägar för att referera till filer i startskriptet (till exempel: `java -jar /home/myapp/myapp.jar`).

### <a name="tomcat"></a>Tomcat

Om du vill distribuera WAR-filer till Tomcat använder du `/api/wardeploy/` slutpunkten för att PUBLICERA din arkivfil. Mer information om detta API finns i den [här dokumentationen.](./deploy-zip.md#deploy-war-file)

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss-EAP

Om du vill distribuera WAR-filer till JBoss använder du `/api/wardeploy/` slutpunkten för att PUBLICERA din arkivfil. Mer information om detta API finns i den [här dokumentationen.](./deploy-zip.md#deploy-war-file)

Om du vill distribuera .ear-filer [använder du FTP](deploy-ftp.md).

::: zone-end

Distribuera inte .war eller .jar med FTP. FTP-verktyget är utformat för att ladda upp startskript, beroenden eller andra körningsfiler. Det är inte det optimala valet för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och felsöka appar

Prestandarapporter, trafikvisualiseringar och hälsokontroll är tillgängliga för varje app via Azure Portal. Mer information finns i Azure App Service [diagnostiköversikt.](overview-diagnostics.md)

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Mer information finns i [Stream-loggar i Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH-konsolåtkomst

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Felsökningsverktyg

De inbyggda Java-avbildningarna baseras på Alpine Linux-operativsystemet. [](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Använd `apk` pakethanteraren för att installera felsökningsverktyg eller -kommandon.

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

Alla Java-körningar på App Service med Azul JVM kommer med Zulu Flight Recorder. Du kan använda det här för att registrera JVM-, system- och programhändelser och felsöka problem i Java-program.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Timed Recording

För att kunna göra en inspelning som tar tid behöver du PID (process-ID) för Java-programmet. Du hittar PID genom att öppna en webbläsare till webbappens SCM-webbplats på https://<ditt-webbplatsnamn>.scm.azurewebsites.net/ProcessExplorer/. Den här sidan visar de processer som körs i webbappen. Hitta processen med namnet "java" i tabellen och kopiera motsvarande PID (process-ID).

Öppna sedan **felsökningskonsolen i** det översta verktygsfältet på SCM-webbplatsen och kör följande kommando. Ersätt `<pid>` med det process-ID som du kopierade tidigare. Det här kommandot startar en 30-sekunders profilerarinspelning av Ditt Java-program och genererar en fil `timed_recording_example.jfr` med namnet i katalogen `D:\home` .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

SSH i din App Service och kör kommandot `jcmd` för att se en lista över alla Java-processer som körs. Förutom själva jcmd bör du se att ditt Java-program körs med ett process-ID-nummer (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Kör kommandot nedan för att starta en 30-sekundersinspelning av JVM. Detta profilerar JVM och skapar en JFR-fil *med namnet jfr_example.jfr* i arbetskatalogen. (Ersätt 116 med pid för din Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Under 30-sekundersintervallet kan du verifiera att inspelningen äger rum genom att köra `jcmd 116 JFR.check` . Då visas alla inspelningar för den angivna Java-processen.

#### <a name="continuous-recording"></a>Kontinuerlig registrering

Du kan använda Zulu Flight Recorder för att kontinuerligt profilera ditt Java-program med minimal påverkan på körningsprestanda[(källa).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Det gör du genom att köra följande Azure CLI-kommando för att skapa en appinställning med JAVA_OPTS med den konfiguration som krävs. Innehållet i den JAVA_OPTS appinställningen skickas till `java` kommandot när din app startas.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

När inspelningen har startats kan du när som helst dumpa aktuella inspelningsdata med hjälp av `JFR.dump` kommandot .

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Analysera `.jfr` filer

Använd [FTPS för](deploy-ftp.md) att ladda ned JFR-filen till den lokala datorn. Om du vill analysera JFR-filen laddar du ned och installerar [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Anvisningar om Zulu Mission Control finns i [Azul-dokumentationen och](https://docs.azul.com/zmc/) [installationsanvisningarna.](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="app-logging"></a>Apploggning

::: zone pivot="platform-windows"

Aktivera [programloggning](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config) för att konfigurera App Service att skriva programmets standardkonsolutdata och standardkonsolfelströmmar till det lokala filsystemet eller Azure Blob Storage. Loggning till den lokala App Service-filsysteminstansen inaktiveras 12 timmar efter att den har konfigurerats. Om du behöver längre kvarhållning konfigurerar du programmet att skriva utdata till en Blob Storage-container. Dina Java- och Tomcat-apploggar finns i *katalogen /home/LogFiles/Application/.*

::: zone-end
::: zone pivot="platform-linux"

Aktivera [programloggning](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config) för att konfigurera App Service att skriva programmets standardkonsolutdata och standardkonsolfelströmmar till det lokala filsystemet eller Azure Blob Storage. Om du behöver längre kvarhållning konfigurerar du programmet att skriva utdata till en Blob Storage-container. Dina Java- och Tomcat-apploggar finns i *katalogen /home/LogFiles/Application/.*

Azure Blob Storage för Linux-baserade App Services kan bara konfigureras med [hjälp av Azure Monitor (förhandsversion)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Om ditt program använder [Logback](https://logback.qos.ch/) eller [Log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra spårningarna för granskning i Azure Application Insights med hjälp av konfigurationsanvisningarna för loggningsramverket i [Utforska Java-spårningsloggar i Application Insights](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux har stöd för direktjustering och anpassning via Azure Portal och CLI. Läs följande artiklar om icke-Java-specifik webbappskonfiguration:

- [Konfigurera appinställningar](configure-common.md#configure-app-settings)
- [Konfigurera en anpassad domän](app-service-web-tutorial-custom-domain.md)
- [Konfigurera SSL-bindningar](configure-ssl-bindings.md)
- [Lägga till ett CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurera Kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Ange alternativ för Java-körning

Om du vill ange allokerat minne eller andra JVM-körningsalternativ skapar du [en appinställning](configure-common.md#configure-app-settings) med `JAVA_OPTS` namnet med alternativen. App Service skickar den här inställningen som en miljövariabel till Java-körningen när den startas.

I Azure Portal under **Programinställningar** för webbappen skapar du en ny appinställning med namnet som innehåller ytterligare `JAVA_OPTS` inställningar, till exempel `-Xms512m -Xmx1204m` .

Om du vill konfigurera appinställningen från Maven-plugin-programmet lägger du till inställnings-/värdetaggar i avsnittet för Azure-plugin-programmet. I följande exempel anges en specifik lägsta och högsta Java-heap-storlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett enda program med ett distributionsfack i App Service plan kan använda följande alternativ:

- B1- och S1-instanser: `-Xms1024m -Xmx1024m`
- B2- och S2-instanser: `-Xms3072m -Xmx3072m`
- B3- och S3-instanser: `-Xms6144m -Xmx6144m`

När du finjusterar programmets heapinställningar granskar du App Service plan information och tar hänsyn till att flera program och distributionsfack måste hitta den optimala allokeringen av minne.

### <a name="turn-on-web-sockets"></a>Aktivera webbsocketar

Aktivera stöd för webbsocketar i Azure Portal i **programinställningarna** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera stöd för webbsocket med hjälp av Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om programmet:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standardteckenkodning

I Azure Portal under **Programinställningar för** webbappen skapar du en ny appinställning med namnet `JAVA_OPTS` med värdet `-Dfile.encoding=UTF-8` .

Du kan också konfigurera appinställningen med hjälp av App Service Maven. Lägg till inställningens namn och värdetaggar i plugin-konfigurationen:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Kompilera JSP-filer i förväg

För att förbättra prestanda för Tomcat-program kan du kompilera dina JSP-filer innan du distribuerar till App Service. Du kan använda [Maven-plugin-programmet](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) som tillhandahålls av Apache Sling eller med den här [Ant-build-filen](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs App Service har samma uppsättning [säkerhetsmetoder](../security/fundamentals/paas-applications-using-app-services.md) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (Easy Auth)

Konfigurera appautentisering i Azure Portal med alternativet **Autentisering och auktorisering.** Därifrån kan du aktivera autentisering med hjälp Azure Active Directory inloggningar eller sociala inloggningar som Facebook, Google eller GitHub. Azure Portal konfigurationen fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i Konfigurera [din App Service för att använda Azure Active Directory inloggning](configure-authentication-provider-aad.md) och relaterade artiklar för andra identitetsproviders. Om du behöver aktivera flera inloggningsproviders följer du anvisningarna i artikeln [anpassa App Service för](app-service-authentication-how-to.md) autentisering.

#### <a name="java-se"></a>Java SE

Spring Boot kan använda Azure Active Directory Spring Boot [starter för](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) att skydda program med välbekanta Anteckningar och API:er för Spring Security. Se till att öka den maximala rubrikstorleken i filen *application.properties.* Vi föreslår värdet `16384` .

#### <a name="tomcat"></a>Tomcat

Ditt Tomcat-program kan komma åt användarens anspråk direkt från servleten genom att ändra huvudobjektet till ett Map-objekt. Map-objektet mappar varje anspråkstyp till en samling anspråk för den typen. I koden nedan är `request` en instans av `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du granska `Map` objektet för ett specifikt anspråk. Följande kodfragment itererar till exempel igenom alla anspråkstyper och skriver ut innehållet i varje samling.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Använd sökvägen för att logga ut `/.auth/ext/logout` användare. Om du vill utföra andra åtgärder kan du läsa dokumentationen om hur [du App Service autentisering och auktorisering.](./app-service-authentication-how-to.md) Det finns också officiell dokumentation om Tomcat [HttpServletRequest-gränssnittet](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servletmetoder är också indelade baserat på din App Service konfiguration:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den här funktionen skapar du en `WEBSITE_AUTH_SKIP_PRINCIPAL` programinställning med namnet med värdet `1` . Om du vill inaktivera alla servletfilter som App Service skapar du en inställning med `WEBSITE_SKIP_FILTERS` namnet med värdet `1` .

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ anvisningarna i Skydda ett anpassat DNS-namn med en [SSL-bindning i Azure App Service](configure-ssl-bindings.md) för att ladda upp ett befintligt SSL-certifikat och binda det till programmets domännamn. Som standard tillåter programmet fortfarande HTTP-anslutningar. Följ de specifika stegen i självstudien för att framtvinga SSL och TLS.

### <a name="use-keyvault-references"></a>Använda KeyVault-referenser

[Azure KeyVault tillhandahåller](../key-vault/general/overview.md) centraliserad hemlighetshantering med åtkomstprinciper och granskningshistorik. Du kan lagra hemligheter (till exempel lösenord eller anslutningssträngar) i KeyVault och komma åt dessa hemligheter i ditt program via miljövariabler.

Följ först anvisningarna för att [ge din app åtkomst till Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och skapa en [KeyVault-referens](app-service-key-vault-references.md#reference-syntax)till din hemlighet i en programinställning . Du kan verifiera att referensen matchar hemligheten genom att skriva ut miljövariabeln vid fjärråtkomst till App Service terminalen.

Om du vill mata in dessa hemligheter i spring- eller Tomcat-konfigurationsfilen använder du syntaxen för miljövariabeln injection ( `${MY_ENV_VAR}` ). För Spring-konfigurationsfiler kan du läsa den här dokumentationen [om externaliserade konfigurationer.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Använda Java Key Store

Som standard läses alla offentliga eller privata [certifikat som överförs till App Service Linux](configure-ssl-certificate.md) in i respektive Java-nyckelarkiv när containern startas. När du har laddat upp certifikatet måste du starta om App Service för att det ska läsas in i Java Key Store. Offentliga certifikat läses in i nyckelarkivet `$JAVA_HOME/jre/lib/security/cacerts` på och privata certifikat lagras i `$JAVA_HOME/lib/security/client.jks` .

Ytterligare konfiguration kan vara nödvändig för att kryptera din JDBC-anslutning med certifikat i Java Key Store. Läs dokumentationen för din valda JDBC-drivrutin.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Initiera Java Key Store

Initiera objektet genom `import java.security.KeyStore` att läsa in keystore-filen med lösenordet. Standardlösenordet för båda nyckelarkiven är "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Läsa in nyckelarkivet manuellt

Du kan läsa in certifikat manuellt till nyckelarkivet. Skapa en appinställning, `SKIP_JAVA_KEYSTORE_LOAD` , med värdet för för att App Service från att läsa in `1` certifikaten i nyckelarkivet automatiskt. Alla offentliga certifikat som överförs till App Service via Azure Portal lagras under `/var/ssl/certs/` . Privata certifikat lagras under `/var/ssl/private/` .

Du kan interagera eller felsöka Java Key Tool genom att öppna en [SSH-anslutning](configure-linux-open-ssh-session.md) till App Service och köra kommandot `keytool` . En lista [över kommandon finns](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) i dokumentationen för nyckelverktyget. Mer information om KeyStore-API:et finns i [den officiella dokumentationen.](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)

::: zone-end

## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribueras på Azure App Service på Linux med plattformarna NewRelic och AppDynamics Application Performance Monitoring (APM).

### <a name="configure-new-relic"></a>Konfigurera New Relic

::: zone pivot="platform-windows"

1. Skapa ett NewRelic-konto [NewRelic.com](https://newrelic.com/signup)
2. Ladda ned Java-agenten från NewRelic, den har ett filnamn som liknar *newrelic-java-x.x.x.zip*.
3. Kopiera din licensnyckel. Du behöver den för att konfigurera agenten senare.
4. [SSH till din App Service instans](configure-linux-open-ssh-session.md) och skapa en ny katalog */home/site/wwwroot/apm*.
5. Ladda upp de uppackade NewRelic Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent ska vara i */home/site/wwwroot/apm/newrelic*.
6. Ändra YAML-filen *på /home/site/wwwroot/apm/newrelic/newrelic.yml* och ersätt platshållarlicensvärdet med din egen licensnyckel.
7. I Azure Portal bläddrar du till programmet i App Service skapar en ny programinställning.

    - För **Java SE-appar** skapar du en miljövariabel `JAVA_OPTS` med namnet med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - För **Tomcat** skapar du en miljövariabel med `CATALINA_OPTS` namnet med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Skapa ett NewRelic-konto [på NewRelic.com](https://newrelic.com/signup)
2. Ladda ned Java-agenten från NewRelic och har ett filnamn som liknar *detnewrelic-java-x.x.x.zip*.
3. Kopiera din licensnyckel. Du behöver den för att konfigurera agenten senare.
4. [SSH till din App Service instans](configure-linux-open-ssh-session.md) och skapa en ny katalog */home/site/wwwroot/apm*.
5. Ladda upp de uppackade NewRelic Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent ska vara i */home/site/wwwroot/apm/newrelic*.
6. Ändra YAML-filen *på /home/site/wwwroot/apm/newrelic/newrelic.yml* och ersätt platshållarlicensvärdet med din egen licensnyckel.
7. I Azure Portal bläddrar du till programmet i App Service skapar en ny programinställning.
   
    - För **Java SE-appar** skapar du en miljövariabel `JAVA_OPTS` med namnet med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - För **Tomcat** skapar du en miljövariabel med `CATALINA_OPTS` namnet med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Om du redan har en miljövariabel `JAVA_OPTS` för eller lägger du till alternativet i slutet av det aktuella `CATALINA_OPTS` `-javaagent:/...` värdet.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

::: zone pivot="platform-windows"

1. Skapa ett AppDynamics-konto [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Ladda ned Java-agenten från AppDynamics-webbplatsen. Filnamnet liknar *detAppServerAgent-x.x.x.xxxxx.zip*
3. Använd [Kudu-konsolen för](https://github.com/projectkudu/kudu/wiki/Kudu-console) att skapa en ny katalog */home/site/wwwroot/apm*.
4. Ladda upp Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent ska finnas i */home/site/wwwroot/apm/appdynamics*.
5. I Azure Portal bläddrar du till programmet i App Service skapa en ny programinställning.

   - För **Java SE-appar** skapar du en miljövariabel `JAVA_OPTS` med namnet med värdet där App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` namnet.
   - För **Tomcat-appar** skapar du en miljövariabel `CATALINA_OPTS` med namnet med värdet där App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` namnet.

::: zone-end
::: zone pivot="platform-linux"

1. Skapa ett AppDynamics-konto [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Ladda ned Java-agenten från AppDynamics-webbplatsen. Filnamnet kommer att likna *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH till din App Service instans](configure-linux-open-ssh-session.md) och skapa en ny katalog */home/site/wwwroot/apm*.
4. Ladda upp Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent ska finnas i */home/site/wwwroot/apm/appdynamics*.
5. I Azure Portal bläddrar du till programmet i App Service skapa en ny programinställning.

   - För **Java SE-appar** skapar du en miljövariabel `JAVA_OPTS` med namnet med värdet där App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` namnet.
   - För **Tomcat-appar** skapar du en miljövariabel `CATALINA_OPTS` med namnet med värdet där App Service `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` namnet.

::: zone-end

> [!NOTE]
>  Om du redan har en miljövariabel `JAVA_OPTS` för eller lägger du till alternativet i slutet av det aktuella `CATALINA_OPTS` `-javaagent:/...` värdet.

## <a name="configure-data-sources"></a>Konfigurera datakällor

### <a name="java-se"></a>Java SE

För att ansluta till datakällor Spring Boot program föreslår vi att du skapar anslutningssträngar och matar in dem i filen *application.properties.*

1. I avsnittet Konfiguration på sidan App Service anger du ett namn för strängen, klistrar in JDBC-anslutningssträngen i värdefältet och anger typen till "Anpassad". Du kan också ange den här anslutningssträngen som fackinställning.

    Den här anslutningssträngen är tillgänglig för vårt program som en miljövariabel med namnet `CUSTOMCONNSTR_<your-string-name>` . Till exempel får anslutningssträngen som vi skapade ovan namnet `CUSTOMCONNSTR_exampledb` .

2. I filen *application.properties refererar* du till den här anslutningssträngen med miljövariabelnamnet. I vårt exempel använder vi följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Mer information [om Spring Boot finns i](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) dokumentationen om [dataåtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) och externa konfigurationer.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

De här anvisningarna gäller för alla databasanslutningar. Du måste fylla platshållarna med den valda databasens drivrutinsklassnamn och JAR-fil. Tillhandahålls är en tabell med klassnamn och drivrutinsnedladdningar för vanliga databaser.

| Databas   | Drivrutinsklassnamn                             | JDBC-drivrutin                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Ladda](https://dev.mysql.com/downloads/connector/j/) ned (välj "Plattformsoberoende") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Om du vill konfigurera Tomcat att använda Java Database Connectivity (JDBC) eller JAVA Persistence API (JPA) måste du först anpassa miljövariabeln som läses in av Tomcat vid `CATALINA_OPTS` start. Ange dessa värden via en appinställning i App Service [Maven-plugin-programmet](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariablerna på **sidan Inställningar**  >  **för konfigurationsprogram** i Azure Portal.

Kontrollera sedan om datakällan ska vara tillgänglig för ett program eller alla program som körs på Tomcat-servleten.

#### <a name="application-level-data-sources"></a>Datakällor på programnivå

1. Skapa en *context.xml-fil* i *KATALOGEN META-INF/för* projektet. Skapa *katalogen META-INF/om* den inte finns.

2. I *context.xmllägger* du till ett `Context` -element för att länka datakällan till en JNDI-adress. Ersätt `driverClassName` platshållaren med drivrutinens klassnamn från tabellen ovan.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uppdatera programmets *web.xml* att använda datakällan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Delade resurser på servernivå

Tomcat-installationer App Service i Windows finns i delat utrymme på App Service plan. Du kan inte ändra en Tomcat-installation direkt för serveromfattande konfiguration. Om du vill göra konfigurationsändringar på servernivå i Tomcat-installationen måste du kopiera Tomcat till en lokal mapp där du kan ändra Tomcats konfiguration. 

##### <a name="automate-creating-custom-tomcat-on-app-start"></a>Automatisera skapandet av anpassad Tomcat vid appstart

Du kan använda ett startskript för att utföra åtgärder innan en webbapp startar. Startskriptet för att anpassa Tomcat måste slutföra följande steg:

1. Kontrollera om Tomcat redan har kopierats och konfigurerats lokalt. I så fall kan startskriptet avslutas här.
2. Kopiera Tomcat lokalt.
3. Gör de nödvändiga konfigurationsändringarna.
4. Ange att konfigurationen har slutförts.

Här är ett PowerShell-skript som utför följande steg:

```powershell
    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    {... customization ...}

    # Mark that the operation was a success
    New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
```

##### <a name="transforms"></a>Transformering

Ett vanligt användningsfall för att anpassa en Tomcat-version är att ändra konfigurationsfilerna , eller `server.xml` `context.xml` `web.xml` Tomcat. App Service redan dessa filer för att tillhandahålla plattformsfunktioner. Om du vill fortsätta att använda dessa funktioner är det viktigt att bevara innehållet i de här filerna när du gör ändringar i dem. För att åstadkomma detta rekommenderar vi att du använder en [XSL-transformering (XSLT).](https://www.w3schools.com/xml/xsl_intro.asp) Använd en XSL-transformering för att göra ändringar i XML-filerna samtidigt som det ursprungliga innehållet i filen bevaras.

###### <a name="example-xslt-file"></a>Exempel på XSLT-fil

Den här exempeltransformeringen lägger till en ny anslutningsnod i `server.xml` . Observera Identity *Transform*, som bevarar det ursprungliga innehållet i filen.

```xml
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="xml" indent="yes"/>
  
    <!-- Identity transform: this ensures that the original contents of the file are included in the new file -->
    <!-- Ensure that your transform files include this block -->
    <xsl:template match="@* | node()" name="Copy">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()"/>
      </xsl:copy>
    </xsl:template>
  
    <xsl:template match="@* | node()" mode="insertConnector">
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                   contains(., '&lt;Connector') and
                                   (contains(., 'scheme=&quot;https&quot;') or
                                    contains(., &quot;scheme='https'&quot;))]">
      <xsl:value-of select="." disable-output-escaping="yes" />
    </xsl:template>
  
    <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                     comment()[contains(., '&lt;Connector') and
                                               (contains(., 'scheme=&quot;https&quot;') or
                                                contains(., &quot;scheme='https'&quot;))]
                                    )]
                        ">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()" mode="insertConnector" />
      </xsl:copy>
    </xsl:template>
  
    <!-- Add the new connector after the last existing Connnector if there is one -->
    <xsl:template match="Connector[last()]" mode="insertConnector">
      <xsl:call-template name="Copy" />
  
      <xsl:call-template name="AddConnector" />
    </xsl:template>
  
    <!-- ... or before the first Engine if there is no existing Connector -->
    <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                  mode="insertConnector">
      <xsl:call-template name="AddConnector" />
  
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template name="AddConnector">
      <!-- Add new line -->
      <xsl:text>&#xa;</xsl:text>
      <!-- This is the new connector -->
      <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
                 maxThreads="150" scheme="https" secure="true" 
                 keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
                 clientAuth="false" sslProtocol="TLS" />
    </xsl:template>

</xsl:stylesheet>
```

###### <a name="function-for-xsl-transform"></a>Funktion för XSL-transformering

PowerShell har inbyggda verktyg för att transformera XML-filer med hjälp av XSL-transformeringar. Följande skript är en exempelfunktion som du kan använda i `startup.ps1` för att utföra transformeringen:

```powershell
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);

        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
```

##### <a name="app-settings"></a>Appinställningar

Plattformen måste också veta var din anpassade version av Tomcat är installerad. Du kan ange installationens plats i `CATALINA_BASE` appinställningen.

Du kan använda Azure CLI för att ändra den här inställningen:

```powershell
    az webapp config appsettings set -g $MyResourceGroup -n $MyUniqueApp --settings CATALINA_BASE="%LOCAL_EXPANDED%\tomcat"
```

Eller så kan du manuellt ändra inställningen i Azure Portal:

1. Gå till **Inställningar**  >  **Konfiguration**  >  **Programinställningar**.
1. Välj **Ny programinställning.**
1. Använd dessa värden för att skapa inställningen:
   1. **Namn**: `CATALINA_BASE`
   1. **Värde**: `"%LOCAL_EXPANDED%\tomcat"`

##### <a name="example-startupps1"></a>Exempel startup.ps1

Följande exempelskript kopierar en anpassad Tomcat till en lokal mapp, utför en XSL-transformering och anger att transformeringen lyckades:

```powershell
    # Locations of xml and xsl files
    $target_xml="$LOCAL_EXPANDED\tomcat\conf\server.xml"
    $target_xsl="$HOME\site\server.xsl"

    # Define the transform function
    # Useful if transforming multiple files
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);
        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }

    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml

    # Mark that the operation was a success if successful
    if($success){
        New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
    }
```

#### <a name="finalize-configuration"></a>Slutför konfigurationen

Slutligen placerar vi drivrutinens JAR:er i Tomcat-klassökvägen och startar om App Service. Kontrollera att JDBC-drivrutinsfilerna är tillgängliga för Tomcat-classloader genom att placera dem i *katalogen /home/tomcat/lib.* (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att App Service dessa filer till din App Service instans:

1. I [Cloud Shell](https://shell.azure.com)du webapp-tillägget:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Anslut till den lokala tunnelporten med SFTP-klienten och ladda upp filerna till *mappen /home/tomcat/lib.*

Du kan också använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [instruktionerna för att hämta dina FTP-autentiseringsuppgifter.](deploy-configure-credentials.md)

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla databasanslutningar. Du måste fylla platshållarna med den valda databasens drivrutinsklassnamn och JAR-fil. Tillhandahålls är en tabell med klassnamn och drivrutinsnedladdningar för vanliga databaser.

| Databas   | Drivrutinsklassnamn                             | JDBC-drivrutin                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Ladda](https://dev.mysql.com/downloads/connector/j/) ned (välj "Plattformsoberoende") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Om du vill konfigurera Tomcat att använda Java Database Connectivity (JDBC) eller Java Persistence API (JPA) måste du först anpassa miljövariabeln som läses in av Tomcat vid `CATALINA_OPTS` start. Ange dessa värden via en appinställning i App Service [Maven-pluginprogrammet](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariablerna på **sidan**  >  **Inställningar för konfigurationsprogram** i Azure Portal.

Kontrollera sedan om datakällan ska vara tillgänglig för ett program eller alla program som körs på Tomcat-servleten.

#### <a name="application-level-data-sources"></a>Datakällor på programnivå

1. Skapa en *context.xml-fil* i *katalogen META-INF/i* projektet. Skapa *katalogen META-INF/om* den inte finns.

2. I *context.xml* lägger du till `Context` ett -element för att länka datakällan till en JNDI-adress. Ersätt `driverClassName` platshållaren med drivrutinens klassnamn från tabellen ovan.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uppdatera programmets *web.xml* att använda datakällan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Delade resurser på servernivå

Om du lägger till en delad datakälla på servernivå måste du redigera Tomcats server.xml. Ladda först upp ett [startskript](faq-app-service-linux.md#built-in-images) och ange sökvägen till skriptet i **Startkommando**  >  **för konfiguration.** Du kan ladda upp startskriptet med [FTP](deploy-ftp.md).

Startskriptet gör en [xsl-transformering](https://www.w3schools.com/xml/xsl_intro.asp) till server.xml och matar ut den resulterande xml-filen till `/usr/local/tomcat/conf/server.xml` . Startskriptet ska installera libxslt via apk. XSL-filen och startskriptet kan laddas upp via FTP. Nedan visas ett exempel på ett startskript.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Ett exempel på en xsl-fil finns nedan. Xsl-exempelfilen lägger till en ny anslutningsnod i Tomcat-server.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Slutför konfigurationen

Placera slutligen drivrutinens JAR:er i Tomcat-klassökvägen och starta om App Service.

1. Kontrollera att JDBC-drivrutinsfilerna är tillgängliga för Tomcat-classloader genom att placera dem i *katalogen /home/tomcat/lib.* (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att App Service dessa filer till din App Service instans:

    1. I [Cloud Shell](https://shell.azure.com)du webbapptillägget:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Anslut till den lokala tunnelporten med SFTP-klienten och ladda upp filerna till mappen */home/tomcat/lib.*

    Du kan också använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter.](deploy-configure-credentials.md)

2. Om du har skapat en datakälla på servernivå startar du om App Service Linux-program. Tomcat återställs `CATALINA_BASE` till och använder den uppdaterade `/home/tomcat` konfigurationen.

### <a name="jboss-eap"></a>JBoss-EAP

Det finns tre grundläggande steg när du registrerar en datakälla med [JBoss EAP:](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)ladda upp JDBC-drivrutinen, lägga till JDBC-drivrutinen som en modul och registrera modulen. App Service är en tillståndslös värdtjänst, så konfigurationskommandona för att lägga till och registrera datakällmodulen måste skriptas och tillämpas när containern startas.

1. Hämta databasens JDBC-drivrutin. 
2. Skapa en XML-moduldefinitionsfil för JDBC-drivrutinen. Exemplet nedan är en moduldefinition för PostgreSQL.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Placera dina JBoss CLI-kommandon i en fil med namnet `jboss-cli-commands.cli` . JBoss-kommandona måste lägga till modulen och registrera den som en datakälla. Exemplet nedan visar JBoss CLI-kommandon för PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Skapa ett startskript som `startup_script.sh` anropar JBoss CLI-kommandona. Exemplet nedan visar hur du anropar `jboss-cli-commands.cli` din . Senare kommer du att App Service för att köra det här skriptet när containern startar. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Använd valfri FTP-klient och ladda upp JDBC-drivrutinen, `jboss-cli-commands.cli` `startup_script.sh` , och moduldefinitionen till `/site/deployments/tools/` .
2. Konfigurera platsen så att den `startup_script.sh` körs när containern startas. I Azure-portalen går du till **Startkommando**  >  **för allmänna**  >  **konfigurationsinställningar.** Ange startkommandofältet till `/home/site/deployments/tools/startup_script.sh` . **Spara** ändringarna.

Bekräfta att datakällan har lagts till på JBoss-servern genom att använda SSH i webbappen och köra `$JBOSS_HOME/bin/jboss-cli.sh --connect` . När du är ansluten till JBoss kör du `/subsystem=datasources:read-resource` för att skriva ut en lista över datakällorna.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Välja en Java-körningsversion

App Service kan användarna välja huvudversion av JVM, till exempel Java 8 eller Java 11, samt delversionen, till exempel 1.8.0_232 eller 11.0.5. Du kan också välja att den lägre versionen uppdateras automatiskt när nya delversioner blir tillgängliga. I de flesta fall bör produktionsplatser använda fästa mindre JVM-versioner. Detta förhindrar oväntade avbrott under en automatisk uppdatering av delversionen.

Om du väljer att fästa delversionen måste du regelbundet uppdatera JVM-delversionen på webbplatsen. För att säkerställa att programmet körs på den nyare delversionen skapar du en mellanlagringsplats och ökar den lägre versionen på mellanlagringsplatsen. När du har bekräftat att programmet körs korrekt på den nya delversionen kan du växla mellan mellanlagrings- och produktionsplatserna.

## <a name="jboss-eap-hardware-options"></a>Maskinvarualternativ för JBoss EAP

JBoss EAP är endast tillgängligt på alternativen Premium och Isolerad maskinvara. Kunder som skapade en JBoss EAP-webbplats på en kostnadsfri, delad, Basic- eller Standard-nivå under den offentliga förhandsversionen bör skala upp till premium- eller isolerad maskinvarunivå för att undvika oväntade beteenden.

## <a name="java-runtime-statement-of-support"></a>Stöd för Java Runtime

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures Java Development Kit (JDK) som stöds är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) som tillhandahålls via [Azul Systems](https://www.azul.com/). Azul Zulu Enterprise-byggen av OpenJDK är en kostnadsfri, produktionsklar distribution av OpenJDK för Azure och Azure Stack backas upp av Microsoft- och Azul-system. De innehåller alla komponenter för att skapa och köra Java SE-program. Du kan installera JDK från [Java JDK-installation](/azure/developer/java/fundamentals/java-jdk-long-term-support).

Större versionsuppdateringar tillhandahålls via nya körningsalternativ i Azure App Service. Kunder uppdaterar till dessa nyare versioner av Java genom att konfigurera App Service distribution och ansvarar för testning och säkerställa att den större uppdateringen uppfyller deras behov.

JDK:er som stöds korrigeras automatiskt kvartalsvis i januari, april, juli och oktober varje år. Mer information om Java på Azure finns i det [här supportdokumentet.](/azure/developer/java/fundamentals/java-jdk-long-term-support)

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar av större säkerhetsproblem kommer att släppas så snart de blir tillgängliga från Azul Systems. En "större" säkerhetsrisk definieras av en baspoäng på 9,0 eller högre på [NIST Common Vulnerability Scoring System, version 2.](https://nvd.nist.gov/vuln-metrics/cvss)

Tomcat 8.0 har nått slutet på sin livslängd [(EOL) den 30 september 2018.](https://tomcat.apache.org/tomcat-80-eol.html) Även om körningen fortfarande är tillgänglig på Azure App Service kommer Azure inte att tillämpa säkerhetsuppdateringar på Tomcat 8.0. Om möjligt migrerar du dina program till Tomcat 8.5 eller 9.0. Både Tomcat 8.5 och 9.0 finns på Azure App Service. Mer information [finns på den officiella Tomcat-webbplatsen.](https://tomcat.apache.org/whichversion.html) 

### <a name="deprecation-and-retirement"></a>Utfasning och tillbakadragning

Om en Java-körning som stöds kommer att dras tillbaka får Azure-utvecklare som använder den berörda körningen ett meddelande om utfasning minst sex månader innan körningen dras tillbaka.


### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned Production Edition av Azul Zulu Enterprise JDK för lokal utveckling [från Azuls nedladdningswebbplats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Utvecklingsstöd

Produktsupporten för [Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) som stöds av Azure är tillgänglig via Microsoft när du utvecklar för Azure [eller Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med ett kvalificerat [Azure-supportplan.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Nästa steg

Besök Azure [för Java-utvecklarcentret](/java/azure/) för att hitta snabbstarter, självstudier och Java-referensdokumentation för Azure.

Allmänna frågor om att App Service för Linux som inte är specifika för Java-utveckling besvaras i vanliga [frågor och svar App Service Linux.](faq-app-service-linux.md)
