---
title: 'Självstudie: Skapa och köra en anpassad avbildning i Azure App Service'
description: En stegvis guide för att skapa en anpassad Linux- eller Windows-avbildning, push-installera avbildningen till Azure Container Registry och sedan distribuera avbildningen till Azure App Service. Lär dig hur du migrerar anpassade program till App Service i en anpassad container.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: azure app service, web app, linux, windows, docker, container
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 0770b46a60f497d3a3da772e7be13ece0526eca0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765711"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migrera anpassad programvara till Azure App Service en anpassad container

::: zone pivot="container-windows"  

[Azure App Service](overview.md) har fördefinierade programstackar i Windows som ASP.NET eller Node.js, som körs i IIS. Den förkonfigurerade Windows-miljön låser operativsystemet från administrativ åtkomst, programinstallationer, ändringar av den globala sammansättningscachen och så vidare (se [Operativsystemfunktioner i Azure App Service](operating-system-functionality.md)). Men genom att använda en anpassad Windows-container i App Service kan du göra nödvändiga operativsystemändringar som appen behöver, vilket gör det enkelt att migrera en lokal app som kräver en anpassad operativsystem- och programvarukonfiguration. Den här kursen beskriver hur du migrerar en ASP.NET-app till App Service som använder anpassade teckensnitt som installeras i Windows-teckensnittsbiblioteket. Du distribuerar en Windows-avbildning med en anpassad konfiguration från Visual Studio till [Azure Container Registry](../container-registry/index.yml) och kör den sedan i App Service.

