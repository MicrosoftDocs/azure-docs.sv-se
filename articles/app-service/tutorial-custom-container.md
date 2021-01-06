---
title: 'Självstudie: skapa och köra en anpassad avbildning i Azure App Service'
description: En steg-för-steg-guide om hur du skapar en anpassad Linux-eller Windows-avbildning, push-överför avbildningen till Azure Container Registry och sedan distribuerar avbildningen till Azure App Service. Lär dig hur du migrerar distribuera anpassad program vara till App Service i en anpassad behållare.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: Azure App Service, webbapp, Linux, Windows, Docker, container
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: b3507e22c691f3e3ca9f9e6562a313e95e42f080
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900203"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migrera anpassad program vara till Azure App Service med en anpassad behållare

::: zone pivot="container-windows"  

[Azure App Service](overview.md) har fördefinierade programstackar i Windows som ASP.NET eller Node.js, som körs i IIS. Den förkonfigurerade Windows-miljön låser operativsystemet från administrativ åtkomst, programinstallationer, ändringar av den globala sammansättningscachen och så vidare (se [Operativsystemfunktioner i Azure App Service](operating-system-functionality.md)). Men genom att använda en anpassad Windows-container i App Service kan du göra nödvändiga operativsystemändringar som appen behöver, vilket gör det enkelt att migrera en lokal app som kräver en anpassad operativsystem- och programvarukonfiguration. Den här kursen beskriver hur du migrerar en ASP.NET-app till App Service som använder anpassade teckensnitt som installeras i Windows-teckensnittsbiblioteket. Du distribuerar en Windows-avbildning med en anpassad konfiguration från Visual Studio till [Azure Container Registry](../container-registry/index.yml) och kör den sedan i App Service.

