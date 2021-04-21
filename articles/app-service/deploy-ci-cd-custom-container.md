---
title: CI/CD till anpassade containrar
description: Konfigurera kontinuerlig distribution till en anpassad Windows- eller Linux-container i Azure App Service.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 6519f3fe7335ed41f4d5ef67771aaa738a33e4a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782611"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Kontinuerlig distribution med anpassade containrar i Azure App Service

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad containeravbildning [från hanterade](https://azure.microsoft.com/services/container-registry/) Azure Container Registry databaser [eller Docker Hub](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Gå till Distributionscenter

I [Azure Portal](https://portal.azure.com)du till hanteringssidan för din App Service appen.

Klicka på Inställningar för **Distributionscenter på den vänstra**  >  **menyn.** 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Välj distributionskälla

**Välj** distributionskälla beroende på ditt scenario:
- **Containerregistret** uppsättningar CI/CD mellan ditt containerregister och App Service.
- Alternativet **GitHub Actions** är till för dig om du underhåller källkoden för containeravbildningen i GitHub. Utlöst av nya genomföranden till GitHub-lagringsplatsen kan åtgärden distribuera köras och direkt till ditt containerregister och sedan uppdatera din App Service för att `docker build` `docker push` köra den nya avbildningen. Mer information finns i [Så här fungerar CI/CD med GitHub Actions](#how-cicd-works-with-github-actions).
- Om du vill konfigurera CI/CD med **Azure Pipelines kan** du gå [till Distribuera en Azure Web App-container från Azure Pipelines.](/azure/devops/pipelines/targets/webapp-on-container-linux)

> [!NOTE]
> För en Docker Compose-app väljer **du Container Registry**.

Om du väljer GitHub Actions klickar **du på** **Auktorisera** och följer auktoriseringsanvisningarna. Om du redan har auktoriserat med GitHub tidigare kan du distribuera från en annan användares lagringsplats genom att klicka på **Ändra konto.**

När du har auktoriserat ditt Azure-konto med GitHub **väljer** du **den organisation,** **lagringsplats** och **gren** som du vill distribuera från.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Konfigurera registerinställningar
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Konfigurera registerinställningar

Om du vill distribuera en app med flera containrar (Docker Compose) **väljer du** **Docker Compose** i **Containertyp.**

Om du inte ser listrutan **Containertyp bläddrar** du tillbaka till **Källa och** **väljer** **Container Registry**.
::: zone-end

I **Registerkälla** väljer **du** var containerregistret finns. Om det varken är Azure Container Registry eller Docker Hub väljer **du** **Privat register.**

::: zone pivot="container-linux"
> [!NOTE]
> Om appen för flera containrar (Docker Compose) använder fler än en privat avbildning kontrollerar du att de privata avbildningarna finns i samma privata register och är tillgängliga med samma autentiseringsuppgifter. Om din app med flera containrar endast använder offentliga  **avbildningar väljer du Docker Hub**, även om vissa avbildningar inte finns Docker Hub.
::: zone-end  

Följ nästa steg genom att välja den flik som matchar ditt val.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

**Listrutan** Register visar register i samma prenumeration som din app. **Välj** det register som du vill använda.

> [!NOTE]
> Om du vill distribuera från ett register i en annan prenumeration väljer **du** **Privat register** i **Registerkälla i** stället.

::: zone pivot="container-windows"
**Välj** den **avbildning** och **tagg som** ska distribueras. Om du vill kan **du skriva** startkommandot i **startfilen**. 
::: zone-end
::: zone pivot="container-linux"
Följ nästa steg beroende på **containertypen**:
- För **Docker Compose väljer** **du registret** för dina privata avbildningar. **Klicka** **på Välj** fil för att ladda  [upp Docker Compose-filen,](https://docs.docker.com/compose/compose-file/)eller klistra bara in innehållet i Docker Compose-filen i **Config**.
- För **Enskild container** väljer du **den** **avbildning och** tagg **som** ska distribueras. Om du vill kan **du skriva** startkommandot i **startfilen**. 
::: zone-end

App Service lägger till strängen i **startfilen** i slutet av [kommandot `docker run` (som `[COMMAND] [ARG...]` segment) när](https://docs.docker.com/engine/reference/run/) du startar containern.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
I **Lagringsplatsåtkomst** väljer **du** om avbildningen som ska distribueras är offentlig eller privat.
::: zone-end
::: zone pivot="container-linux"
I **Åtkomst till lagringsplats** **väljer du** om avbildningen som ska distribueras är offentlig eller privat. För en Docker Compose-app med en eller flera privata avbildningar **väljer du** **Privat**.
::: zone-end

Om du väljer en privat avbildning **anger du** **Inloggning** (användarnamn) **och Lösenord** för Docker-kontot.

::: zone pivot="container-windows"
**Ange** avbildnings- och taggnamnet **i Fullständigt avbildningsnamn och Tagg**, avgränsade med en `:` (till exempel `nginx:latest` ). Om du vill skriver **du** startkommandot i **Startfil**. 
::: zone-end
::: zone pivot="container-linux"
Följ nästa steg beroende på **containertypen**:
- För **Docker Compose väljer** **du registret** för dina privata avbildningar. **Klicka** **på Välj fil** för att ladda  upp [din Docker Compose-fil](https://docs.docker.com/compose/compose-file/)eller klistra bara in innehållet i din Docker Compose-fil i **Config**.
- För **Enskild container,** **ange avbildnings-** och taggnamnet i Fullständigt **avbildningsnamn** och Tagg , avgränsade med `:` en (till exempel `nginx:latest` ). Om du vill skriver **du** startkommandot i **Startfil**. 
::: zone-end

App Service lägger till strängen i **startfilen** i slutet av kommandot [ `docker run` (som `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) när du startar containern.

# <a name="private-registry"></a>[Privat register](#tab/private)

I **Server-URL** **anger** du url:en för servern, som börjar **med https://**.

I **Logga in** och Lösenord **anger** **du** dina inloggningsuppgifter för ditt privata register.

::: zone pivot="container-windows"
**Ange** avbildnings- och taggnamnet **i Fullständigt avbildningsnamn och Tagg**, avgränsade med en `:` (till exempel `nginx:latest` ). Om du vill skriver **du** startkommandot i **Startfil**. 
::: zone-end
::: zone pivot="container-linux"
Följ nästa steg beroende på **containertypen**:
- För **Docker Compose väljer** **du registret** för dina privata avbildningar. **Klicka** **på Välj fil** för att ladda  upp [din Docker Compose-fil](https://docs.docker.com/compose/compose-file/)eller klistra bara in innehållet i din Docker Compose-fil i **Config**.
- För **Enskild container,** **ange avbildnings-** och taggnamnet i Fullständigt **avbildningsnamn och Tagg**, avgränsade med en `:` (till exempel `nginx:latest` ). Om du vill kan **du skriva** startkommandot i **startfilen**. 
::: zone-end

App Service lägger till strängen i **startfilen** i slutet av [kommandot `docker run` (som `[COMMAND] [ARG...]` segment) när](https://docs.docker.com/engine/reference/run/) du startar containern.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Aktivera CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Aktivera CI/CD
::: zone-end

App Service stöder CI/CD-integrering med Azure Container Registry och Docker Hub. Om du vill aktivera det **väljer** **du På** i Kontinuerlig **distribution.**

::: zone pivot="container-linux"
> [!NOTE]
> Om du **GitHub Actions** i **Källa** får du inte det här alternativet eftersom CI/CD hanteras av GitHub Actions direkt. I stället visas avsnittet **Arbetsflödeskonfiguration** där du kan klicka på **Förhandsgranska fil** **för** att granska arbetsflödesfilen. Azure sparar den här filen i den valda GitHub-källlagringsplatsen för att hantera bygg- och distributionsuppgifter. Mer information finns i [Så här fungerar CI/CD med GitHub Actions](#how-cicd-works-with-github-actions).
::: zone-end

När du aktiverar det här alternativet App Service en webhook till din lagringsplats i Azure Container Registry eller Docker Hub. Din lagringsplats publicerar till den här webhooken när den valda avbildningen uppdateras med `docker push` . Webhooken gör att din App Service startas om och körs för `docker pull` att få den uppdaterade avbildningen.

**För andra privata register kan du** publicera till webhooken manuellt eller som ett steg i en CI/CD-pipeline. I **Webhook-URL klickar** **du på** **knappen Kopiera** för att hämta webhook-URL:en.

::: zone pivot="container-linux"
> [!NOTE]
> Stödet för appar med flera containrar (Docker Compose) är begränsat: 
> - För Azure Container Registry skapar App Service en webhook i det valda registret med registret som omfång. En till en lagringsplats i registret (inklusive de som inte refereras till av `docker push` docker Compose-filen) utlöser en omstart av appen. Du kanske vill ändra [webhooken till](../container-registry/container-registry-webhook.md) ett smalare omfång.
> - Docker Hub stöder inte webhooks på registernivå. Du måste **lägga till** webhooks manuellt i avbildningarna som anges i Docker Compose-filen.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Spara dina inställningar
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Spara inställningarna
::: zone-end

**Klicka på** **Spara.**

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Så här fungerar CI/CD med GitHub Actions

Om du **GitHub Actions** i **Källa** [(se](#2-choose-deployment-source)Välj distributionskälla) App Service du CI/CD på följande sätt:

- Sätter in en GitHub Actions arbetsflödesfil på din GitHub-lagringsplats för att hantera bygg- och distributionsuppgifter till App Service.
- Lägger till autentiseringsuppgifterna för ditt privata register som GitHub-hemligheter. Den genererade arbetsflödesfilen kör [åtgärden Azure/docker-login](https://github.com/Azure/docker-login) för att logga in med ditt privata register och kör sedan `docker push` för att distribuera till den.
- Lägger till publiceringsprofilen för din app som en GitHub-hemlighet. Den genererade arbetsflödesfilen använder den här hemligheten för att autentisera med App Service och kör sedan [åtgärden Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) för att konfigurera den uppdaterade avbildningen, vilket utlöser en omstart av appen för att hämta den uppdaterade avbildningen.
- Samlar in information från [arbetsflödets körningsloggar](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) och visar den **på** fliken Loggar i appens **distributionscenter.**

Du kan anpassa GitHub Actions bygga providern på följande sätt:

- Anpassa arbetsflödesfilen när den har genererats på din GitHub-lagringsplats. Mer information finns i [Arbetsflödessyntax för GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Se bara till att arbetsflödet slutar med åtgärden [Azure/webapps-deploy för](https://github.com/Azure/webapps-deploy) att utlösa en omstart av appen.
- Om den valda grenen är skyddad kan du fortfarande förhandsgranska arbetsflödesfilen utan att spara konfigurationen. Lägg sedan till den och nödvändiga GitHub-hemligheterna i din lagringsplats manuellt. Den här metoden ger dig inte loggintegrering med Azure Portal.
- I stället för en publiceringsprofil distribuerar du med hjälp [av ett huvudnamn för](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) tjänsten Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Autentisera med ett huvudnamn för tjänsten

Den här valfria konfigurationen ersätter standardautentisering med publiceringsprofiler i den genererade arbetsflödesfilen.

**Generera** ett huvudnamn för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) I följande exempel ersätter *\<subscription-id>* du , och med dina egna *\<group-name>* *\<app-name>* värden. **Spara** hela JSON-utdata för nästa steg, inklusive den översta nivån `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Av säkerhetsskäl beviljar du den lägsta nödvändiga åtkomsten till tjänstens huvudnamn. Omfånget i föregående exempel är begränsat till den App Service appen och inte hela resursgruppen.

I [GitHub](https://github.com/) **bläddrar du** till din lagringsplats och **väljer sedan Inställningar** **> Hemligheter > Lägg till en ny hemlighet**. **Klistra** in hela JSON-utdata från Azure CLI-kommandot i hemlighetens värdefält. **Ge** hemligheten ett namn som `AZURE_CREDENTIALS` .

I arbetsflödesfilen som genereras av **Distributionscenter** **ändrar du** steget med kod som i `azure/webapps-deploy` följande exempel:

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

Om du vill konfigurera containerregistret och Docker-avbildningen **kör du** [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set).

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
Om du vill konfigurera en app med  flera containrar (Docker Compose) förbereder du en Docker Compose-fil lokalt och kör  [sedan az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) med `--multicontainer-config-file` parametern . Om docker Compose-filen innehåller privata avbildningar lägger **du till** parametrar enligt `--docker-registry-server-*` föregående exempel.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Om du vill konfigurera CI/CD från containerregistret till din app kör **du** [az webapp deployment container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config) med `--enable-cd` parametern . Kommandot matar ut webhook-URL:en, men du måste skapa webhooken i registret manuellt i ett separat steg. Följande exempel aktiverar CI/CD i din app och använder sedan webhook-URL:en i utdata för att skapa webhooken i Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Fler resurser

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Snabbstart: Köra en anpassad container på App Service](quickstart-custom-container.md)
* [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)
* [Konfigurera anpassade containrar](configure-custom-container.md)
* [Arbetsflöden för åtgärder som ska distribueras till Azure](https://github.com/Azure/actions-workflow-samples)
