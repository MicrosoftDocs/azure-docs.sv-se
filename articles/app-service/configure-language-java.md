---
title: Konfigurera Java-appar
description: Lär dig hur du konfigurerar Java-appar för att köras på Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
keywords: Azure App Service, Web App, Windows, oss, Java, Tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: cc532c5ac6babb8378860ac5049e931cc7657932
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629265"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Konfigurera en Java-app för Azure App Service

Med Azure App Service kan Java-utvecklare snabbt bygga, distribuera och skala sina Java-, Tomcat-och JBoss-EAP-webbprogram på en helt hanterad tjänst. Distribuera program med maven-plugin-program, från kommando raden eller i redigerings program som IntelliJ, Sol förmörkelse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder App Service. Om du aldrig har använt Azure App Service bör du läsa igenom Java- [snabb](quickstart-java.md) starten först. Allmänna frågor om att använda App Service som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [Azure Web App-plugin-programmet för maven för](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) att distribuera dina. War-eller. jar-filer. Distribution med populära IDE: er stöds också med [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) eller [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Annars beror distributions metoden på din Arkiv typ:

### <a name="java-se"></a>Java SE

Om du vill distribuera. jar-filer till Java SE använder du `/api/zipdeploy/` slut punkten för kudu-webbplatsen. Mer information om det här API: et finns i [den här dokumentationen](./deploy-zip.md#rest).

### <a name="tomcat"></a>Tomcat

Om du vill distribuera. War-filer till Tomcat använder du `/api/wardeploy/` slut punkten för att publicera Arkiv filen. Mer information om det här API: et finns i [den här dokumentationen](./deploy-zip.md#deploy-war-file).

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss-EAP

Om du vill distribuera. War-filer till JBoss använder du `/api/wardeploy/` slut punkten för att publicera Arkiv filen. Mer information om det här API: et finns i [den här dokumentationen](./deploy-zip.md#deploy-war-file).

[Använd FTP](deploy-ftp.md)för att distribuera. öron filer.

::: zone-end

Distribuera inte. War eller. jar med FTP. FTP-verktyget är utformat för att ladda upp start skript, beroenden eller andra runtime-filer. Det är inte det bästa valet för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och Felsök appar

Prestanda rapporter, trafik visualiseringar och hälso checkups är tillgängliga för varje app via Azure Portal. Mer information finns i [Översikt över Azure App Service-diagnostik](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Mer information finns i [Stream-loggar i Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>Åtkomst till SSH-konsol

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Fel söknings verktyg

De inbyggda Java-avbildningarna baseras på operativ systemet [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Använd `apk` Package Manager för att installera eventuella fel söknings verktyg eller kommandon.

::: zone-end

### <a name="flight-recorder"></a>Flyg inspelning

Alla Java-körningar på App Service med Azul-JVMs levereras med Zulu Flight-inspelare. Du kan använda detta för att registrera JVM-, system-och program händelser och felsöka problem i Java-programmen.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Inspelnings tid

Om du vill ha en tidsad inspelning behöver du ett PID (process-ID) för Java-programmet. Du hittar PID genom att öppna en webbläsare till webbappens SCM-webbplats på https://<ditt webbplats namn>. scm.azurewebsites.net/ProcessExplorer/. Den här sidan visar de processer som körs i din webbapp. Hitta processen med namnet java i tabellen och kopiera motsvarande PID (process-ID).

Öppna sedan **fel söknings konsolen** i det övre verktygsfältet på SCM-platsen och kör följande kommando. Ersätt `<pid>` med det process-ID som du kopierade tidigare. Det här kommandot startar en 30 sekunders profilering av ditt Java-program och genererar en fil med namnet `timed_recording_example.jfr` i `D:\home` katalogen.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

Använd SSH i App Service och kör `jcmd` kommandot för att se en lista över alla Java-processer som körs. Förutom jcmd bör du se ditt Java-program med ett process-ID-nummer (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Kör kommandot nedan för att starta en 30-sekunders inspelning av JVM. Detta kommer att profilera JVM och skapa en JFR-fil med namnet *jfr_example. jfr* i arbets katalogen. (Ersätt 116 med PID för din Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Under det 30 andra intervallet kan du verifiera att inspelningen sker genom att köra `jcmd 116 JFR.check` . Då visas alla inspelningar för den aktuella Java-processen.

#### <a name="continuous-recording"></a>Kontinuerlig inspelning

Du kan använda Zulu Flight-brännare för att kontinuerligt profilera ditt Java-program med minimal påverkan på körnings prestanda ([källa](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Det gör du genom att köra följande Azure CLI-kommando för att skapa en app-inställning med namnet JAVA_OPTS med den nödvändiga konfigurationen. Innehållet i inställningen för JAVA_OPTS appen skickas till `java` kommandot när appen startas.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

När inspelningen har startats kan du när som helst dumpa de aktuella inspelnings data med hjälp av `JFR.dump` kommandot.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Analysera `.jfr` filer

Använd [FTPS](deploy-ftp.md) för att ladda ned jfr-filen till den lokala datorn. Om du vill analysera JFR-filen laddar du ned och installerar [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Anvisningar om hur du Zulu uppdrags kontroll finns i [Azul-dokumentationen](https://docs.azul.com/zmc/) och i [installations anvisningarna](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="app-logging"></a>Loggning av app

::: zone pivot="platform-windows"

Aktivera [program loggning](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service att skriva programmets standard-och standard konsol fel strömmar till det lokala fil systemet eller Azure-Blob Storage. Loggning till den lokala App Service fil Systems instansen är inaktive rad 12 timmar efter att den har kon figurer ATS Om du behöver kvarhållare kan du konfigurera programmet att skriva utdata till en Blob Storage-behållare. Du hittar dina program loggar för Java och Tomcat i katalogen */Home/LogFiles/Application/* .

::: zone-end
::: zone pivot="platform-linux"

Aktivera [program loggning](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service att skriva programmets standard-och standard konsol fel strömmar till det lokala fil systemet eller Azure-Blob Storage. Om du behöver kvarhållare kan du konfigurera programmet att skriva utdata till en Blob Storage-behållare. Du hittar dina program loggar för Java och Tomcat i katalogen */Home/LogFiles/Application/* .

Azure Blob Storage-loggning för Linux-baserade App Services kan bara konfigureras med [Azure Monitor (för hands version)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Om ditt program använder [logback](https://logback.qos.ch/) eller [log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra spårningarna för granskning till Azure Application insikter med hjälp av konfigurations anvisningar för loggnings ramverk i [utforska Java trace-loggar i Application Insights](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder direkt justering och anpassning genom Azure Portal och CLI. Läs följande artiklar för konfiguration av icke-Java-en webbapp:

- [Konfigurera appinställningar](configure-common.md#configure-app-settings)
- [Konfigurera en anpassad domän](app-service-web-tutorial-custom-domain.md)
- [Konfigurera SSL-bindningar](configure-ssl-bindings.md)
- [Lägg till en CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurera kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Ange Java Runtime-alternativ

Om du vill ange allokerat minne eller andra alternativ för JVM-körning skapar du en [app-inställning](configure-common.md#configure-app-settings) med namnet `JAVA_OPTS` med alternativen. App Service skickar den här inställningen som en miljö variabel till Java-körningsmiljön när den startas.

I Azure Portal, under **program inställningar** för webbappen, skapar du en ny app-inställning med namnet `JAVA_OPTS` som innehåller de ytterligare inställningarna, till exempel `-Xms512m -Xmx1204m` .

Om du vill konfigurera appens inställning från maven-plugin-programmet lägger du till inställningar/värde-Taggar i Azure plugin-avsnittet. I följande exempel anges en angiven minsta och högsta Java-Heap-storlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett enda program med en distributions plats i sin App Service plan kan använda följande alternativ:

- B1 och S1-instanser: `-Xms1024m -Xmx1024m`
- B2-och S2-instanser: `-Xms3072m -Xmx3072m`
- B3 och S3-instanser: `-Xms6144m -Xmx6144m`

När du finjusterar inställningarna för programheap granskar du App Service plan information och tar hänsyn till flera program och distributions fack måste hitta den optimala allokeringen av minnet.

### <a name="turn-on-web-sockets"></a>Aktivera webb-Sockets

Aktivera stöd för Web Sockets i Azure Portal i **program inställningarna** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera WebSocket-stöd med hjälp av Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standard tecken kodning

Skapa en ny app-inställning med namnet med värde under **program inställningar** för webbappen i Azure Portal `JAVA_OPTS` `-Dfile.encoding=UTF-8` .

Du kan också konfigurera appens inställning med hjälp av App Service maven-plugin-programmet. Lägg till inställnings namn och värde Taggar i plugin-konfigurationen:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Kompilera JSP-filer i förväg

För att förbättra prestanda för Tomcat-program kan du kompilera JSP-filerna innan du distribuerar till App Service. Du kan använda [plugin-programmet maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) som tillhandahålls av Apache sling eller med hjälp av den här [Ant build-filen](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service har samma uppsättning [rekommenderade säkerhets metoder](../security/fundamentals/paas-applications-using-app-services.md) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (enkel autentisering)

Konfigurera app-autentisering i Azure Portal med alternativet **autentisering och auktorisering** . Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure Portal konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera din app service app för att använda Azure Active Directory inloggning](configure-authentication-provider-aad.md) och relaterade artiklar för andra identitets leverantörer. Om du behöver aktivera flera inloggnings leverantörer följer du anvisningarna i artikeln [anpassa App Service-autentisering](app-service-authentication-how-to.md) .

#### <a name="java-se"></a>Java SE

Förstarts utvecklare kan använda [Start Start programmet Azure Active Directory våren](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) för att skydda program med hjälp av välbekanta säkerhets anteckningar och API: er. Se till att öka storleken på den maximala sidhuvuden i *program. Properties* -filen. Vi föreslår ett värde av `16384` .

#### <a name="tomcat"></a>Tomcat

Ditt Tomcat-program kan komma åt användarens anspråk direkt från servlet genom att omvandla huvudobjektet till ett kart objekt. Kart-objektet mappar varje anspråks typ till en samling av anspråken för den typen. I koden nedan `request` är en instans av `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du kontrol lera `Map` objektet för ett enskilt anspråk. Följande kodfragment itererar till exempel igenom alla anspråks typer och skriver ut innehållet i varje samling.

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

Använd sökvägen för att logga ut användare `/.auth/ext/logout` . Om du vill utföra andra åtgärder kan du läsa dokumentationen om [App Service autentisering och auktorisering](./app-service-authentication-how-to.md). Det finns också en officiell dokumentation om Tomcat [HttpServletRequest-gränssnittet](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servlet-metoder har också dehydratiseras baserat på din App Service konfiguration:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den här funktionen skapar du en program inställning med namnet `WEBSITE_AUTH_SKIP_PRINCIPAL` med värdet `1` . Om du vill inaktivera alla servlet-filter som har lagts till av App Service skapar du en inställning `WEBSITE_SKIP_FILTERS` med namnet med värdet `1` .

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ anvisningarna i avsnittet [skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md) för att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domän namn. Som standard tillåter programmet fortfarande HTTP-anslutningar – Följ de specifika stegen i självstudien för att genomdriva SSL och TLS.

### <a name="use-keyvault-references"></a>Använda nyckel Valvs referenser

Azure-nyckels [valvet](../key-vault/general/overview.md) tillhandahåller centraliserad hemlig hantering med åtkomst principer och gransknings historik. Du kan lagra hemligheter (till exempel lösen ord eller anslutnings strängar) i ett nyckel valv och komma åt dessa hemligheter i ditt program via miljövariabler.

Börja med att följa anvisningarna för [att ge appen åtkomst till Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [skapa en nyckel valv referens till din hemlighet i en program inställning](app-service-key-vault-references.md#reference-syntax). Du kan kontrol lera att referensen matchar hemligheten genom att skriva ut miljövariabeln och fjärrans luta till App Service terminalen.

Om du vill mata in dessa hemligheter i din fjäder-eller Tomcat-konfigurationsfil använder du miljövariabeln insprutning-syntax ( `${MY_ENV_VAR}` ). För våren-konfigurationsfiler läser du den här dokumentationen om de [externa konfigurationerna](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Använda Java-nyckel arkivet

Som standard kommer alla offentliga eller privata certifikat som [laddats upp till App Service Linux](configure-ssl-certificate.md) att läsas in i respektive Java-nyckel Arkiv när behållaren startar. När du har laddat upp certifikatet måste du starta om App Service för att det ska kunna läsas in i Java-nyckel arkivet. Offentliga certifikat läses in i nyckel arkivet vid `$JAVA_HOME/jre/lib/security/cacerts` , och privata certifikat lagras i `$JAVA_HOME/lib/security/client.jks` .

Ytterligare konfiguration kan vara nödvändig för att kryptera JDBC-anslutningen med certifikat i Java-nyckel arkivet. Se dokumentationen för din valda JDBC-drivrutin.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Initiera Java-nyckel arkivet

Om du vill initiera `import java.security.KeyStore` objektet läser du in nyckel lagrings filen med lösen ordet. Standard lösen ordet för båda nyckel arkiven är "changeit".

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

#### <a name="manually-load-the-key-store"></a>Läs in nyckel lagret manuellt

Du kan läsa in certifikat manuellt till nyckel lagret. Skapa en app-inställning, `SKIP_JAVA_KEYSTORE_LOAD` med värdet `1` att inaktivera App Service från att läsa in certifikaten i nyckel arkivet automatiskt. Alla offentliga certifikat som laddats upp till App Service via Azure Portal lagras under `/var/ssl/certs/` . Privata certifikat lagras under `/var/ssl/private/` .

Du kan interagera eller felsöka Java-nyckel verktyget genom att [öppna en SSH-anslutning](configure-linux-open-ssh-session.md) till app service och köra kommandot `keytool` . En lista över kommandon finns i [dokumentationen för nyckel verktyget](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Mer information om API: t för nyckel Arkiv finns i [den officiella dokumentationen](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

::: zone-end

## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribuerats på Azure App Service i Linux med NewRelic-och AppDynamics-plattformarna för program prestanda övervakning (APM).

### <a name="configure-new-relic"></a>Konfigurera nya Relic

::: zone pivot="platform-windows"

1. Skapa ett NewRelic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Hämta Java-agenten från NewRelic kommer den att ha ett fil namn som liknar *newrelic-java-x.x.x.zip*.
3. Kopiera licens nyckeln, du behöver den för att konfigurera agenten senare.
4. [Använda SSH i App Service-instansen](configure-linux-open-ssh-session.md) och skapa en ny katalog */Home/site/wwwroot/APM*.
5. Överför de uppackade NewRelic Java agent-filerna till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/newrelic*.
6. Ändra YAML-filen på */Home/site/wwwroot/APM/newrelic/newrelic.yml* och ersätt licens värdet för plats hållaren med din egen licens nyckel.
7. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.

    - För **Java se** -appar skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - För **Tomcat** skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Skapa ett NewRelic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Hämta Java-agenten från NewRelic kommer den att ha ett fil namn som liknar *newrelic-java-x.x.x.zip*.
3. Kopiera licens nyckeln, du behöver den för att konfigurera agenten senare.
4. [Använda SSH i App Service-instansen](configure-linux-open-ssh-session.md) och skapa en ny katalog */Home/site/wwwroot/APM*.
5. Överför de uppackade NewRelic Java agent-filerna till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/newrelic*.
6. Ändra YAML-filen på */Home/site/wwwroot/APM/newrelic/newrelic.yml* och ersätt licens värdet för plats hållaren med din egen licens nyckel.
7. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
   
    - För **Java se** -appar skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - För **Tomcat** skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Om du redan har en miljö variabel för `JAVA_OPTS` eller `CATALINA_OPTS` lägger du till `-javaagent:/...` alternativet i slutet av det aktuella värdet.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

::: zone pivot="platform-windows"

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Hämta Java-agenten från AppDynamics-webbplatsen, fil namnet ser ut ungefär som *AppServerAgent-x.x.x.xxxxx.zip*
3. Använd [kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att skapa en ny katalog- */Home/site/wwwroot/APM*.
4. Överför Java-agentens filer till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/AppDynamics*.
5. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.

   - För **Java se** -appar skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.
   - För **Tomcat** -appar skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.

::: zone-end
::: zone pivot="platform-linux"

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Hämta Java-agenten från AppDynamics-webbplatsen, fil namnet ser ut ungefär som *AppServerAgent-x.x.x.xxxxx.zip*
3. [Använda SSH i App Service-instansen](configure-linux-open-ssh-session.md) och skapa en ny katalog */Home/site/wwwroot/APM*.
4. Överför Java-agentens filer till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/AppDynamics*.
5. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.

   - För **Java se** -appar skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.
   - För **Tomcat** -appar skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.

::: zone-end

> [!NOTE]
>  Om du redan har en miljö variabel för `JAVA_OPTS` eller `CATALINA_OPTS` lägger du till `-javaagent:/...` alternativet i slutet av det aktuella värdet.

## <a name="configure-data-sources"></a>Konfigurera datakällor

### <a name="java-se"></a>Java SE

För att ansluta till data källor i Start program för våren rekommenderar vi att du skapar anslutnings strängar och infogar dem i *appens. egenskaps* fil.

1. I avsnittet "konfiguration" på sidan App Service anger du ett namn för strängen, klistrar in JDBC-anslutningssträngen i fältet värde och anger typen anpassad. Du kan också ange den här anslutnings strängen som plats inställning.

    Den här anslutnings strängen är tillgänglig för vårt program som en miljö variabel med namnet `CUSTOMCONNSTR_<your-string-name>` . Den anslutnings sträng som vi skapade ovan får exempelvis namnet `CUSTOMCONNSTR_exampledb` .

2. I din *app. Properties* -fil refererar du till den här anslutnings strängen med miljö variabel namnet. I vårt exempel skulle vi använda följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Mer information om det här ämnet finns i [vår Boot-dokumentation om data åtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) och [externa konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla databas anslutningar. Du måste fylla i plats hållarna med den valda databasens driv rutins klass namn och JAR-fil. Det angivna är en tabell med klass namn och hämtning av driv rutiner för vanliga databaser.

| Databas   | Klass namn för driv rutin                             | JDBC-drivrutin                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Hämta](https://dev.mysql.com/downloads/connector/j/) (Välj plattform oberoende) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Om du vill konfigurera Tomcat för att använda Java Database Connectivity (JDBC) eller Java-persistence API (JPA), måste du först anpassa den `CATALINA_OPTS` miljö variabel som läses in av Tomcat vid start. Ange dessa värden via en app-inställning i [maven-plugin-programmet för App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler på sidan Inställningar för **konfigurations**  >  **program** i Azure Portal.

Ta sedan reda på om data källan ska vara tillgänglig för ett program eller för alla program som körs på Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Data källor på program nivå

1. Skapa en *context.xml* -fil i projektets *meta-inf/* katalog. Skapa *meta-inf/-* katalogen om den inte finns.

2. I *context.xml* lägger du till ett- `Context` element för att länka data källan till en JNDI-adress. Ersätt `driverClassName` plats hållaren med driv Rutinens klass namn från tabellen ovan.

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

3. Uppdatera programmets *web.xml* att använda data källan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Slutför konfiguration

Slutligen kommer vi att placera driv rutins jar v7 i Tomcat-classpath och starta om App Service. Se till att JDBC-drivrutinsfiler är tillgängliga för Tomcat-classloader genom att placera dem i */Home/Tomcat/lib* -katalogen. (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att överföra filerna till din App Service-instans:

1. I [Cloud Shell](https://shell.azure.com)installerar du webapp-tillägget:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Kör följande CLI-kommando för att skapa en SSH-tunnel från det lokala systemet till App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Anslut till den lokala tunnel porten med din SFTP-klient och överför filerna till mappen */Home/Tomcat/lib* .

Alternativt kan du använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter](deploy-configure-credentials.md).

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla databas anslutningar. Du måste fylla i plats hållarna med den valda databasens driv rutins klass namn och JAR-fil. Det angivna är en tabell med klass namn och hämtning av driv rutiner för vanliga databaser.

| Databas   | Klass namn för driv rutin                             | JDBC-drivrutin                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Hämta](https://dev.mysql.com/downloads/connector/j/) (Välj plattform oberoende) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Om du vill konfigurera Tomcat för att använda Java Database Connectivity (JDBC) eller Java-persistence API (JPA), måste du först anpassa den `CATALINA_OPTS` miljö variabel som läses in av Tomcat vid start. Ange dessa värden via en app-inställning i [maven-plugin-programmet för App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler på sidan Inställningar för **konfigurations**  >  **program** i Azure Portal.

Ta sedan reda på om data källan ska vara tillgänglig för ett program eller för alla program som körs på Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Data källor på program nivå

1. Skapa en *context.xml* -fil i projektets *meta-inf/* katalog. Skapa *meta-inf/-* katalogen om den inte finns.

2. I *context.xml* lägger du till ett- `Context` element för att länka data källan till en JNDI-adress. Ersätt `driverClassName` plats hållaren med driv Rutinens klass namn från tabellen ovan.

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

3. Uppdatera programmets *web.xml* att använda data källan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Delade resurser på server nivå

Om du lägger till en delad data källa på server nivå krävs det att du redigerar Tomcat server.xml. Börja med att ladda upp ett [Start skript](faq-app-service-linux.md#built-in-images) och ange sökvägen till skriptet i **konfigurations**  >  **Start kommandot**. Du kan ladda upp start skriptet med [FTP](deploy-ftp.md).

Start skriptet gör en XSL- [transformering](https://www.w3schools.com/xml/xsl_intro.asp) till server.xml-filen och utdata från den resulterande XML-filen till `/usr/local/tomcat/conf/server.xml` . Start skriptet bör installera libxslt via APK. XSL-filen och start skriptet kan överföras via FTP. Nedan visas ett exempel på ett start skript.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Ett exempel på en XSL-fil anges nedan. Exempel-XSL-filen lägger till en ny anslutnings-nod till Tomcat-server.xml.

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

#### <a name="finalize-configuration"></a>Slutför konfiguration

Till sist placerar du driv rutins jar v7 i Tomcat-classpath och startar om App Service.

1. Se till att JDBC-drivrutinsfiler är tillgängliga för Tomcat-classloader genom att placera dem i */Home/Tomcat/lib* -katalogen. (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att överföra filerna till din App Service-instans:

    1. I [Cloud Shell](https://shell.azure.com)installerar du webapp-tillägget:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från det lokala systemet till App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Anslut till den lokala tunnel porten med din SFTP-klient och överför filerna till mappen */Home/Tomcat/lib* .

    Alternativt kan du använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter](deploy-configure-credentials.md).

2. Om du har skapat en data källa på server nivå startar du om App Service Linux-programmet. Tomcat kommer att återställas `CATALINA_BASE` till `/home/tomcat` och använda den uppdaterade konfigurationen.

### <a name="jboss-eap"></a>JBoss-EAP

Det finns tre grundläggande steg när du [registrerar en data källa med JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management): laddar upp JDBC-drivrutinen, lägger till JDBC-drivrutinen som en modul och registrerar modulen. App Service är en tillstånds lös tjänst, så konfigurations kommandon för att lägga till och registrera DataSource-modulen måste följas av skript och tillämpas när behållaren startar.

1. Hämta databasens JDBC-drivrutin. 
2. Skapa en definitions fil för XML-modul för JDBC-drivrutinen. Exemplet som visas nedan är en modul definition för PostgreSQL.

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

1. Sätt dina JBoss CLI-kommandon i en fil med namnet `jboss-cli-commands.cli` . JBoss-kommandona måste lägga till modulen och registrera den som en data källa. Exemplet nedan visar JBoss CLI-kommandon för PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Skapa ett start skript `startup_script.sh` som anropar JBoss CLI-kommandona. Exemplet nedan visar hur du anropar `jboss-cli-commands.cli` . Senare kommer du att configre App Service för att köra skriptet när behållaren startar. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Använd en valfri FTP-klient, ladda upp JDBC-drivrutinen, `jboss-cli-commands.cli` , `startup_script.sh` och modulen definition till `/site/deployments/tools/` .
2. Konfigurera platsen så att `startup_script.sh` den körs när behållaren startar. I Azure Portal navigerar du till   >    >  **Start kommandot** konfiguration allmänna inställningar. Ange start kommando fältet till `/home/site/deployments/tools/startup_script.sh` . **Spara** ändringarna.

För att bekräfta att data källan har lagts till i JBoss-servern, SSH i webapp och kör `$JBOSS_HOME/bin/jboss-cli.sh --connect` . När du är ansluten till JBoss kör `/subsystem=datasources:read-resource` du för att skriva ut en lista över data källorna.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Välja en Java Runtime-version

App Service låter användare välja den huvud version av JVM, till exempel Java 8 eller Java 11, samt den lägre versionen, till exempel 1.8.0 _232 eller 11.0.5. Du kan också välja att den lägre versionen uppdateras automatiskt när nya del versioner blir tillgängliga. I de flesta fall bör produktions platser använda fästa mindre JVM-versioner. Detta förhindrar unnanticipated avbrott under en del versions automatisk uppdatering.

Om du väljer att fästa den lägre versionen måste du regelbundet uppdatera den lägre JVM-versionen på platsen. För att säkerställa att ditt program körs på den senare lägre versionen skapar du en mellanlagringsplats och ökar den lägre versionen på mellanlagringsplatsen. När du har bekräftat att programmet körs korrekt på den nya lägre versionen kan du växla mellan mellanlagrings-och produktions platser.

## <a name="jboss-eap-hardware-options"></a>Alternativ för JBoss EAP-maskinvara

JBoss EAP är bara tillgängligt för alternativen Premium och isolerad maskin vara. Kunder som har skapat en JBoss EAP-webbplats på en kostnads fri, delad, Basic-eller standard-nivå under den offentliga för hands versionen bör skala upp till Premium eller isolerad maskin varu nivå för att undvika oväntade beteenden.

## <a name="java-runtime-statement-of-support"></a>Stöd för Java Runtime-sats

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures Java Development Kit (JDK) som stöds är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) via [Azul system](https://www.azul.com/). Azul Zulu Enterprise builds of OpenJDK är en kostnads färdig distribution med flera plattformar av OpenJDK för Azure och Azure Stack som backas upp av Microsoft-och Azul-system. De innehåller alla komponenter för att skapa och köra Java SE-program. Du kan installera JDK från [Java JDK-installationen](/azure/developer/java/fundamentals/java-jdk-long-term-support).

Huvud versions uppdateringar kommer att tillhandahållas via nya körnings alternativ i Azure App Service. Kunder uppdaterar till dessa nyare versioner av Java genom att konfigurera sina App Service-distributioner och ansvarar för att testa och se till att den viktiga uppdateringen uppfyller deras behov.

JDKs som stöds korrigeras automatiskt en gång i kvartalet i januari, april, juli och oktober varje år. Mer information om java i Azure finns i [det här support dokumentet](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar för viktiga säkerhets problem kommer att släppas så snart de blir tillgängliga från Azul system. En "större" sårbarhet definieras av en baspoäng på 9,0 eller högre på [NIST vanliga sårbarhets bedömnings system, version 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8,0 har uppnått [livs längd (EOL) från och med 30 September 2018](https://tomcat.apache.org/tomcat-80-eol.html). Även om körningen fortfarande är tillgänglig på Azure App Service kommer Azure inte att tillämpa säkerhets uppdateringar på Tomcat 8,0. Migrera om möjligt dina program till Tomcat 8,5 eller 9,0. Både Tomcat 8,5 och 9,0 finns på Azure App Service. Mer information finns på den [officiella Tomcat-webbplatsen](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds dras tillbaka kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om förvarning minst sex månader innan körningen dras tillbaka.


### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned Production Edition of Azul Zulu Enterprise JDK för lokal utveckling från [Azuls nedladdnings plats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Utvecklings support

Produkt support för det [Azure-Azul Zulu-JDK som stöds](https://www.azul.com/downloads/azure-only/zulu/) är tillgängligt via Microsoft när du utvecklar för azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal för Azure.

## <a name="next-steps"></a>Nästa steg

Besök [Azure för Java Developer](/java/azure/) Center för att hitta Azures snabb starter, självstudier och Java-referens dokumentation.

Allmänna frågor om hur du använder App Service för Linux som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service Linux](faq-app-service-linux.md).
