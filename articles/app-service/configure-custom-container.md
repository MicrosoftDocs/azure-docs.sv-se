---
title: Konfigurera en anpassad behållare
description: Lär dig hur du konfigurerar en anpassad behållare i Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 8083c3c0c88d904ccb3ec75ae69a699867bd0f25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704879"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Konfigurera en anpassad container för Azure App Service

Den här artikeln visar hur du konfigurerar en anpassad behållare som ska köras på Azure App Service.

::: zone pivot="container-windows"

Den här guiden innehåller viktiga begrepp och instruktioner för skapa behållare av Windows-appar i App Service. Om du aldrig har använt Azure App Service följer du snabb starten och [självstudien](tutorial-custom-container.md) för [egen behållare](quickstart-custom-container.md) först.

::: zone-end

::: zone pivot="container-linux"

Den här guiden innehåller viktiga begrepp och instruktioner för skapa behållare av Linux-appar i App Service. Om du aldrig har använt Azure App Service följer du snabb starten och [självstudien](tutorial-custom-container.md) för [egen behållare](quickstart-custom-container.md) först. Det finns också en [snabb start](quickstart-multi-container.md) och [självstudier](tutorial-multi-container-app.md)för flera behållare.

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Överordnade avbildningar som stöds

För din anpassade Windows-avbildning måste du välja den högra [överordnade avbildningen (bas avbildningen)](https://docs.docker.com/develop/develop-images/baseimages/) för det ramverk som du vill använda:

- Om du vill distribuera .NET Framework appar använder du en överordnad avbildning som baseras på Windows Server Core [Long-term Servicing Channel (LTSC)-](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) versionen. 
- Om du vill distribuera .NET Core-appar använder du en överordnad avbildning som baseras på Windows Server nano-versionen av Windows Server nano [halvårs kanal (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) . 

Det tar lite tid att ladda ned en överordnad avbildning när appen startas. Men du kan minska starttiden genom att använda någon av följande överordnade avbildningar som redan har cachelagrats i Azure App Service:

- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.Microsoft.com/dotNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.Microsoft.com/dotNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.Microsoft.com/dotNet/Core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotNet/Core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotNet/Core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotNet/Core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1809
- [MCR.Microsoft.com/dotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Ändra Docker-avbildningen av en anpassad behållare

Om du vill ändra en befintlig anpassad container-app från den aktuella Docker-avbildningen till en ny avbildning, använder du följande kommando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Använda en avbildning från ett privat register

Om du vill använda en avbildning från ett privat register, till exempel Azure Container Registry, kör du följande kommando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

För *\<username>* och *\<password>* anger du inloggnings uppgifterna för ditt privata register konto.

## <a name="i-dont-see-the-updated-container"></a>Jag ser inte den uppdaterade behållaren

Om du ändrar inställningarna för din Docker-behållare så att de pekar på en ny behållare kan det ta några minuter innan appen hanterar HTTP-begäranden från den nya behållaren. Medan den nya behållaren hämtas och startas, App Service fortsätta att hantera begär Anden från den gamla behållaren. När den nya behållaren har startats och är redo att ta emot begär Anden börjar App Service skicka begär anden till den.

## <a name="how-container-images-are-stored"></a>Hur behållar avbildningar lagras

Första gången du kör en anpassad Docker-avbildning i App Service gör App Service en `docker pull` och hämtar alla bild lager. Dessa lager lagras på disk, precis som om du använder Docker lokalt. Varje gången appen startas om gör App Service en `docker pull` , men hämtar bara lager som har ändrats. Om inga ändringar har gjorts använder App Service befintliga lager på den lokala disken.

Om appen ändrar beräknings instanser av någon anledning, som att skala upp och ned pris nivåerna, måste App Service hämta alla lager igen. Samma sak gäller om du skalar ut för att lägga till fler instanser. Det finns också sällsynta fall där app-instanserna kan ändras utan en skalnings åtgärd.

## <a name="configure-port-number"></a>Konfigurera port nummer

Som standard förutsätter App Service att din anpassade behållare lyssnar på port 80. Om din behållare lyssnar på en annan port anger du `WEBSITES_PORT` appens inställning i App Service-appen. Du kan ställa in den via [Cloud Shell](https://shell.azure.com). I bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service tillåter för närvarande att din behållare endast exponerar en port för HTTP-begäranden. 

## <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Din anpassade behållare kan använda miljövariabler som behöver anges externt. Du kan skicka dem via [Cloud Shell](https://shell.azure.com). I bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

När din app körs, matas App Service app-inställningarna in i processen som miljövariabler automatiskt. Du kan kontrol lera variabler för container miljön med URL: en `https://<app-name>.scm.azurewebsites.net/Env)` .

::: zone pivot="container-windows"
För IIS-eller .NET Framework (4,0 eller senare)-baserade behållare matas de in i `System.ConfigurationManager` som inställningar och anslutnings strängar för .net-appar automatiskt av App Service. För alla andra språk och ramverk är de tillgängliga som miljövariabler för processen, med något av följande motsvarande prefix:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Den här metoden fungerar både för appar med en container eller flera behållare, där miljövariablerna anges i filen *filen Docker. yml* .

::: zone-end

## <a name="use-persistent-shared-storage"></a>Använd beständig delad lagring

::: zone pivot="container-windows"

Du kan använda *C:\home* -katalogen i appens fil system för att spara filer mellan omstarter och dela dem över instanser. `C:\home`I din app kan du använda behållar appen för att få åtkomst till beständig lagring.

När beständigt lagrings utrymme är inaktiverat `C:\home` behålls inte skrivningar till katalogen. [Docker-värd loggar och behållar loggar](#access-diagnostic-logs) sparas i en standard beständig delad lagring som inte är kopplad till behållaren. När beständig lagring har Aktiver ATS sparas alla skrivningar till `C:\home` katalogen och de kan nås av alla instanser av en utskalad app och loggen är tillgänglig i `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

Du kan använda */Home* -katalogen i appens fil system för att spara filer mellan omstarter och dela dem över instanser. `/home`I din app kan du använda behållar appen för att få åtkomst till beständig lagring.

När beständig lagring är inaktive rad `/home` behålls inte skrivningar till katalogen i appens omstarter eller över flera instanser. Det enda undantaget är `/home/LogFiles` katalogen som används för att lagra Docker-och container-loggar. När beständig lagring är aktive rad är alla skrivningar till `/home` katalogen bestående och kan nås av alla instanser av en utskalad app.

::: zone-end

Beständig lagring är som standard inaktive rad och inställningen exponeras inte i appens inställningar. Om du vill aktivera det anger du `WEBSITES_ENABLE_APP_SERVICE_STORAGE` appens inställning via [Cloud Shell](https://shell.azure.com). I bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Du kan också [Konfigurera en egen beständig lagring](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

App Service avslutar TLS/SSL på klient sidan. Det innebär att TLS/SSL-begäranden aldrig kommer till din app. Du behöver inte, och ska inte implementera stöd för TLS/SSL i din app. 

Klient delar finns i Azures Data Center. Om du använder TLS/SSL med din app, kommer trafiken över Internet alltid att krypteras på ett säkert sätt.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Anpassa ASP.NET Machine Key insprutning

 Automatiskt skapade nycklar matas in i behållaren som dator nycklar för ASP.NET kryptografiska rutiner under behållarens start. Du [hittar dessa nycklar i din behållare](#connect-to-the-container) genom att leta efter följande miljövariabler: `MACHINEKEY_Decryption` , `MACHINEKEY_DecryptionKey` , `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

De nya nycklarna vid varje omstart kan återställa ASP.NET formulär-autentisering och Visa status, om din app är beroende av dem. För att förhindra automatisk omgenerering av nycklar [ställer du in dem manuellt som App Service app-inställningar](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Anslut till behållaren

Du kan ansluta till din Windows-behållare direkt för diagnostiska uppgifter genom att gå till `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Så här fungerar det:

- Med fel söknings konsolen kan du köra interaktiva kommandon, till exempel starta PowerShell-sessioner, inspektera register nycklar och navigera i hela behållar fil systemet.
- Den fungerar separat från den grafiska webbläsaren ovanför, som bara visar filerna i din [delade lagring](#use-persistent-shared-storage).
- I en utskalad app är fel söknings konsolen ansluten till en av behållar instanserna. Du kan välja en annan instans i list rutan **instans** på den översta menyn.
- Alla ändringar du gör i behållaren från-konsolen behålls *inte* när din app startas om (förutom vid ändringar i den delade lagringen), eftersom den inte är en del av Docker-avbildningen. Om du vill spara ändringarna, till exempel register inställningar och program varu installation, så gör du dem till en del av Dockerfile.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

App Service loggar åtgärder av Docker-värden samt aktiviteter i behållaren. Loggar från Docker-värden (plattforms loggar) levereras som standard, men program loggar eller webb server loggar inifrån behållaren måste aktive ras manuellt. Mer information finns i [Aktivera program loggning](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) och [Aktivera loggning av webb server](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

Det finns flera sätt att komma åt Docker-loggar:

- [I Azure Portal](#in-azure-portal)
- [Från kudu-konsolen](#from-the-kudu-console)
- [Med kudu-API: et](#with-the-kudu-api)
- [Skicka loggar till Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>I Azure Portal

Docker-loggar visas i portalen på sidan **behållar inställningar** i din app. Loggarna trunkeras, men du kan ladda ned alla loggar genom att klicka på **Hämta**. 

### <a name="from-the-kudu-console"></a>Från kudu-konsolen

Navigera till `https://<app-name>.scm.azurewebsites.net/DebugConsole` och klicka på mappen **loggfiler** för att se de enskilda loggfilerna. Om du vill ladda ned hela **LogFiles** -katalogen klickar du på ikonen **Ladda ned** till vänster om katalog namnet. Du kan också komma åt den här mappen med en FTP-klient.

I konsolens Terminal har du inte åtkomst till `C:\home\LogFiles` mappen som standard eftersom beständig delad lagring inte är aktive rad. Aktivera det här beteendet i konsolens Terminal genom att [Aktivera beständig delad lagring](#use-persistent-shared-storage).

Om du försöker hämta Docker-loggen som används för närvarande med en FTP-klient kan du få ett fel meddelande på grund av ett fil låsning.

### <a name="with-the-kudu-api"></a>Med kudu-API: et

Navigera direkt till `https://<app-name>.scm.azurewebsites.net/api/logs/docker` för att se metadata för Docker-loggarna. Du kan se fler än en loggfil i listan, och `href` egenskapen låter dig hämta logg filen direkt. 

För att hämta alla loggar tillsammans i en ZIP-fil, åtkomst `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Anpassa container minne

Som standard är alla Windows-behållare som distribueras i Azure App Service begränsade till 1 GB RAM-minne. Du kan ändra det här värdet genom att ange `WEBSITE_MEMORY_LIMIT_MB` app-inställningen via [Cloud Shell](https://shell.azure.com). I bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Värdet definieras i MB och måste vara mindre än och lika med värdens totala fysiska minne. I en App Service plan med 8 GB RAM-minne får det totala antalet `WEBSITE_MEMORY_LIMIT_MB` för alla appar inte överstiga 8 GB. Information om hur mycket minne som är tillgängligt för varje pris nivå finns i [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/windows/)i avsnittet **Premium container (Windows) plan** .

## <a name="customize-the-number-of-compute-cores"></a>Anpassa antalet beräknings kärnor

Som standard körs en Windows-behållare med alla tillgängliga kärnor för den valda pris nivån. Du kanske vill minska antalet kärnor som mellanlagrings platsen använder, till exempel. Om du vill minska antalet kärnor som används av en behållare ställer du in `WEBSITE_CPU_CORES_LIMIT` app-inställningen på det önskade antalet kärnor. Du kan ställa in den via [Cloud Shell](https://shell.azure.com). I bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Uppdatering av app-inställningen utlöser automatisk omstart, vilket ger minimal nedtid. För en app för produktion kan du byta ut den till en mellanlagringsplats, ändra app-inställningen på mellanlagringsplatsen och sedan växla tillbaka till produktion.

Verifiera det justerade antalet genom att gå till kudu-konsolen ( `https://<app-name>.scm.azurewebsites.net` ) och skriva följande kommandon med hjälp av PowerShell. Varje kommando matar ut ett tal.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Processorerna kan vara multicore-eller hyperthreading-processorer. Information om hur många kärnor som är tillgängliga för varje pris nivå finns i [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/windows/)i avsnittet **Premium container (Windows) plan** .

## <a name="customize-health-ping-behavior"></a>Anpassa ping-beteende för hälsa

App Service anser att en behållare har startats när behållaren startar och svarar på en HTTP-ping. Hälso-ping-begäran innehåller rubriken `User-Agent= "App Service Hyper-V Container Availability Check"` . Om behållaren startar men inte svarar på ett ping efter en viss tid, App Service loggar en händelse i Docker-loggen, vilket säger att behållaren inte startades. 

Om ditt program är resurs intensivt kanske behållaren inte svarar på HTTP-ping i tid. Om du vill kontrol lera åtgärderna när HTTP-pingar inte fungerar anger du `CONTAINER_AVAILABILITY_CHECK_MODE` appens inställning. Du kan ställa in den via [Cloud Shell](https://shell.azure.com). I bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

I PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

I följande tabell visas möjliga värden:

| Värde | Förklaringar |
| - | - |
| **Hjälp** | Starta om behållaren efter tre tillgänglighets kontroller i följd |
| **ReportOnly** | Standardvärdet. Starta inte om behållaren men rapportera i Docker-loggarna för behållaren efter tre tillgänglighets kontroller i följd. |
| **Av** | Sök inte efter tillgänglighet. |

## <a name="support-for-group-managed-service-accounts"></a>Stöd för grupphanterade tjänst konton

Grupphanterade tjänst konton (gMSAs) stöds för närvarande inte i Windows-behållare i App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Aktivera SSH

SSH möjliggör säker kommunikation mellan en container och en klient. För att en anpassad behållare ska stödja SSH måste du lägga till den i Docker-avbildningen.

> [!TIP]
> Alla inbyggda Linux-behållare i App Service har lagt till SSH-instruktionerna i sina avbildnings databaser. Du kan gå igenom följande instruktioner med [Node.js 10,14-lagringsplatsen](https://github.com/Azure-App-Service/node/blob/master/10.14) för att se hur den är aktive rad där. Konfigurationen i den Node.js inbyggda avbildningen är något annorlunda, men samma princip.

- Lägg till [en sshd_config-fil](https://man.openbsd.org/sshd_config) till din lagrings plats, som i följande exempel.

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
    > - `Port` måste vara inställt på 2222.
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

    Den här konfigurationen tillåter inte externa anslutningar till behållaren. Port 2222 i behållaren är bara tillgänglig i brygga nätverket i ett privat virtuellt nätverk och är inte tillgänglig för en angripare på Internet.

- Starta SSH-servern i Start skriptet för din behållare.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurera appar med flera behållare

- [Använd beständigt lagrings utrymme i Docker Compose](#use-persistent-storage-in-docker-compose)
- [För hands versions begränsningar](#preview-limitations)
- [Docker Skriv alternativ](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Använd beständigt lagrings utrymme i Docker Compose

Appar med flera behållare som WordPress behöver beständig lagring för att fungera korrekt. För att aktivera den måste Docker-konfigurationen peka på en lagrings plats *utanför* din behållare. Lagrings platser i din behållare har inte kvar ändringar än starta om appar.

Aktivera beständig lagring genom att ställa in `WEBSITES_ENABLE_APP_SERVICE_STORAGE` appens inställning med hjälp av kommandot [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) i [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

I filen *filen Docker. yml* mappar du `volumes` alternativet till `${WEBAPP_STORAGE_HOME}` . 

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

Multi-container är för närvarande en för hands version. Följande App Service plattforms funktioner stöds inte:

- Autentisering/auktorisering
- Hanterade identiteter
- CORS

### <a name="docker-compose-options"></a>Docker Skriv alternativ

I följande listor visas en Docker-konfigurations alternativ som stöds och inte stöds:

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
> Andra alternativ som inte uttryckligen anropas ignoreras i den offentliga för hands versionen.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Migrera anpassad program vara till Azure App Service med en anpassad behållare](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Självstudie: WordPress-app med flera behållare](tutorial-multi-container-app.md)

::: zone-end

Eller, se ytterligare resurser:

[Läs in certifikat i Windows/Linux-behållare](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
