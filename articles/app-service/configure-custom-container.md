---
title: Konfigurera en anpassad container
description: Lär dig hur du konfigurerar en anpassad container i Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.topic: article
ms.date: 02/23/2021
ms.custom: devx-track-azurepowershell
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 48d2eeec1bdb1b9b4a393b4116092f043716077c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832041"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Konfigurera en anpassad container för Azure App Service

Den här artikeln visar hur du konfigurerar en anpassad container för körning på Azure App Service.

::: zone pivot="container-windows"

Den här guiden innehåller viktiga begrepp och instruktioner för containerisering av Windows-appar i App Service. Om du aldrig har använt Azure App Service följer du [snabbstarten och självstudien om](quickstart-custom-container.md) anpassade [containrar](tutorial-custom-container.md) först.

::: zone-end

::: zone pivot="container-linux"

Den här guiden innehåller viktiga begrepp och instruktioner för containerisering av Linux-appar i App Service. Om du aldrig har använt Azure App Service följer du [snabbstarten och självstudien om](quickstart-custom-container.md) anpassade [containrar](tutorial-custom-container.md) först. Det finns också en [snabbstart och självstudie för en app med](quickstart-multi-container.md) flera [containrar.](tutorial-multi-container-app.md)

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Överordnade avbildningar som stöds

