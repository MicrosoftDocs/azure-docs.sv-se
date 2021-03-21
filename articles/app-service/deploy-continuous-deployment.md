---
title: Konfigurera kontinuerlig distribution
description: Lär dig hur du aktiverar CI/CD för att Azure App Service från GitHub, BitBucket, Azure databaser eller andra databaser. Välj den build-pipeline som passar dina behov.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 52f0db739cff9614dc4e9f5ef71d582e926fc65a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470276"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service

[Azure App Service](overview.md) aktiverar kontinuerlig distribution från [GitHub](https://help.github.com/articles/create-a-repo), [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)och [Azure databaser](/azure/devops/repos/git/creatingrepo) -databaser genom att hämta de senaste uppdateringarna.

> [!NOTE]
> **Utvecklings Center sidan (klassisk)** i Azure Portal, vilket är den gamla distributions miljön, kommer att bli inaktuell i mars 2021. Den här ändringen påverkar inte några befintliga distributions inställningar i appen och du kan fortsätta att hantera distribution av appar på sidan **distributions Center** .

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Konfigurera distributions källa

1. Gå till hanterings sidan för din App Service-app i [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn klickar du på Inställningar för **distributions Center**  >  . 

1. I **källa** väljer du något av CI/CD-alternativen.

    ![Visar hur du väljer distributions källa i distributions Center för Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Välj den flik som motsvarar ditt val av steg.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub-åtgärder](#how-the-github-actions-build-provider-works) är standard build-providern. Ändra det genom att klicka på **ändra Provider**  >  **App Service Build Service** (kudu) > **OK**.

    > [!NOTE]
    > Om du vill använda Azure-pipelines som build-Provider för din App Service-app konfigurerar du den inte i App Service. Konfigurera i stället CI/CD direkt från Azure-pipeliner. Alternativet för **Azure-pipeliner** pekar precis i rätt riktning.

1. Om du distribuerar från GitHub för första gången klickar du på **auktorisera** och följer behörighets frågorna. Om du vill distribuera från en annan användares lagrings plats klickar du på **Ändra konto**.

1. När du har auktoriserat ditt Azure-konto med GitHub väljer du **organisation**, **lagrings plats** och **gren** för att konfigurera CI/CD för.

1. När GitHub-åtgärder är den valda build-providern kan du välja den arbets flödes fil som du vill använda i list rutorna för **körnings stack** och **version** . Azure sparar den här arbets flödes filen i din valda GitHub-lagringsplats för att hantera skapande och distribution av uppgifter. Om du vill se filen innan du sparar ändringarna klickar du på **Förhandsgranska fil**.

    > [!NOTE]
    > App Service identifierar [språk Stacks inställningen](configure-common.md#configure-language-stack-settings) för appen och väljer den lämpligaste arbets flödes mal len. Om du väljer en annan mall kan den distribuera en app som inte fungerar som den ska. Mer information finns i [så här fungerar providern för GitHub-åtgärder](#how-the-github-actions-build-provider-works).

1. Klicka på **Spara**.
   
    Nya incheckningar i den valda databasen och grenen distribueras nu kontinuerligt till din App Service-app. Du kan spåra incheckningar och distributioner på fliken **loggar** .

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

BitBucket-integreringen använder App Service build-tjänster (kudu) för att bygga automatisering.

4. Om du distribuerar från BitBucket för första gången klickar du på **auktorisera** och följer behörighets frågorna. Om du vill distribuera från en annan användares lagrings plats klickar du på **Ändra konto**.

1. För BitBucket väljer du det BitBucket- **team**, den **lagrings plats** och den **gren** som du vill distribuera kontinuerligt.

1. Klicka på **Spara**.
   
    Nya incheckningar i den valda databasen och grenen distribueras nu kontinuerligt till din App Service-app. Du kan spåra incheckningar och distributioner på fliken **loggar** .
   
# <a name="local-git"></a>[Lokal Git](#tab/local)

Se [lokal Git-distribution till Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure-lagringsplatser](#tab/repos)

> [!NOTE]
> Azure databaser som distributions källa är stöd för Windows-appar.
>

4. App Service Build Service (kudu) är standard versionen för build.

    > [!NOTE]
    > Om du vill använda Azure-pipelines som build-Provider för din App Service-app konfigurerar du den inte i App Service. Konfigurera i stället CI/CD direkt från Azure-pipeliner. Alternativet för **Azure-pipeliner** pekar precis i rätt riktning.

1. Välj den **Azure DevOps-organisation**, det **projekt**, den **lagrings plats** och den **gren** som du vill distribuera kontinuerligt. 

    Om din DevOps-organisation inte finns med i listan, är den ännu inte länkad till din Azure-prenumeration. Mer information finns i [skapa en Azure-tjänst anslutning](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Inaktivera kontinuerlig distribution

1. Gå till hanterings sidan för din App Service-app i [Azure Portal](https://portal.azure.com).

1. På den vänstra menyn klickar du på **distributions Center**  >  **Inställningar**  >  **Koppla från**. 

    ![Visar hur du kopplar från din mapp för molnappar med App Service-appen i Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Som standard bevaras arbets flödes filen för GitHub-åtgärder i din lagrings plats, men den fortsätter att utlösa distribution till din app. Om du vill ta bort den från lagrings platsen väljer du **ta bort arbets flödes fil**.

1. Klicka på **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Så här fungerar providern för GitHub-åtgärder

GitHub Actions build Provider är ett alternativ för [CI/CD från GitHub](#configure-deployment-source)och gör följande för att konfigurera CI/CD:

- Dearbeter en arbets flödes fil för GitHub-åtgärder i din GitHub-lagringsplats för att hantera skapande och distribution av aktiviteter till App Service.
- Lägger till publicerings profilen för appen som en GitHub-hemlighet. Arbets flödes filen använder den här hemligheten för att autentisera med App Service.
- Fångar information från [arbets flödets körnings loggar](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) och visar den på fliken **loggar** i appens **distributions Center**.

Du kan anpassa GitHub-åtgärdernas build-Provider på följande sätt:

- Anpassa arbets flödes filen när den har genererats i din GitHub-lagringsplats. Mer information finns i [syntax för arbets flöden för GitHub-åtgärder](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Se bara till att arbets flödet distribuerar till App Service med åtgärden [Azure/webapps – distribuera](https://github.com/Azure/webapps-deploy) .
- Om den valda grenen är skyddad kan du fortfarande förhandsgranska arbets flödes filen utan att spara konfigurationen, och sedan lägga till den manuellt i lagrings platsen. Den här metoden ger dig inte logg integrering med Azure Portal.
- Distribuera med ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) i Azure Active Directory i stället för en publicerings profil.

#### <a name="authenticate-with-a-service-principal"></a>Autentisera med ett huvud namn för tjänsten

Den här valfria konfigurationen ersätter standardautentiseringen med publicerings profiler i den genererade arbets flödes filen.

1. Generera ett huvud namn för tjänsten med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). I följande exempel ersätter *\<subscription-id>* , *\<group-name>* och *\<app-name>* med dina egna värden:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > För säkerhet ger du den lägsta åtkomst som krävs för tjänstens huvud namn. Omfånget i föregående exempel är begränsat till den särskilda App Service-appen och inte hela resurs gruppen.
    
1. Spara hela JSON-utdata för nästa steg, inklusive den översta nivån `{}` .

1. I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i fältet hemligt värde. Ge hemligheten ett namn som `AZURE_CREDENTIALS` .

1. I arbets flödes filen som genereras av **distributions Center** ändrar du `azure/webapps-deploy` steget med kod som i följande exempel (som ändras från en Node.js arbets flödes fil):

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
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Distribuera från andra databaser

För Windows-appar kan du manuellt konfigurera kontinuerlig distribution från en git-eller Mercurial-lagringsplats i molnet som portalen inte stöder direkt, till exempel [GitLab](https://gitlab.com/). Du gör det genom att välja extern git i list rutan **källa** . Mer information finns i [Konfigurera kontinuerlig distribution med hjälp av manuella steg](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Fler resurser

* [Distribuera från Azure pipelines till Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Undersök vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda Azure PowerShell](/powershell/azure/)
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)