![Visar den webbapp som körs i en Windows-behållare.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- <a href="https://hub.docker.com/" target="_blank">Registrera dig för ett Docker Hub-konto</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installera Docker för Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Växla Docker för att köra Windows-containrar</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installera Visual Studio 2019</a> med arbets belastningarna **ASP.net och webb utveckling** och **Azure Development** . Om du redan har installerat Visual Studio 2019:
    - Installera de senaste uppdateringarna i Visual Studio genom att klicka på **Hjälp**  >  **söka efter uppdateringar**.
    - Lägg till arbets belastningarna i Visual Studio genom att klicka på **verktyg**  >  **Hämta verktyg och funktioner**.

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

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Skärm bild som visar appen som visas i standard webbläsaren.":::

Eftersom den använder ett installerat teckensnitt kan inte appen köras i sandbox-miljön för App Service. Du kan dock distribuera den med hjälp av en Windows-container i stället, eftersom du kan installera teckensnittet i Windows-containern.

### <a name="configure-windows-container"></a>Konfigurera Windows-containern

I Solution Explorer högerklickar du på projektet **CustomFontSample** och väljer **Lägg till** > **Container Orchestration Support** (Stöd för containerorkestrering).

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Skärm bild av fönstret Solution Explorer som visar CustomFontSample-projekt, Lägg till och behållare Orchestrator-support meny alternativ som valts.":::

Välj **Docker Skriv**  >  **OK**.

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
> För att testa Windows-behållaren lokalt, se till att Docker är igång på den lokala datorn.
>

## <a name="publish-to-azure-container-registry"></a>Publicera till Azure Container Registry

[Azure Container Registry](../container-registry/index.yml) kan lagra dina avbildningar för containerdistribution. Du kan konfigurera App Service att använda avbildningar i Azure Container Registry.

### <a name="open-publish-wizard"></a>Öppna publiceringsguiden

I Solution Explorer högerklickar du på projektet **CustomFontSample** och väljer **Publicera**.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Skärm bild av Solution Explorer som visar CustomFontSample-projektet och publicera valt.":::

### <a name="create-registry-and-publish"></a>Skapa register och publicera

I guiden publicera väljer du **container Registry**  >  **Skapa ny Azure Container Registry**  >  **publicera**.

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Skärm bild av publicerings guiden som visar Container Registry, skapar nya Azure Container Registry och knappen publicera har valts.":::

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

På den vänstra menyn väljer du **skapa en resurs**  >  **webb**  >  **Web App for containers**.

### <a name="configure-app-basics"></a>Konfigurera grundläggande program

På fliken **grundläggande** inställningar konfigurerar du inställningarna enligt följande tabell och klickar sedan på **Nästa: Docker**.

| Inställning  | Föreslaget värde | Mer information |
| ----------------- | ------------ | ----|
|**Prenumeration**| Kontrol lera att rätt prenumeration har valts. |  |
|**Resursgrupp**| Välj **Skapa ny**, Skriv **myResourceGroup** och klicka på **OK**. |  |
|**Namn**| Skriv ett unikt namn. | Webbadressen till webbappen är `http://<app-name>.azurewebsites.net`, där `<app-name>` är appens namn. |
|**Publicera**| Docker-behållare | |
|**Operativsystem**| Windows | |
|**Region**| Europa, västra | |
|**Windows-plan**| Välj **Skapa ny**, Skriv **myAppServicePlan** och klicka på **OK**. | |

Fliken **grundläggande** bör se ut så här:

![Visar fliken grundläggande som används för att konfigurera webbappen.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Konfigurera Windows-containern

På fliken **Docker** konfigurerar du din anpassade Windows-behållare så som visas i följande tabell och väljer **Granska + skapa**.

| Inställning  | Föreslaget värde |
| ----------------- | ------------ |
|**Bildkälla**| Azure Container register |
|**Register**| Välj [registret som du skapade tidigare](#publish-to-azure-container-registry). |
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

![Visar den nya webb sidan för webbappen.](media/tutorial-custom-container/app-starting.png)

Vänta några minuter och försök igen tills startsidan visas med det snygga teckensnitt du förväntar dig:

![Visar start sidan med det teckensnitt som du har konfigurerat.](media/tutorial-custom-container/app-running.png)

**Grattis!** Du har migrerat ett ASP.NET-program till Azure App Service i en Windows-container.

## <a name="see-container-start-up-logs"></a>Se containerns startloggar

Det kan ta lite tid för Windows-containern att läsas in. Om du vill se förloppet navigerar du till följande URL genom att ersätta *\<app-name>* med namnet på din app.
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

Azure App Service använder Docker-behållar tekniken för att vara värd för både inbyggda avbildningar och anpassade avbildningar. Om du vill se en lista över inbyggda avbildningar kör du Azure CLI-kommandot, ["AZ webapp List-Runtimes--Linux"](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-list-runtimes). Om dessa bilder inte uppfyller dina behov kan du bygga och distribuera en anpassad avbildning.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa en anpassad avbildning om ingen inbyggd avbildning uppfyller dina behov
> * Skicka den anpassade avbildningen till ett privat behållar register på Azure
> * Kör den anpassade avbildningen i App Service
> * Konfigurera miljövariabler
> * Uppdatera och distribuera om avbildningen
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

Genom att slutföra den här självstudien får du en liten avgift på ditt Azure-konto för behållar registret och kan innebära ytterligare kostnader för att vara värd för behållaren i mer än en månad.

## <a name="set-up-your-initial-environment"></a>Konfigurera din inledande miljö

- Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Installera [Docker](https://docs.docker.com/get-started/#setup), som du använder för att bygga Docker-avbildningar. Om du installerar Docker kan det krävas en omstart av datorn.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- I den här självstudien krävs version 2.0.80 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

När du har installerat Docker eller kört Azure Cloud Shell öppnar du ett terminalfönster och kontrollerar att Docker är installerat:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Klona eller hämta exempel appen

Du kan hämta exemplet för den här självstudien via git-kloning eller hämta.

### <a name="clone-with-git"></a>Klona med git

Klona exempel lagrings platsen:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Se till att inkludera `--config core.autocrlf=input` argumentet för att garantera korrekt rad brytningar i filer som används i Linux-behållaren:

Gå sedan till mappen:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Ladda ned från GitHub

I stället för att använda Git-kloning kan du besöka [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , välja **klona** och sedan välja **Hämta zip**. 

Packa upp ZIP-filen i en mapp med namnet *Docker-django-webapp-Linux*. 

Öppna sedan ett terminalfönster i den *Docker-django-webapp-Linux-* mappen.

## <a name="optional-examine-the-docker-file"></a>Valfritt Granska Docker-filen

Filen i exemplet med namnet _Dockerfile_ som beskriver Docker-avbildningen och innehåller konfigurations anvisningar:

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
* Den andra gruppen med kommandon skapar en [SSH](https://www.ssh.com/ssh/protocol/) -Server för säker kommunikation mellan behållaren och värden.
* Den sista raden, `ENTRYPOINT ["init.sh"]` , startar `init.sh` för att starta SSH-tjänsten och python-servern.

## <a name="build-and-test-the-image-locally"></a>Bygg och testa avbildningen lokalt

> [!NOTE]
> Docker Hub har [kvoter för antalet anonyma hämtningar per IP och antalet autentiserade hämtningar per kostnads fri användare (se **data överföring**)](https://www.docker.com/pricing). Om du märker att dina hämtningar från Docker Hub begränsas, kan `docker login` du prova om du inte redan är inloggad.
> 

1. Kör följande kommando för att skapa avbildningen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Testa att bygget fungerar genom att köra Docker-behållaren lokalt:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Det här [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) kommandot anger porten med `-p` argumentet följt av namnet på avbildningen. 
    
    > [!TIP]
    > Om du kör på Windows och ser felet *standard_init_linux. go: 211: exec-användar processen orsakade "ingen sådan fil eller katalog"*, *init.sh* -filen innehåller CR-LF-raden i stället för förväntad LF-ändelse. Det här felet uppstår om du använde git för att klona exempel lagrings platsen men utelämnade `--config core.autocrlf=input` parametern. I det här fallet klonar du lagrings platsen igen med argumentet--config. Du kan också se felet om du har redigerat *init.sh* och sparat det med CRLF-slut. I det här fallet sparar du filen igen med endast LF-ändelsen.

1. Bläddra till `http://localhost:8000` för att kontrol lera att webbappen och behållaren fungerar som de ska.

    ![Testa webbappen lokalt](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I det här avsnittet och de som följer kan du etablera resurser i Azure som du push-överför avbildningen till och sedan distribuera en behållare till Azure App Service. Du börjar med att skapa en resurs grupp där du kan samla in alla dessa resurser.

Kör kommandot [AZ Group Create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create) för att skapa en resurs grupp:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Du kan ändra `--location` värdet för att ange en region nära dig.

## <a name="push-the-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

I det här avsnittet ska du push-överföra avbildningen till Azure Container Registry från vilken App Service kan distribuera den.

1. Kör [`az acr create`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-create) kommandot för att skapa en Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Ersätt `<registry-name>` med ett lämpligt namn för registret. Namnet får bara innehålla bokstäver och siffror och måste vara unikt i hela Azure.

1. Kör [`az acr show`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-show) kommandot för att hämta autentiseringsuppgifter för registret:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    JSON-utdata för det här kommandot ger två lösen ord tillsammans med registrets användar namn.
    
1. Använd `docker login` kommandot för att logga in på behållar registret:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Ersätt `<registry-name>` och `<registry-username>` med värden från föregående steg. När du uppmanas till det skriver du ett av lösen orden från föregående steg.

    Du använder samma register namn i alla återstående steg i det här avsnittet.

1. När inloggningen lyckas, tagga din lokala Docker-avbildning för registret:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Använd `docker push` kommandot för att skicka avbildningen till registret:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Det kan ta några minuter att ladda upp avbildningen första gången eftersom den innehåller bas avbildningen. Efterföljande uppladdningar är vanligt vis snabbare.

    Medan du väntar kan du slutföra stegen i nästa avsnitt för att konfigurera App Service att distribuera från registret.

1. Använd `az acr repository list` kommandot för att kontrol lera att push lyckades:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    Utdata ska visa namnet på din bild.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Konfigurera App Service för att distribuera avbildningen från registret

Om du vill distribuera en behållare till Azure App Service skapar du först en webbapp på App Service och ansluter sedan webb programmet till behållar registret. När webbappen startar hämtar App Service automatiskt avbildningen från registret.

1. Skapa en App Service plan med [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest&preserve-view=true#az-appservice-plan-create) kommandot:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    En App Service plan motsvarar den virtuella datorn som är värd för webbappen. Som standard använder föregående kommando en [pris nivå](https://azure.microsoft.com/pricing/details/app-service/linux/) som är kostnads fri för den första månaden. Du kan styra nivån med `--sku` parametern.

1. Skapa webbappen med [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-create) kommandot:

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Ersätt `<app-name>` med ett namn för webbappen, som måste vara unikt i hela Azure. Ersätt även `<registry-name>` med namnet på ditt register från föregående avsnitt.

1. Använd [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) för att ställa in `WEBSITES_PORT` miljövariabeln som förväntat av appens kod: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Ersätt `<app-name>` med det namn som du använde i föregående steg.
    
    Mer information om den här miljövariabeln finns i [readme i exemplets GitHub-lagringsplats](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Aktivera [hanterad identitet](./overview-managed-identity.md) för webbappen med hjälp av- [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest&preserve-view=true#az-webapp-identity-assign) kommandot:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Ersätt `<app-name>` med det namn som du använde i föregående steg. Utdata från kommandot (filtreras efter `--query` `--output` argumenten) är tjänstens huvud namn för den tilldelade identiteten, som du använder strax.

    Med hanterad identitet kan du ge webbappen behörighet att få åtkomst till andra Azure-resurser utan att behöva några speciella autentiseringsuppgifter.

1. Hämta ditt prenumerations-ID med [`az account show`](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az-account-show) kommandot, som du behöver i nästa steg:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Ge webb programmet behörighet att få åtkomst till behållar registret:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Ersätt följande värden:
    - `<principal-id>` med tjänstens huvud namn-ID från `az webapp identity assign` kommandot
    - `<registry-name>` med namnet på ditt behållar register
    - `<subscription-id>` med det prenumerations-ID som hämtats från `az account show` kommandot

Mer information om dessa behörigheter finns i [Vad är rollbaserad åtkomst kontroll i Azure](../role-based-access-control/overview.md) och 

## <a name="deploy-the-image-and-test-the-app"></a>Distribuera avbildningen och testa appen

Du kan utföra de här stegen när avbildningen har överförts till behållar registret och App Service är helt etablerad.

1. Använd [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest&preserve-view=true#az-webapp-config-container-set) kommandot för att ange behållar registret och avbildningen som ska distribueras för webb programmet:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Ersätt `<app_name>` med namnet på din webbapp och Ersätt `<registry-name>` på två platser med namnet på ditt register. 

    - När du använder ett annat register än Docker-hubben (som det här exemplet visar) `--docker-registry-server-url` måste formateras som `https://` följt av registrets fullständigt kvalificerade domän namn.
    - Meddelandet "inga autentiseringsuppgifter angavs för att få åtkomst till Azure Container Registry. Försöker leta upp... " visar att Azure använder appens hanterade identitet för att autentisera med behållar registret i stället för att begära ett användar namn och lösen ord.
    - Om du får felet "AttributeError: NoneType"-objektet har inte attributet reserverat, kontrollerar du att det `<app-name>` är korrekt.

    > [!TIP]
    > Du kan hämta webbappens behållar inställningar när som helst med kommandot `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . Avbildningen anges i egenskapen `DOCKER_CUSTOM_IMAGE_NAME` . När webbappen distribueras via Azure DevOps eller Azure Resource Manager mallar, kan avbildningen också visas i en egenskap med namnet `LinuxFxVersion` . Båda egenskaperna har samma syfte. Om båda finns i webbappens konfiguration, `LinuxFxVersion` prioriteras.

1. När `az webapp config container set` kommandot har slutförts ska webb programmet köras i behållaren på App Service.

    Testa appen genom att bläddra till `http://<app-name>.azurewebsites.net` och ersätta `<app-name>` med namnet på din webbapp. Vid första åtkomsten kan det ta lite tid innan appen svarar eftersom App Service måste hämta hela avbildningen från registret. Om tids gränsen för webbläsaren uppdateras, behöver du bara uppdatera sidan. När den första avbildningen hämtas körs efterföljande tester mycket snabbare.

    ![Lyckad test av webbappen på Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Ändra app-koden och distribuera om

I det här avsnittet ska du göra en ändring i webb program koden, återskapa behållaren och sedan skicka behållaren till registret. App Service hämtar sedan den uppdaterade avbildningen automatiskt från registret för att uppdatera den webbapp som körs.

1. I din lokala *Docker-django-webapp-Linux-* mapp öppnar du filen *app/templates/app/index.html*.

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

1. Ändra till mappen *Docker-django-webapp-Linux* och återskapa avbildningen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Uppdatera versions numret i avbildningens tagg till v-1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Byt ut `<registry-name>` mot namnet på ditt register.

1. Push-överför avbildningen till registret:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Starta om webb programmet:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Byt ut `<app_name>` mot namnet på din webbapp. Vid omstart hämtar App Service den uppdaterade avbildningen från behållar registret.

1. Kontrol lera att uppdateringen har distribuerats genom att bläddra till `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

1. Aktivera behållar loggning:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Aktivera logg strömmen:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

    Du kan även granska loggfilerna från din webbläsare via `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Om du vill stoppa logg strömningen när som helst, skriver du **CTRL** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Anslut till containern med SSH

SSH möjliggör säker kommunikation mellan en container och en klient. Din anpassade avbildning måste vara konfigurerad för att du ska kunna aktivera SSH-anslutningen till din behållare. När behållaren har körts kan du öppna en SSH-anslutning.

### <a name="configure-the-container-for-ssh"></a>Konfigurera behållaren för SSH

Exempel programmet som används i den här självstudien har redan den konfiguration som krävs i *Dockerfile*, som installerar SSH-servern och anger även inloggnings uppgifterna. Det här avsnittet är endast information. Om du vill ansluta till behållaren går du vidare till nästa avsnitt

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Den här konfigurationen tillåter inte externa anslutningar till behållaren. SSH är endast tillgängligt via webbplatsen för Kudu/SCM. Kudu/SCM-webbplatsen autentiseras med ditt Azure-konto.

*Dockerfile* kopierar också *sshd_config* -filen till mappen */etc/ssh/* och exponerar port 2222 på behållaren:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Port 2222 är en intern port som endast är tillgänglig för behållare i ett privat virtuellt nätverks bro nätverk. 

Slutligen börjar Entry-skriptet, *init.sh*, SSH-servern.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Öppna SSH-anslutning till container

1. Bläddra till `https://<app-name>.scm.azurewebsites.net/webssh/host` och logga in med ditt Azure-konto. Byt ut `<app-name>` mot namnet på din webbapp.

1. När du har loggat in omdirigeras du till en informations sida för webb programmet. Välj **SSH** överst på sidan för att öppna gränssnittet och använda kommandon.

    Du kan till exempel undersöka de processer som körs i den med hjälp av `top` kommandot.
    
## <a name="clean-up-resources"></a>Rensa resurser

De resurser som du skapade i den här artikeln kan medföra kontinuerliga kostnader. Om du vill rensa resurserna behöver du bara ta bort resurs gruppen som innehåller dem:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Distribuera en anpassad avbildning till ett privat behållar register
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
> [Självstudie: mappa ett anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera en anpassad container](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Självstudie: WordPress-app med flera behållare](tutorial-multi-container-app.md)
::: zone-end