För din anpassade Windows-avbildning måste du välja rätt överordnad [avbildning (basavbildning) för](https://docs.docker.com/develop/develop-images/baseimages/) det ramverk som du vill använda:

- Om du .NET Framework en överordnad avbildning baserat på Windows Server Core [Långsiktig underhållskanal (LTSC)-versionen.](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 
- Om du vill distribuera .NET Core-appar använder du en överordnad avbildning som baseras på SAC-versionen (Windows Server Nano [Semi-Annual Servicing Channel).](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 

Det tar lite tid att ladda ned en överordnad avbildning när appen startas. Men du kan minska starttiden genom att använda någon av följande överordnade avbildningar som redan har cachelagrats i Azure App Service:

- [](https://hub.docker.com/_/microsoft-windows-servercore)mcr.microsoft.com/windows/servercore:2004
- [](https://hub.docker.com/_/microsoft-windows-servercore)mcr.microsoft.com/windows/servercore:ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Ändra Docker-avbildningen av en anpassad container

Om du vill ändra en befintlig anpassad containerapp från den aktuella Docker-avbildningen till en ny avbildning använder du följande kommando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Använda en avbildning från ett privat register

Om du vill använda en avbildning från ett privat register, till Azure Container Registry, kör du följande kommando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

För *\<username>* och , ange *\<password>* inloggningsuppgifterna för ditt privata registerkonto.

## <a name="i-dont-see-the-updated-container"></a>Jag ser inte den uppdaterade containern

Om du ändrar inställningarna för Docker-containern så att de pekar på en ny container kan det ta några minuter innan appen visar HTTP-begäranden från den nya containern. När den nya containern hämtas och startas fortsätter App Service att betjäna begäranden från den gamla containern. Endast när den nya containern har startats och är redo att ta emot begäranden App Service att skicka begäranden till den.

## <a name="how-container-images-are-stored"></a>Hur containeravbildningar lagras

Första gången du kör en anpassad Docker-avbildning App Service App Service en och `docker pull` hämtar alla avbildningslager. Dessa lager lagras på disk, till exempel om du använder Docker lokalt. Varje gång appen startas om App Service , `docker pull` men hämtar bara lager som har ändrats. Om det inte har gjorts några ändringar App Service befintliga lager på den lokala disken.

Om appen av någon anledning ändrar beräkningsinstanser, till exempel att skala upp och ned prisnivåerna, måste App Service hämta alla lager igen. Samma sak gäller om du skalar ut för att lägga till ytterligare instanser. Det finns också sällsynta fall där appinstanserna kan ändras utan en skalningsåtgärd.

## <a name="configure-port-number"></a>Konfigurera portnummer

Som standard förutsätter App Service din anpassade container lyssnar på port 80. Om containern lyssnar på en annan port anger du `WEBSITES_PORT` appinställningen i din App Service appen. Du kan ange det via [Cloud Shell](https://shell.azure.com). I Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service tillåter för närvarande att containern endast exponerar en port för HTTP-begäranden. 

## <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Din anpassade container kan använda miljövariabler som måste anges externt. Du kan skicka dem via [Cloud Shell](https://shell.azure.com). I Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

När appen körs matas App Service appinställningar in i processen som miljövariabler automatiskt. Du kan verifiera miljövariabler för containrar med URL:en `https://<app-name>.scm.azurewebsites.net/Env)` .

Om din app använder avbildningar från ett privat register eller från Docker Hub sparas autentiseringsuppgifter för åtkomst till lagringsplatsen i miljövariabler: `DOCKER_REGISTRY_SERVER_URL` `DOCKER_REGISTRY_SERVER_USERNAME` , och `DOCKER_REGISTRY_SERVER_PASSWORD` . På grund av säkerhetsrisker exponeras inga av dessa reserverade variabelnamn för programmet.

::: zone pivot="container-windows"
För IIS- .NET Framework (4.0 eller högre) containrar matas de in som `System.ConfigurationManager` .NET-appinställningar och anslutningssträngar automatiskt av App Service. För alla andra språk eller ramverk tillhandahålls de som miljövariabler för processen, med något av följande motsvarande prefix:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Den här metoden fungerar både för appar med en enskild container eller appar med flera containrar, där miljövariablerna anges i *filen docker-compose.yml.*

::: zone-end

## <a name="use-persistent-shared-storage"></a>Använda beständig delad lagring

::: zone pivot="container-windows"

Du kan använda *katalogen C:\home* i appens filsystem för att spara filer mellan omstarter och dela dem mellan instanser. i `C:\home` din app tillhandahålls för att ge containerappen åtkomst till beständig lagring.

När beständig lagring är inaktiverat bevaras `C:\home` inte skrivningar till katalogen. [Docker-värdloggar och containerloggar](#access-diagnostic-logs) sparas i en beständig standardlagringsplats som inte är kopplad till containern. När beständig lagring är aktiverad bevaras alla skrivningar till katalogen och kan nås av alla instanser av en `C:\home` utskalade app och loggen är tillgänglig på `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

Du kan använda *katalogen /home* i appens filsystem för att spara filer mellan omstarter och dela dem mellan instanser. i `/home` din app tillhandahålls för att göra det möjligt för containerappen att komma åt beständig lagring.

När beständig lagring är inaktiverad bevaras inte skrivningar till katalogen `/home` vid omstart av appen eller över flera instanser. Det enda undantaget är `/home/LogFiles` katalogen, som används för att lagra Docker- och containerloggarna. När beständig lagring är aktiverat bevaras alla skrivningar till katalogen och kan nås av alla instanser `/home` av en utskalade app.

::: zone-end

Som standard är beständig lagring inaktiverad och inställningen visas inte i appinställningarna. Om du vill aktivera det anger `WEBSITES_ENABLE_APP_SERVICE_STORAGE` du appinställningen via [Cloud Shell](https://shell.azure.com). I Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Du kan också [konfigurera din egen beständiga lagring.](configure-connect-to-azure-storage.md)

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

App Service TLS/SSL avslutas på frontend-sidan. Det innebär att TLS/SSL-begäranden aldrig kommer till din app. Du behöver inte, och bör inte implementera något stöd för TLS/SSL i din app. 

Frontend-ändarna finns i Azure-datacenter. Om du använder TLS/SSL med din app krypteras alltid trafiken över Internet på ett säkert sätt.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Anpassa ASP.NET-maskinnyckelinjektion

 Under containerstarten matas automatiskt genererade nycklar in i containern som datornycklar för ASP.NET kryptografiska rutiner. Du hittar [dessa nycklar i containern genom](#connect-to-the-container) att leta efter följande miljövariabler: , , , `MACHINEKEY_Decryption` `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` `MACHINEKEY_Validation` . 

De nya nycklarna vid varje omstart kan återställas ASP.NET formulärautentisering och visningstillstånd, om din app är beroende av dem. För att förhindra automatisk återskapande av nycklar, [ange dem manuellt som App Service appinställningar](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Ansluta till containern

Du kan ansluta till Windows-containern direkt för diagnostikuppgifter genom att gå till `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Så här fungerar det:

- Med felsökningskonsolen kan du köra interaktiva kommandon, till exempel starta PowerShell-sessioner, inspektera registernycklar och navigera i hela containerfilsystemet.
- Den fungerar separat från den grafiska webbläsaren ovanför, vilket endast visar filerna i din [delade lagring.](#use-persistent-shared-storage)
- I en utskalade app ansluts felsökningskonsolen till en av containerinstanserna. Du kan välja en annan instans från **listrutan** Instans på den översta menyn.
- Ändringar som du gör i containern  inifrån konsolen bevaras inte när appen startas om (förutom ändringar i den delade lagringen), eftersom den inte ingår i Docker-avbildningen. Om du vill bevara dina ändringar, till exempel registerinställningar och programvaruinstallation, gör dem till en del av Dockerfile.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

App Service loggar åtgärder från Docker-värden samt aktiviteter från containern. Loggar från Docker-värden (plattformsloggar) levereras som standard, men programloggar eller webbserverloggar från containern måste aktiveras manuellt. Mer information finns i Aktivera [programloggning och](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) [Aktivera webbserverloggning.](troubleshoot-diagnostic-logs.md#enable-web-server-logging) 

Det finns flera sätt att komma åt Docker-loggar:

- [I Azure Portal](#in-azure-portal)
- [Från Kudu-konsolen](#from-the-kudu-console)
- [Med Kudu-API:et](#with-the-kudu-api)
- [Skicka loggar till Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>I Azure Portal

Docker-loggar visas i portalen på sidan **Containerinställningar** i din app. Loggarna trunkeras, men du kan ladda ned alla loggar genom att klicka på **Ladda ned.** 

### <a name="from-the-kudu-console"></a>Från Kudu-konsolen

Gå till `https://<app-name>.scm.azurewebsites.net/DebugConsole` och klicka på mappen **LogFiles** för att se de enskilda loggfilerna. Om du vill ladda **ned hela LogFiles-katalogen** klickar **du på** nedladdningsikonen till vänster om katalognamnet. Du kan också komma åt den här mappen med hjälp av en FTP-klient.

I konsolterminalen kan du inte komma åt mappen `C:\home\LogFiles` som standard eftersom beständig delad lagring inte är aktiverat. Aktivera det här beteendet i konsolterminalen genom [att aktivera beständig delad lagring](#use-persistent-shared-storage).

Om du försöker ladda ned Den Docker-logg som för närvarande används med hjälp av en FTP-klient kan du få ett felmeddelande på grund av ett fillås.

### <a name="with-the-kudu-api"></a>Med Kudu-API:et

Gå direkt till `https://<app-name>.scm.azurewebsites.net/api/logs/docker` för att se metadata för Docker-loggarna. Du kan se fler än en loggfil i listan och med `href` egenskapen kan du ladda ned loggfilen direkt. 

Om du vill ladda ned alla loggar tillsammans i en ZIP-fil öppnar du `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Anpassa containerminne

Som standard är alla Windows-containrar som distribueras i Azure App Service begränsade till 1 GB RAM-minne. Du kan ändra det här värdet genom att `WEBSITE_MEMORY_LIMIT_MB` ange appinställningen via [Cloud Shell](https://shell.azure.com). I Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Värdet definieras i MB och måste vara mindre och lika med det totala fysiska minnet för värden. I till exempel en App Service plan med 8 GB RAM får den kumulativa summan för för alla `WEBSITE_MEMORY_LIMIT_MB` appar inte överskrida 8 GB. Information om hur mycket minne som är tillgängligt för varje prisnivå finns [i App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)i avsnittet Premium **Container (Windows)-plan.**

## <a name="customize-the-number-of-compute-cores"></a>Anpassa antalet beräkningskärnor

Som standard körs en Windows-container med alla tillgängliga kärnor för din valda prisnivå. Du kanske till exempel vill minska antalet kärnor som din mellanlagringsplats använder. Om du vill minska antalet kärnor som används av en container anger du `WEBSITE_CPU_CORES_LIMIT` appinställningen till önskat antal kärnor. Du kan ange det via [Cloud Shell](https://shell.azure.com). I Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Uppdatering av appinställningen utlöser automatisk omstart, vilket orsakar minimal avbrottstid. För en produktionsapp bör du överväga att växla den till en mellanlagringsplats, ändra appinställningen i mellanlagringsplatsen och sedan växla tillbaka den till produktion.

Kontrollera det justerade numret genom att gå till Kudu-konsolen ( `https://<app-name>.scm.azurewebsites.net` ) och skriva följande kommandon med hjälp av PowerShell. Varje kommando matar ut ett tal.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Processorerna kan vara processorer med flera kärnor eller hypertrådning. Information om hur många kärnor som är tillgängliga för varje prisnivå finns [i App Service i](https://azure.microsoft.com/pricing/details/app-service/windows/)avsnittet Premium Container **(Windows)-plan.**

## <a name="customize-health-ping-behavior"></a>Anpassa beteende för hälso ping

App Service anser att en container har startats korrekt när containern startar och svarar på en HTTP-ping. Begäran om hälso ping innehåller -huvudet `User-Agent= "App Service Hyper-V Container Availability Check"` . Om containern startar men inte svarar på en ping efter en viss tid, loggar App Service en händelse i Docker-loggen och säger att containern inte startat. 

Om programmet är resurskrävande kanske containern inte svarar på HTTP-ping i tid. Om du vill styra åtgärderna när HTTP-pingar misslyckas anger du `CONTAINER_AVAILABILITY_CHECK_MODE` appinställningen. Du kan ange det via [Cloud Shell](https://shell.azure.com). I Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

I följande tabell visas möjliga värden:

| Värde | Beskrivningar |
| - | - |
| **Reparation** | Starta om containern efter tre efterföljande tillgänglighetskontroller |
| **ReportOnly** | Standardvärdet. Starta inte om containern utan rapportera i Docker-loggarna för containern efter tre efterföljande tillgänglighetskontroller. |
| **Av** | Kontrollera inte tillgängligheten. |

## <a name="support-for-group-managed-service-accounts"></a>Stöd för grupp-hanterade tjänstkonton

Grupp hanterade tjänstkonton (gMSA) stöds för närvarande inte i Windows-containrar i App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Aktivera SSH

SSH möjliggör säker kommunikation mellan en container och en klient. För att en anpassad container ska ha stöd för SSH måste du lägga till den i själva Docker-avbildningen.

> [!TIP]
> Alla inbyggda Linux-containrar i App Service har lagt till SSH-instruktionerna i sina avbildningsdatabaser. Du kan gå igenom följande instruktioner medNode.js [ 10.14-lagringsplatsen](https://github.com/Azure-App-Service/node/blob/master/10.14) för att se hur den är aktiverad där. Konfigurationen i den Node.js inbyggda avbildningen är något annorlunda, men i princip densamma.

- Lägg [till sshd_config-fil](https://man.openbsd.org/sshd_config) till lagringsplatsen, som i följande exempel.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Den här filen konfigurerar OpenSSH och måste innehålla följande objekt:
    > - `Port` måste anges till 2222.
    > - `Ciphers`måste innehålla minst ett objekt i listan: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`måste innehålla minst ett objekt i listan: `hmac-sha1,hmac-sha1-96`.

- Lägg till följande kommandon i din Dockerfile:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Den här konfigurationen tillåter inte externa anslutningar till containern. Port 2222 i containern är endast tillgänglig i bryggnätverket i ett privat virtuellt nätverk och är inte tillgänglig för en angripare på Internet.

- Starta SSH-servern i startskriptet för din container.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurera appar med flera containrar

- [Använda beständig lagring i Docker Compose](#use-persistent-storage-in-docker-compose)
- [Begränsningar för förhandsversion](#preview-limitations)
- [Docker Compose-alternativ](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Använda beständig lagring i Docker Compose

Appar med flera containrar som WordPress behöver beständig lagring för att fungera korrekt. Om du vill aktivera det måste Docker Compose-konfigurationen peka på en *lagringsplats utanför* containern. Lagringsplatser i containern bevarar inte ändringar utöver appstart.

Aktivera beständig lagring genom att ange appinställningen med kommandot `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) i [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

I filen *docker-compose.yml* mappar du `volumes` alternativet till `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` är en miljövariabel i App Service som är mappad till beständig lagring för din app. Exempel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Begränsningar för förhandsversion

Flera containrar är för närvarande i förhandsversion. Följande funktioner App Service plattform stöds inte:

- Autentisering/auktorisering
- Hanterade identiteter
- CORS

### <a name="docker-compose-options"></a>Docker Compose-alternativ

Följande listor visar konfigurationsalternativ som stöds och som inte stöds:

#### <a name="supported-options"></a>Alternativ som stöds

- command
- entrypoint
- miljö
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Alternativ som inte stöds

- build (inte tillåtet)
- depends_on (ignoreras)
- networks (ignoreras)
- secrets (ignoreras)
- andra portar än 80 och 8080 (ignoreras)

> [!NOTE]
> Alla andra alternativ som inte uttryckligen anges ignoreras i den allmänt tillgängliga förhandsversionen.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Migrera anpassad programvara till Azure App Service en anpassad container](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Självstudie: WordPress-app med flera containrar](tutorial-multi-container-app.md)

::: zone-end

Eller så kan du se ytterligare resurser:

[Läsa in certifikat i Windows/Linux-containrar](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