![Visar webbappen som körs i en Windows-container.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- <a href="https://hub.docker.com/" target="_blank">Registrera dig för ett Docker Hub-konto</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installera Docker för Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Växla Docker för att köra Windows-containrar</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installera Visual Studio 2019 med</a> arbetsbelastningarna **ASP.NET webbutveckling och** **Azure-utveckling.** Om du redan har installerat Visual Studio 2019:
    - Installera de senaste uppdateringarna i Visual Studio genom att klicka **på Hjälp** sök  >  **efter uppdateringar.**
    - Lägg till arbetsbelastningarna i Visual Studio klicka på **Verktyg**  >  **Hämta verktyg och funktioner**.

## <a name="set-up-the-app-locally"></a>Konfigurera appen lokalt

### <a name="download-the-sample"></a>Ladda ned exemplet

I det här steget konfigurerar du det lokala .NET-projektet.

- [Ladda ned exempelprojektet](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Extrahera (packa upp) filen *custom-font-win-container.zip*.

Exempelprojektet innehåller ett enkelt ASP.NET-program som använder ett anpassat teckensnitt som installeras i Windows-teckensnittsbiblioteket. Det är inte nödvändigt att installera teckensnitt, men det är ett exempel på en app som är integrerad med det underliggande operativsystemet. För att migrera den här typen av app till App Service måste du antingen omorganisera din kod för att ta bort integrationen eller migrera den som den är i en anpassad Windows-container.

### <a name="install-the-font"></a>Installera teckensnittet

I Utforskaren navigerar du till _custom-font-win-container-master/CustomFontSample_, högerklickar på _FrederickatheGreat-Regular.ttf_ och väljer **Installera**.

Det här teckensnittet är offentligt tillgängligt från [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Kör appen

Öppna filen *custom-font-win-container/CustomFontSample.sln* i Visual Studio. 

Skriv `Ctrl+F5` för att köra appen utan felsökning. Appen visas i din standardwebbläsare. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Skärmbild som visar appen i standardwebbläsaren.":::

Eftersom den använder ett installerat teckensnitt kan inte appen köras i sandbox-miljön för App Service. Du kan dock distribuera den med hjälp av en Windows-container i stället, eftersom du kan installera teckensnittet i Windows-containern.

### <a name="configure-windows-container"></a>Konfigurera Windows-containern

I Solution Explorer högerklickar du på projektet **CustomFontSample** och väljer **Lägg till** > **Container Orchestration Support** (Stöd för containerorkestrering).

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Skärmbild av fönstret Solution Explorer visar de valda menyalternativen För CustomFontSample-projekt, Lägg till och Container Orchestrator.":::

Välj **Docker Compose**  >  **OK**.

Nu har projektet konfigurerats för att köra i en Windows-container. En _Dockerfile_ läggs till i **CustomFontSample**-projektet och ett **docker-compose**-projekt läggs till i lösningen. 

Från Solution Explorer öppnar du **Dockerfile**.

Du måste använda en [överordnad avbildning som stöds](configure-custom-container.md#supported-parent-images). Ändra den överordnade avbildningen genom att ersätta raden `FROM` med följande kod:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Lägg till följande rad i slutet av filen och spara filen:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

Du hittar _InstallFont.ps1_ i projektet **CustomFontSample**. Det är ett enkelt skript som installerar teckensnittet. Du hittar en mer komplex version av skriptet i [Skriptcenter](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Om du vill testa Windows-containern lokalt kontrollerar du att Docker har startats på den lokala datorn.
>

## <a name="publish-to-azure-container-registry"></a>Publicera till Azure Container Registry

[Azure Container Registry](../container-registry/index.yml) kan lagra dina avbildningar för containerdistribution. Du kan konfigurera App Service att använda avbildningar i Azure Container Registry.

### <a name="open-publish-wizard"></a>Öppna publiceringsguiden

I Solution Explorer högerklickar du på projektet **CustomFontSample** och väljer **Publicera**.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Skärmbild av Solution Explorer visar CustomFontSample-projektet och Publicera valt.":::

### <a name="create-registry-and-publish"></a>Skapa register och publicera

I publiceringsguiden väljer du **Container Registry**  >  **Skapa ny Azure Container Registry**  >  **Publicera.**

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Skärmbild av publiceringsguiden som visar Container Registry, Skapa Azure Container Registry och knappen Publicera markerad.":::

### <a name="sign-in-with-azure-account"></a>Logga in med Azure-konto

I dialogrutan **Create a new Azure Container Registry** (Skapa nytt Azure-containerregister) väljer du **Lägg till ett konto** och loggar in till din Azure-prenumeration. Välj det konto som innehåller den önskade prenumerationen i listrutan om du redan är inloggad.

![Logga in på Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Konfigurera registret

Konfigurera det nya containerregistret baserat på de föreslagna värdena i tabellen nedan. Klicka på **Skapa** när du är klar.

| Inställning  | Föreslaget värde | Mer information |
| ----------------- | ------------ | ----|
|**DNS-prefix**| Behåll det genererade registernamnet eller ändra det till ett annat unikt namn. |  |
|**Resursgrupp**| Klicka på **Nytt**, skriv **myResourceGroup** och klicka på **OK**. |  |
|**SKU**| Grundläggande | [Prisnivåer](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Registerplats**| Europa, västra | |

![Konfigurera Azure-containerregister](./media/tutorial-custom-container/configure-registry.png)

Ett terminalfönster öppnas och visar förloppet för avbildningsdistributionen. Vänta tills distributionen har slutförts.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-web-app"></a>Skapa en webbapp

I den vänstra menyn väljer du **Skapa en resurs**  >  **Web**  >  **Web App for Containers**.

### <a name="configure-app-basics"></a>Konfigurera appens grunder

På **fliken Grundläggande konfigurerar** du inställningarna enligt följande tabell och klickar sedan på **Nästa: Docker**.

| Inställning  | Föreslaget värde | Mer information |
| ----------------- | ------------ | ----|
|**Prenumeration**| Kontrollera att rätt prenumeration har valts. |  |
|**Resursgrupp**| Välj **Skapa ny,** skriv **myResourceGroup** och klicka på **OK.** |  |
|**Namn**| Skriv ett unikt namn. | Webbadressen till webbappen är `http://<app-name>.azurewebsites.net`, där `<app-name>` är appens namn. |
|**Publicera**| Docker-container | |
|**Operativsystem**| Windows | |
|**Region**| Europa, västra | |
|**Windows-plan**| Välj **Skapa ny,** skriv **myAppServicePlan och** klicka på **OK.** | |

Fliken **Grundläggande bör** se ut så här:

![Visar fliken Grundläggande inställningar som används för att konfigurera webbappen.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Konfigurera Windows-containern

På fliken **Docker** konfigurerar du din anpassade Windows-container enligt följande tabell och väljer **Granska + skapa.**

| Inställning  | Föreslaget värde |
| ----------------- | ------------ |
|**Bildkälla**| Azure Container Register |
|**Register**| Välj [det register som du skapade tidigare.](#publish-to-azure-container-registry) |
|**Bild**| customfontsample |
|**Tag**| senaste |

### <a name="complete-app-creation"></a>Slutför appgenereringen

Klicka på **Skapa** och vänta på att Azure skapar resurserna som krävs.

## <a name="browse-to-the-web-app"></a>Bläddra till webbappen

När Azure-åtgärden är klar visas ett meddelande.

![Visar att Azure-åtgärden har slutförts.](media/tutorial-custom-container/portal-create-finished.png)

1. Klicka på **Gå till resurs**.

2. På appsidan klickar du på länken under **URL**.

En ny webbläsarsida öppnas på följande sida:

![Visar den nya webbläsarsidan för webbappen.](media/tutorial-custom-container/app-starting.png)

Vänta några minuter och försök igen tills startsidan visas med det snygga teckensnitt du förväntar dig:

![Visar startsidan med det teckensnitt som du har konfigurerat.](media/tutorial-custom-container/app-running.png)

**Grattis!** Du har migrerat ett ASP.NET-program till Azure App Service i en Windows-container.

## <a name="see-container-start-up-logs"></a>Se containerns startloggar

Det kan ta lite tid för Windows-containern att läsas in. Om du vill se förloppet går du till följande URL genom *\<app-name>* att ersätta med namnet på din app.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

De strömmade loggarna ser ut så här:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service använder Docker-containertekniken som värd för både inbyggda avbildningar och anpassade avbildningar. Om du vill se en lista över inbyggda avbildningar kör du Azure CLI-kommandot [az webapp list-runtimes --linux.](/cli/azure/webapp#az_webapp_list_runtimes) Om avbildningarna inte uppfyller dina behov kan du skapa och distribuera en anpassad avbildning.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en anpassad avbildning om ingen inbyggd avbildning uppfyller dina behov
> * Push-skicka den anpassade avbildningen till ett privat containerregister i Azure
> * Kör den anpassade avbildningen i App Service
> * Konfigurera miljövariabler
> * Uppdatera och distribuera om avbildningen
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

Den här självstudien medför en liten avgift på ditt Azure-konto för containerregistret och kan medföra ytterligare kostnader för att vara värd för containern längre än en månad.

## <a name="set-up-your-initial-environment"></a>Konfigurera din första miljö

- Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Installera [Docker](https://docs.docker.com/get-started/#setup), som du använder för att skapa Docker-avbildningar. Installation av Docker kan kräva en omstart av datorn.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Den här självstudien kräver version 2.0.80 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

När du har installerat Docker eller Azure Cloud Shell öppnar du ett terminalfönster och kontrollerar att Docker är installerat:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Klona eller ladda ned exempelappen

Du kan hämta exemplet för den här självstudien via git-kloning eller nedladdning.

### <a name="clone-with-git"></a>Klona med git

Klona exempeldatabasen:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Se till att inkludera `--config core.autocrlf=input` argumentet för att garantera rätt radslut i filer som används i Linux-containern:

Gå sedan till den mappen:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Ladda ned från GitHub

I stället för att använda git-klonen kan [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) du gå till , välja **Klona** och sedan **välja Ladda ned ZIP.** 

Packa upp ZIP-filen i en mapp med *namnet docker-django-webapp-linux*. 

Öppna sedan ett terminalfönster i mappen *docker-django-webapp-linux.*

## <a name="optional-examine-the-docker-file"></a>(Valfritt) Granska Docker-filen

Filen i exemplet med namnet _Dockerfile som_ beskriver Docker-avbildningen och innehåller konfigurationsanvisningar:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* Den första gruppen med kommandon installerar appens krav i miljön.
* Den andra gruppen med kommandon skapar en [SSH-server](https://www.ssh.com/ssh/protocol/) för säker kommunikation mellan containern och värden.
* Den sista `ENTRYPOINT ["init.sh"]` raden, , anropar `init.sh` för att starta SSH-tjänsten och Python-servern.

## <a name="build-and-test-the-image-locally"></a>Skapa och testa avbildningen lokalt

> [!NOTE]
> Docker Hub har kvoter för antalet [anonyma pull-överföringar per IP-adress ](https://www.docker.com/pricing)och antalet autentiserade pull-överföringar per kostnadsfri användare (se Dataöverföring ). Om du märker att pull-Docker Hub är begränsade kan du `docker login` prova om du inte redan är inloggad.
> 

1. Kör följande kommando för att skapa avbildningen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Testa att bygget fungerar genom att köra Docker-containern lokalt:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Det [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) här kommandot anger porten med argumentet följt av namnet på `-p` avbildningen. 
    
    > [!TIP]
    > Om du kör på Windows och ser felet *standard_init_linux.go:211: exec user process caused "no such file or directory"*(Ingen sådan fil eller katalog) innehåller *init.sh-filen* CR-LF-radslut i stället för förväntade LF-slut. Det här felet inträffar om du använde git för att klona exempeldatabasen men utelämnade `--config core.autocrlf=input` parametern . I det här fallet klonar du lagringsplatsen igen med argumentet "--config". Du kan också se felet om du har redigerat *init.sh* sparat det med CRLF-slut. I det här fallet sparar du filen igen med endast LF-ändelse.

1. Bläddra till `http://localhost:8000` för att kontrollera att webbappen och containern fungerar korrekt.

    ![Testa webbappen lokalt](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I det här avsnittet och de som följer etablerar du resurser i Azure som du skickar avbildningen till och distribuerar sedan en container till Azure App Service. Du börjar med att skapa en resursgrupp där du samlar in alla dessa resurser.

Kör kommandot [az group create](/cli/azure/group#az_group_create) för att skapa en resursgrupp:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Du kan ändra värdet `--location` för att ange en region nära dig.

## <a name="push-the-image-to-azure-container-registry"></a>Push-skicka avbildningen till Azure Container Registry

I det här avsnittet push-installerar du avbildningen till Azure Container Registry där App Service kan distribuera den.

1. Kör kommandot [`az acr create`](/cli/azure/acr#az_acr_create) för att skapa en Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Ersätt `<registry-name>` med ett lämpligt namn för registret. Namnet får bara innehålla bokstäver och siffror och måste vara unikt i hela Azure.

1. Kör kommandot [`az acr show`](/cli/azure/acr#az_acr_show) för att hämta autentiseringsuppgifter för registret:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    JSON-utdata för det här kommandot innehåller två lösenord tillsammans med registrets användarnamn.
    
1. Använd kommandot `docker login` för att logga in på containerregistret:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Ersätt `<registry-name>` och med värden från föregående `<registry-username>` steg. När du uppmanas till det skriver du ett av lösenorden från föregående steg.

    Du använder samma registernamn i alla återstående steg i det här avsnittet.

1. När inloggningen har lyckats taggar du den lokala Docker-avbildningen för registret:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Använd kommandot `docker push` för att skicka avbildningen till registret:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Det kan ta några minuter att ladda upp avbildningen första gången eftersom den innehåller basavbildningen. Efterföljande uppladdningar går vanligtvis snabbare.

    Medan du väntar kan du slutföra stegen i nästa avsnitt för att konfigurera App Service distribuera från registret.

1. Använd kommandot `az acr repository list` för att kontrollera att push-pushen lyckades:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    Utdata bör visa namnet på bilden.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Konfigurera App Service att distribuera avbildningen från registret

Om du vill distribuera Azure App Service till en App Service skapar du först en webbapp på App Service och ansluter sedan webbappen till containerregistret. När webbappen startar App Service automatiskt avbildningen från registret.

1. Skapa en App Service plan med hjälp av [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) kommandot :

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    En App Service plan motsvarar den virtuella dator som är värd för webbappen. Som standard använder det föregående kommandot en [prisvärd B1-prisnivå](https://azure.microsoft.com/pricing/details/app-service/linux/) som är kostnadsfri den första månaden. Du kan styra nivån med `--sku` parametern .

1. Skapa webbappen med [`az webpp create`](/cli/azure/webapp#az_webapp_create) kommandot :

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Ersätt `<app-name>` med ett namn för webbappen, som måste vara unikt i hela Azure. Ersätt `<registry-name>` också med namnet på ditt register från föregående avsnitt.

1. Använd [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) för att ange `WEBSITES_PORT` miljövariabeln som förväntat av appkoden: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Ersätt `<app-name>` med det namn som du använde i föregående steg.
    
    Mer information om den här miljövariabeln finns [i viktigt i exemplets GitHub-lagringsplats.](https://github.com/Azure-Samples/docker-django-webapp-linux)

1. Aktivera [hanterad](./overview-managed-identity.md) identitet för webbappen med hjälp av [`az webapp identity assign`](/cli/azure/webapp/identity#az_webapp_identity-assign) kommandot :

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Ersätt `<app-name>` med det namn som du använde i föregående steg. Kommandots utdata (filtreras efter argumenten och) är tjänstens huvudnamn för den tilldelade `--query` `--output` identiteten, som du snart använder.

    Med hanterad identitet kan du bevilja behörigheter till webbappen för att få åtkomst till andra Azure-resurser utan att behöva några specifika autentiseringsuppgifter.

1. Hämta ditt prenumerations-ID [`az account show`](/cli/azure/account#az_account_show) med kommandot , som du behöver i nästa steg:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Ge webbappen behörighet att komma åt containerregistret:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Ersätt följande värden:
    - `<principal-id>` med tjänstens huvudnamns-ID från `az webapp identity assign` kommandot
    - `<registry-name>` med namnet på ditt containerregister
    - `<subscription-id>` med prenumerations-ID:t som hämtats från `az account show` kommandot

Mer information om dessa behörigheter finns i [Vad är rollbaserad åtkomstkontroll i Azure](../role-based-access-control/overview.md) och 

## <a name="deploy-the-image-and-test-the-app"></a>Distribuera avbildningen och testa appen

Du kan slutföra de här stegen när avbildningen har pushats till containerregistret och App Service har etablerats fullständigt.

1. Använd kommandot [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) för att ange containerregistret och avbildningen som ska distribueras för webbappen:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Ersätt `<app_name>` med namnet på din webbapp och ersätt på två platser med namnet på ditt `<registry-name>` register. 

    - När du använder ett annat register än Docker Hub (som det här exemplet visar) måste formateras som följt av det fullständigt kvalificerade `--docker-registry-server-url` `https://` domännamnet för registret.
    - Meddelandet "Inga autentiseringsuppgifter har angetts för åtkomst till Azure Container Registry. Försöker leta upp..." anger att Azure använder appens hanterade identitet för att autentisera med containerregistret i stället för att fråga efter ett användarnamn och lösenord.
    - Om du stöter på felet "AttributeError: 'NoneType'-objektet har inget attribut "reserved" (reserverat) kontrollerar du att `<app-name>` ditt är korrekt.

    > [!TIP]
    > Du kan hämta webbappens containerinställningar när som helst med kommandot `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . Avbildningen anges i egenskapen `DOCKER_CUSTOM_IMAGE_NAME` . När webbappen distribueras via Azure DevOps eller Azure Resource Manager-mallar kan avbildningen också visas i en egenskap med namnet `LinuxFxVersion` . Båda egenskaperna har samma syfte. Om båda finns i webbappens konfiguration `LinuxFxVersion` prioriteras.

1. När `az webapp config container set` kommandot har slutförts bör webbappen köras i containern på App Service.

    Om du vill testa appen bläddrar `http://<app-name>.azurewebsites.net` du till och `<app-name>` ersätter med namnet på din webbapp. Vid första åtkomst kan det ta lite tid för appen att svara eftersom App Service måste hämta hela avbildningen från registret. Om webbläsarens tidsinställningar går ut behöver du bara uppdatera sidan. När den första avbildningen har hämtas körs efterföljande tester mycket snabbare.

    ![Lyckat test av webbappen i Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Ändra appkoden och distribuera om

I det här avsnittet gör du en ändring i webbappkoden, återskapar containern och push-erar sedan containern till registret. App Service hämtar sedan automatiskt den uppdaterade avbildningen från registret för att uppdatera webbappen som körs.

1. I den lokala *mappen docker-django-webapp-linux* öppnar du filen *app/templates/app/index.html*.

1. Ändra det första HTML-elementet så att det matchar följande kod.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Spara ändringarna.

1. Ändra till mappen *docker-django-webapp-linux och* återskapa avbildningen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Uppdatera versionsnumret i avbildningens tagg till v1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Byt ut `<registry-name>` mot namnet på ditt register.

1. Push-skicka avbildningen till registret:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Starta om webbappen:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Byt ut `<app_name>` mot namnet på din webbapp. Vid omstart App Service den uppdaterade avbildningen från containerregistret.

1. Kontrollera att uppdateringen har distribuerats genom att gå till `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

1. Aktivera containerloggning:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Aktivera loggströmmen:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

    Du kan även granska loggfilerna från din webbläsare via `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Om du vill stoppa loggströmningen när som helst skriver du **Ctrl** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Anslut till containern med SSH

SSH möjliggör säker kommunikation mellan en container och en klient. Om du vill aktivera SSH-anslutning till containern måste din anpassade avbildning konfigureras för den. När containern körs kan du öppna en SSH-anslutning.

### <a name="configure-the-container-for-ssh"></a>Konfigurera containern för SSH

Exempelappen som används i den här självstudien har redan den nödvändiga konfigurationen i *Dockerfile*, som installerar SSH-servern och även anger inloggningsuppgifterna. Det här avsnittet är endast information. Om du vill ansluta till containern går du vidare till nästa avsnitt

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Den här konfigurationen tillåter inte externa anslutningar till containern. SSH är endast tillgängligt via webbplatsen för Kudu/SCM. Kudu/SCM-webbplatsen autentiseras med ditt Azure-konto.

*Dockerfile kopierar* även *sshd_config* till mappen */etc/ssh/* och exponerar port 2222 på containern:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Port 2222 är en intern port som endast kan nås av containrar i bryggnätverket för ett privat virtuellt nätverk. 

Slutligen startar startskriptet, *init.sh*, SSH-servern.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Öppna SSH-anslutning till container

1. Bläddra till `https://<app-name>.scm.azurewebsites.net/webssh/host` och logga in med ditt Azure-konto. Byt ut `<app-name>` mot namnet på din webbapp.

1. När du har loggat in omdirigeras du till en informationssida för webbappen. Välj **SSH** överst på sidan för att öppna gränssnittet och använda kommandon.

    Du kan till exempel undersöka processerna som körs i den med hjälp av `top` kommandot .
    
## <a name="clean-up-resources"></a>Rensa resurser

De resurser som du skapade i den här artikeln kan medföra löpande kostnader. för att rensa resurserna behöver du bara ta bort resursgruppen som innehåller dem:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Distribuera en anpassad avbildning till ett privat containerregister
> * Distribuera och den anpassade avbildningen i App Service
::: zone pivot="container-linux"
> * Uppdatera och distribuera om avbildningen
::: zone-end
> * Få åtkomst till diagnostikloggar
::: zone pivot="container-linux"
> * Anslut till containern med SSH
::: zone-end

I nästa självstudie får du lära dig hur du mappar ett anpassat DNS-namn till din app.

> [!div class="nextstepaction"]
> [Självstudie: Mappa anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Eller så kan du kolla in andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera en anpassad container](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Självstudie: WordPress-app med flera containrar](tutorial-multi-container-app.md)
::: zone-end
