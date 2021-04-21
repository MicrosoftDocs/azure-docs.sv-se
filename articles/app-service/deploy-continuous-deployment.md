---
title: Konfigurera kontinuerlig distribution
description: Lär dig hur du aktiverar CI/CD för Azure App Service från GitHub, BitBucket, Azure Repos eller andra lagringsplatsen. Välj den bygg-pipeline som passar dina behov.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 633d62fc69c516b482d5749a07052337dc71f567
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789491"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service

[Azure App Service](overview.md) kontinuerlig distribution från [GitHub-,](https://help.github.com/articles/create-a-repo) [BitBucket-](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)och [Azure Repos-lagringsplatsen](/azure/devops/repos/git/creatingrepo) genom att hämta de senaste uppdateringarna.

> [!NOTE]
> Sidan **Development Center (klassisk)** i Azure Portal, som är den gamla distributionsupplevelsen, kommer att bli inaktuell i mars 2021. Den här ändringen påverkar inte befintliga distributionsinställningar i din app och du kan fortsätta att hantera appdistributionen på **sidan Distributionscenter.**

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Konfigurera distributionskälla

1. I [Azure Portal](https://portal.azure.com)navigerar du till hanteringssidan för din App Service appen.

1. Klicka på Inställningar för **Distributionscenter på den vänstra**  >  **menyn.** 

1. I **Källa** väljer du något av CI/CD-alternativen.

    ![Visar hur du väljer distributionskälla i Distributionscenter för Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Välj den flik som motsvarar ditt val för stegen.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) är standardbyggprovidern. Om du vill ändra den **klickar du på** Ändra provider App Service Build  >  **Service** (Kudu) > **OK.**

    > [!NOTE]
    > Om du vill använda Azure Pipelines som byggprovider App Service din app ska du inte konfigurera den i App Service. Konfigurera i stället CI/CD direkt från Azure Pipelines. Alternativet **Azure Pipelines** pekar bara åt rätt håll.

1. Om du distribuerar från GitHub för första gången klickar du på **Auktorisera** och följer anvisningarna för auktorisering. Om du vill distribuera från en annan användares lagringsplats klickar du på **Ändra konto.**

1. När du har auktoriserat ditt Azure-konto med GitHub väljer du **organisation,** **lagringsplats** och **gren för** att konfigurera CI/CD för.

1. När GitHub Actions är den valda versionsprovidern kan du välja  den arbetsflödesfil som du vill använda med listrutan Körningsstack **och** Version. Azure sparar den här arbetsflödesfilen till den valda GitHub-lagringsplatsen för att hantera bygg- och distributionsuppgifter. Om du vill se filen innan du sparar ändringarna klickar du på **Förhandsgranska fil**.

    > [!NOTE]
    > App Service identifierar [språkstackinställningen för din](configure-common.md#configure-language-stack-settings) app och väljer den lämpligaste arbetsflödesmallen. Om du väljer en annan mall kan den distribuera en app som inte körs korrekt. Mer information finns i Så [här fungerar GitHub Actions-byggarprovidern.](#how-the-github-actions-build-provider-works)

1. Klicka på **Spara**.
   
    Nya genomföranden i den valda lagringsplatsen och grenen distribueras nu kontinuerligt App Service din app. Du kan spåra genomföranden och distributioner på **fliken** Loggar.

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

BitBucket-integreringen använder App Service Build Services (Kudu) för att skapa automatisering.

4. Om du distribuerar från BitBucket för första gången klickar du på **Auktorisera** och följer anvisningarna för auktorisering. Om du vill distribuera från en annan användares lagringsplats klickar du på **Ändra konto.**

1. För Bitbucket väljer du den Bitbucket **Team,** **Lagringsplats** och **Gren som** du vill distribuera kontinuerligt.

1. Klicka på **Spara**.
   
    Nya genomföranden i den valda lagringsplatsen och grenen distribueras nu kontinuerligt App Service din app. Du kan spåra genomföranden och distributioner på **fliken** Loggar.
   
# <a name="local-git"></a>[Lokal Git](#tab/local)

Se [Lokal Git-distribution för att Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure-lagringsplatser](#tab/repos)

> [!NOTE]
> Azure Repos som distributionskälla har stöd för Windows-appar.
>

4. App Service Build Service (Kudu) är standardbyggprovidern.

    > [!NOTE]
    > Om du vill använda Azure Pipelines som byggprovider för din App Service ska du inte konfigurera den i App Service. Konfigurera i stället CI/CD direkt från Azure Pipelines. Alternativet **Azure Pipelines** pekar bara åt rätt håll.

1. Välj den **Azure DevOps-organisation,** **projekt,** **lagringsplats** och **gren som** du vill distribuera kontinuerligt. 

    Om din DevOps-organisation inte visas är den ännu inte länkad till din Azure-prenumeration. Mer information finns i Skapa [en Azure-tjänstanslutning.](/azure/devops/pipelines/library/connect-to-azure)

-----

## <a name="disable-continuous-deployment"></a>Inaktivera kontinuerlig distribution

1. I [Azure Portal](https://portal.azure.com)navigerar du till hanteringssidan för din App Service appen.

1. På den vänstra menyn klickar du på **Inställningar för**  >  **Distributionscenter Koppla**  >  **från**. 

    ![Visar hur du kopplar från synkroniseringen av molnmappen med App Service-appen i Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Som standard bevaras GitHub Actions arbetsflödesfilen på lagringsplatsen, men den fortsätter att utlösa distributionen till din app. Om du vill ta bort den från lagringsplatsen väljer du **Ta bort arbetsflödesfil**.

1. Klicka på **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Så här GitHub Actions-byggarprovidern

Den GitHub Actions är ett alternativ för [CI/CD från GitHub](#configure-deployment-source)och gör följande för att konfigurera CI/CD:

- Sätter in en GitHub Actions arbetsflödesfil på din GitHub-lagringsplats för att hantera bygg- och distributionsuppgifter till App Service.
- Lägger till publiceringsprofilen för din app som en GitHub-hemlighet. Arbetsflödesfilen använder den här hemligheten för att autentisera med App Service.
- Samlar in information från [arbetsflödets körningsloggar](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) och visar den på **fliken** Loggar i appens **distributionscenter.**

Du kan anpassa GitHub Actions på följande sätt:

- Anpassa arbetsflödesfilen när den har genererats på GitHub-lagringsplatsen. Mer information finns i [Arbetsflödessyntax för GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Se bara till att arbetsflödet distribueras till App Service med [åtgärden azure/webapps-deploy.](https://github.com/Azure/webapps-deploy)
- Om den valda grenen är skyddad kan du fortfarande förhandsgranska arbetsflödesfilen utan att spara konfigurationen och sedan lägga till den manuellt på din lagringsplats. Den här metoden ger dig inte loggintegrering med Azure Portal.
- I stället för en publiceringsprofil distribuerar du med hjälp [av ett huvudnamn för](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) tjänsten Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Autentisera med ett huvudnamn för tjänsten

Den här valfria konfigurationen ersätter standardautentisering med publiceringsprofiler i den genererade arbetsflödesfilen.

1. Generera ett huvudnamn för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) I följande exempel ersätter *\<subscription-id>* du , och med dina egna *\<group-name>* *\<app-name>* värden:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Av säkerhetsskäl beviljar du den lägsta nödvändiga åtkomsten till tjänstens huvudnamn. Omfånget i föregående exempel är begränsat till den App Service appen och inte hela resursgruppen.
    
1. Spara hela JSON-utdata för nästa steg, inklusive den översta nivån `{}` .

1. I [GitHub bläddrar](https://github.com/)du till din lagringsplats och **väljer Inställningar > Hemligheter > Lägg till en ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i hemlighetens värdefält. Ge hemligheten ett namn som `AZURE_CREDENTIALS` .

1. I arbetsflödesfilen som genereras av **distributionscentret** ändrar du steget med kod som i följande exempel (som ändras från en `azure/webapps-deploy` Node.js arbetsflödesfil):

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
    
## <a name="deploy-from-other-repositories"></a>Distribuera från andra lagringsplatsen

För Windows-appar kan du manuellt konfigurera kontinuerlig distribution från en Git- eller Mercurial-molnlagringsplats som portalen inte stöder direkt, till exempel [GitLab.](https://gitlab.com/) Du gör det genom att välja Extern Git i **listrutan** Källa. Mer information finns i Konfigurera [kontinuerlig distribution med manuella steg.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="more-resources"></a>Fler resurser

* [Distribuera från Azure Pipelines till Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Undersöka vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda Azure PowerShell](/powershell/azure/)
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)
