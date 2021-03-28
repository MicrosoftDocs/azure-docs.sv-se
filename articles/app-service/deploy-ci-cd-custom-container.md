---
title: CI/CD till anpassade behållare
description: Konfigurera kontinuerlig distribution till en anpassad Windows-eller Linux-behållare i Azure App Service.
keywords: Azure App Service, Linux, Docker, ACR, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 654b0f842a3165926242d1ef03f2dfe4e5bacfdc
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643345"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Kontinuerlig distribution med anpassade behållare i Azure App Service

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad behållar avbildning från hanterade [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -databaser eller [Docker-hubb](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. gå till distributions Center

Gå till hanterings sidan för din App Service-app i [Azure Portal](https://portal.azure.com).

I den vänstra menyn klickar du på Inställningar för **distributions Center**  >  . 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Välj distributions källa

**Välj** distributions källa beroende på ditt scenario:
- **Container Registry** ställer in CI/CD mellan ditt behållar register och App Service.
- Alternativet **GitHub-åtgärder** är till för dig om du behåller käll koden för behållar avbildningen i GitHub. Utlöses av nya incheckningar till din GitHub-lagringsplats, distributions åtgärden kan köras `docker build` och `docker push` direkt till behållar registret, och sedan uppdatera App Service-appen för att köra den nya avbildningen. Mer information finns i [så här fungerar CI/CD med GitHub-åtgärder](#how-cicd-works-with-github-actions).
- Om du vill konfigurera CI/CD med **Azure-pipelines**, se [distribuera en Azure Web App-behållare från Azure-pipeliner](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> För en Docker-skapa app väljer du **container Registry**.

Om du väljer GitHub åtgärder **klickar du på** **auktorisera** och följer behörighets frågorna. Om du redan har behörighet för GitHub tidigare kan du distribuera från en annan användares lagrings plats genom att klicka på **Ändra konto**.

När du har auktoriserat ditt Azure-konto med GitHub **väljer** du den **organisation**, den **lagrings plats** och **gren** som du vill distribuera från.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Konfigurera register inställningar
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Konfigurera register inställningar

Om du vill distribuera en behållare för flera behållare (Docker komponera) **väljer** du **Docker** **text i behållar typ**.

Om du inte ser List rutan **container Type** rullar du tillbaka till **källa** och **väljer** **container Registry**.
::: zone-end

I **register källa** **väljer** du var behållar registret är. Om det varken är Azure Container Registry eller Docker Hub **väljer** du **privat register**.

::: zone pivot="container-linux"
> [!NOTE]
> Om din app med flera behållare (Docker Compose) använder mer än en privat avbildning, se till att de privata avbildningarna finns i samma privata register och att de är tillgängliga med samma användarautentiseringsuppgifter. Om appen för flera behållare endast använder offentliga avbildningar **väljer** du **Docker Hub**, även om vissa avbildningar inte finns i Docker Hub.
::: zone-end  

Följ nästa steg genom att välja fliken som matchar ditt val.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

I list rutan **register** visas registren i samma prenumeration som din app. **Välj** det register som du vill använda.

> [!NOTE]
> Om du vill distribuera från ett register i en annan prenumeration **väljer** du **privat register** i **register källa** i stället.

::: zone pivot="container-windows"
**Välj** **avbildningen** och **taggen** som ska distribueras. **Ange** Start kommandot i **Start filen** om du vill. 
::: zone-end
::: zone pivot="container-linux"
Följ nästa steg beroende på **typ av behållare**:
- För **Docker Compose** **väljer** du registret för dina privata avbildningar. **Klicka på** **Välj fil** för att ladda upp din [Docker-filfil](https://docs.docker.com/compose/compose-file/)eller **Klistra in** innehållet i Docker-filen i **config**.
- För **en behållare** **väljer** du **avbildningen** och **taggen** som ska distribueras. **Ange** Start kommandot i **Start filen** om du vill. 
::: zone-end

App Service lägger till strängen i **Start filen** i [slutet av `docker run` kommandot (som `[COMMAND] [ARG...]` segmentet)](https://docs.docker.com/engine/reference/run/) när du startar din behållare.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
I **databas åtkomst** **väljer** du om avbildningen som ska distribueras är offentlig eller privat.
::: zone-end
::: zone pivot="container-linux"
I **databas åtkomst** **väljer** du om avbildningen som ska distribueras är offentlig eller privat. För en Docker-skapa app med en eller flera privata avbildningar **väljer** du **privat**.
::: zone-end

Om du väljer en privat avbildning **anger** du **inloggnings** namnet och **lösen ordet** för Docker-kontot.

::: zone pivot="container-windows"
**Ange** bild-och taggnamn i **fullständigt avbildnings namn och tagg**, avgränsade med en `:` (till exempel `nginx:latest` ). **Ange** Start kommandot i **Start filen** om du vill. 
::: zone-end
::: zone pivot="container-linux"
Följ nästa steg beroende på **typ av behållare**:
- För **Docker Compose** **väljer** du registret för dina privata avbildningar. **Klicka på** **Välj fil** för att ladda upp din [Docker-filfil](https://docs.docker.com/compose/compose-file/)eller **Klistra in** innehållet i Docker-filen i **config**.
- För **en behållare** **anger** du avbildningen och taggnamnet i **fullständigt avbildnings namn och tagg**, avgränsade med en `:` (till exempel `nginx:latest` ). **Ange** Start kommandot i **Start filen** om du vill. 
::: zone-end

App Service lägger till strängen i **Start filen** i [slutet av `docker run` kommandot (som `[COMMAND] [ARG...]` segmentet)](https://docs.docker.com/engine/reference/run/) när du startar din behållare.

# <a name="private-registry"></a>[Privat register](#tab/private)

I **Server**-URL **anger** du URL: en för servern, från och med **https://**.

I **inloggning** och **lösen ord** **anger** du inloggnings uppgifter för ditt privata register.

::: zone pivot="container-windows"
**Ange** bild-och taggnamn i **fullständigt avbildnings namn och tagg**, avgränsade med en `:` (till exempel `nginx:latest` ). **Ange** Start kommandot i **Start filen** om du vill. 
::: zone-end
::: zone pivot="container-linux"
Följ nästa steg beroende på **typ av behållare**:
- För **Docker Compose** **väljer** du registret för dina privata avbildningar. **Klicka på** **Välj fil** för att ladda upp din [Docker-filfil](https://docs.docker.com/compose/compose-file/)eller **Klistra in** innehållet i Docker-filen i **config**.
- För **en behållare** **anger** du avbildningen och taggnamnet i **fullständigt avbildnings namn och tagg**, avgränsade med en `:` (till exempel `nginx:latest` ). **Ange** Start kommandot i **Start filen** om du vill. 
::: zone-end

App Service lägger till strängen i **Start filen** i [slutet av `docker run` kommandot (som `[COMMAND] [ARG...]` segmentet)](https://docs.docker.com/engine/reference/run/) när du startar din behållare.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Aktivera CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Aktivera CI/CD
::: zone-end

App Service stöder CI/CD-integrering med Azure Container Registry och Docker Hub. Aktivera det genom att **välja** **on** i **kontinuerlig distribution**.

::: zone pivot="container-linux"
> [!NOTE]
> Om du väljer **GitHub åtgärder** i **källa** får du inte det här alternativet eftersom CI/CD hanteras av GitHub åtgärder direkt. I stället visas ett avsnitt för **arbets flödes konfiguration** där du kan **Klicka på** **Förhandsgranska fil** för att kontrol lera arbets flödes filen. Azure sparar filen i din valda GitHub-datakälla för att hantera skapande och distribution av uppgifter. Mer information finns i [så här fungerar CI/CD med GitHub-åtgärder](#how-cicd-works-with-github-actions).
::: zone-end

När du aktiverar det här alternativet lägger App Service till en webhook till din lagrings plats i Azure Container Registry eller Docker-hubb. Lagrings platsen skickas till denna webhook när den valda avbildningen uppdateras med `docker push` . Webhooken gör att din App Service-app startas om och körs `docker pull` för att hämta den uppdaterade avbildningen.

**För andra privata register** kan du publicera till webhooken manuellt eller som ett steg i en CI/CD-pipeline. I **webhook**-URL **klickar du på** **kopierings** knappen för att hämta webhook-URL: en.

::: zone pivot="container-linux"
> [!NOTE]
> Stöd för appar med flera behållare (Docker komponera) är begränsat: 
> - För Azure Container Registry skapar App Service en webhook i det valda registret med registret som definitions område. En `docker push` till en lagrings plats i registret (inklusive de som inte refereras till av Docker-filfilen) utlöser en omstart av appen. Du kanske vill [ändra webhooken](../container-registry/container-registry-webhook.md) till ett smalare omfång.
> - Docker-hubben har inte stöd för Webhooks på register nivån. Du måste **lägga till** webhookarna manuellt i de avbildningar som anges i din Docker-filfil.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Spara inställningarna
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Spara inställningarna
::: zone-end

**Klicka på** **Spara**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Så här fungerar CI/CD med GitHub-åtgärder

Om du väljer **GitHub-åtgärder** i **källa** (se [Välj distributions källa](#2-choose-deployment-source)), App Service konfigurerar CI/CD på följande sätt:

- Dearbeter en arbets flödes fil för GitHub-åtgärder i din GitHub-lagringsplats för att hantera skapande och distribution av aktiviteter till App Service.
- Lägger till autentiseringsuppgifterna för ditt privata register som GitHub hemligheter. Den genererade arbets flödes filen kör [Azure/Docker-login-](https://github.com/Azure/docker-login) åtgärden för att logga in med ditt privata register och kör sedan `docker push` för att distribuera till den.
- Lägger till publicerings profilen för appen som en GitHub-hemlighet. Den genererade arbets flödes filen använder den här hemligheten för att autentisera med App Service och kör sedan åtgärden [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) för att konfigurera den uppdaterade avbildningen, som startar en omstart av appen för att hämta den uppdaterade avbildningen.
- Fångar information från [arbets flödets körnings loggar](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) och visar den på fliken **loggar** i appens **distributions Center**.

Du kan anpassa GitHub-åtgärdernas build-Provider på följande sätt:

- Anpassa arbets flödes filen när den har genererats i din GitHub-lagringsplats. Mer information finns i [syntax för arbets flöden för GitHub-åtgärder](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Se bara till att arbets flödet slutar med [Azure/webapps-Deploy-](https://github.com/Azure/webapps-deploy) åtgärden för att utlösa en omstart av appen.
- Om den valda grenen är skyddad kan du fortfarande förhandsgranska arbets flödes filen utan att spara konfigurationen och sedan lägga till den och nödvändiga GitHub-hemligheter i databasen manuellt. Den här metoden ger dig inte logg integrering med Azure Portal.
- Distribuera med ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) i Azure Active Directory i stället för en publicerings profil.

#### <a name="authenticate-with-a-service-principal"></a>Autentisera med ett huvud namn för tjänsten

Den här valfria konfigurationen ersätter standardautentiseringen med publicerings profiler i den genererade arbets flödes filen.

**Generera** ett huvud namn för tjänsten med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). I följande exempel ersätter *\<subscription-id>* , *\<group-name>* och *\<app-name>* med dina egna värden. **Spara** hela JSON-utdata för nästa steg, inklusive den översta nivån `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> För säkerhet ger du den lägsta åtkomst som krävs för tjänstens huvud namn. Omfånget i föregående exempel är begränsat till den särskilda App Service-appen och inte hela resurs gruppen.

I [GitHub](https://github.com/) **bläddrar** du till din lagrings plats och **väljer** sedan **Inställningar > hemligheter > Lägg till en ny hemlighet**. **Klistra in** hela JSON-utdata från Azure CLI-kommandot i fältet hemligt värde. **Ge** hemligheten ett namn som `AZURE_CREDENTIALS` .

I arbets flödes filen som genereras av **distributions Center** **ändrar** du `azure/webapps-deploy` steget med kod som i följande exempel:

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatisera med CLI

Om du vill konfigurera behållar registret och Docker-avbildningen **Kör** du [AZ webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set).

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Privat register](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Om du vill konfigurera en app för flera behållare (Docker komponera) **förbereder** du en Docker-filfil lokalt och **Kör** sedan [AZ webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) med `--multicontainer-config-file` parametern. Om Docker-filen innehåller privata avbildningar **lägger du till** `--docker-registry-server-*` parametrar som visas i föregående exempel.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Om du vill konfigurera CI/CD från behållar registret till din app **Kör** du [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) med `--enable-cd` parametern. Kommandot matar ut webhook-URL: en, men du måste skapa webhooken i registret manuellt i ett separat steg. I följande exempel kan du använda CI/CD i din app och sedan använda webhook-URL: en i utdata för att skapa webhooken i Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Fler resurser

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Snabb start: kör en anpassad behållare på App Service](quickstart-custom-container.md)
* [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)
* [Konfigurera anpassade behållare](configure-custom-container.md)
* [Åtgärder arbets flöden att distribuera till Azure](https://github.com/Azure/actions-workflow-samples)
