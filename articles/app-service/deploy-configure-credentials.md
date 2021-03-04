---
title: Konfigurera autentiseringsuppgifter för distribution
description: Lär dig vilka typer av autentiseringsuppgifter för distributionen som finns Azure App Service och hur du konfigurerar och använder dem.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c7d3c7c8b5da40a4e9ccd9085af5a850b9ebc3dd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052355"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
För att skydda program distribution från en lokal dator stöder [Azure App Service](./overview.md) två typer av autentiseringsuppgifter för [lokal Git-distribution](deploy-local-git.md) och [FTP/S-distribution](deploy-ftp.md). Autentiseringsuppgifterna är inte desamma som dina autentiseringsuppgifter för Azure-prenumerationen.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> **Utvecklings Center sidan (klassisk)** i Azure Portal, vilket är den gamla distributions miljön, kommer att bli inaktuell i mars 2021. Den här ändringen påverkar inte några befintliga distributions inställningar i appen och du kan fortsätta att hantera distribution av appar på sidan **distributions Center** .

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Konfigurera autentiseringsuppgifter för användar omfång

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör kommandot [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) . Ersätt \<username> och \<password> med ett användar namn och lösen ord för distributions användare. 

- Användar namnet måste vara unikt inom Azure, och för lokala git-push-meddelanden får inte innehålla symbolen @. 
- Lösen ordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar lösen ordet som `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Du kan inte konfigurera autentiseringsuppgifterna för användar området med Azure PowerShell. Använd en annan metod eller Överväg att [använda autentiseringsuppgifter för program-scope](#appscope). 

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Du kan konfigurera dina autentiseringsuppgifter för användar omfång på alla appars [resurs sidor](../azure-resource-manager/management/manage-resources-portal.md#manage-resources). Oavsett vilken app du konfigurerar autentiseringsuppgifterna för, gäller den för alla appar för alla prenumerationer i ditt Azure-konto. 

I [Azure Portal](https://portal.azure.com)måste du ha minst en app innan du kan komma åt sidan autentiseringsuppgifter för distribution. Så här konfigurerar du dina autentiseringsuppgifter för användar omfång:

1. I appens vänstra meny väljer du > **distributions Center**  >  **FTPS autentiseringsuppgifter** eller **lokala git/FTPS-autentiseringsuppgifter**.

    ![Visar hur du kan välja FTP-instrumentpanelen från distributions centret i Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Rulla ned till **användar omfång**, konfigurera **användar namn** och **lösen ord** och välj sedan **Spara**.

När du har angett dina autentiseringsuppgifter för distribution kan du hitta användar namnet för *git* -distributionen på appens **översikts** sida.

![Visar hur du hittar användar namnet för git-distributionen på appens översikts sida.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Om Git-distribution har kon figurer ATS visar sidan ett **användar namn för Git/distribution**; annars är ett **FTP-/distributions användar namn**.

> [!NOTE]
> Azure visar inte lösen ordet för distribution av användar omfång. Om du glömmer bort lösen ordet kan du återställa dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Använd autentiseringsuppgifter för användar område med FTP/FTPS

Autentisering till en FTP/FTPS-slutpunkt som använder autentiseringsuppgifter för användar omfång kräver ett användar namn i följande format: `<app-name>\<user-name>`

Eftersom autentiseringsuppgifter för användar omfång är länkade till användaren och inte en speciell resurs, måste användar namnet vara i det här formatet för att dirigera inloggnings åtgärden till rätt app-slutpunkt.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Hämta autentiseringsuppgifter för program omfång

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Hämta programmets autentiseringsuppgifter med hjälp av [AZ webapp Deployment List-Publishing-profils-](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) kommandot. Exempel:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

För [lokal Git-distribution](deploy-local-git.md)kan du också använda [AZ webapp Deployment List-Publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) för att hämta en git-fjärruri för din app, med autentiseringsuppgifterna för programomfång redan inbäddad. Exempel:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Hämta programmets autentiseringsuppgifter med kommandot [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . Exempel:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. I appens vänstra meny väljer du **distributions Center**  >  **FTPS autentiseringsuppgifter** eller **lokala git/FTPS-autentiseringsuppgifter**.

    ![Visar hur du kan välja FTP-instrumentpanelen från distributions centret i Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. I avsnittet **programområde** väljer du **kopierings** länken för att kopiera användar namnet eller lösen ordet.

-----

## <a name="reset-application-scope-credentials"></a>Återställ programautentiseringsuppgifter för program

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Återställ programmets autentiseringsuppgifter med hjälp av kommandot [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) :

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Återställ programmets autentiseringsuppgifter med [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) -kommandot:

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. I appens vänstra meny väljer du **distributions Center**  >  **FTPS autentiseringsuppgifter** eller **lokala git/FTPS-autentiseringsuppgifter**.

    ![Visar hur du kan välja FTP-instrumentpanelen från distributions centret i Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. I avsnittet **program omfattning** väljer du **Återställ**.

-----

## <a name="disable-basic-authentication"></a>Inaktivera grundläggande autentisering

Vissa organisationer behöver uppfylla säkerhets kraven och ska i stället inaktivera åtkomst via FTP eller WebDeploy. På så sätt kan organisationens medlemmar bara komma åt sin App Services via API: er som styrs av Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Om du vill inaktivera FTP-åtkomst till platsen kör du följande CLI-kommando. Ersätt plats hållarna med din resurs grupp och plats namn. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

För att bekräfta att FTP-åtkomst är blockerad kan du försöka autentisera med en FTP-klient, till exempel FileZilla. Hämta autentiseringsuppgifterna för publicering genom att gå till bladet översikt på platsen och klicka på Hämta publicerings profil. Använd filens FTP-värdnamn, användar namn och lösen ord för att autentisera och du får ett 401-felsvar som anger att du inte har behörighet.

### <a name="webdeploy-and-scm"></a>WebDeploy och SCM

Kör följande CLI-kommando om du vill inaktivera åtkomst till grundläggande autentisering till WebDeploy-porten och SCM-platsen. Ersätt plats hållarna med din resurs grupp och plats namn. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Om du vill bekräfta att autentiseringsuppgifterna för publicerings profilen är blockerade på WebDeploy kan du försöka [publicera en webbapp med Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Inaktivera åtkomst till API: et

API: et i föregående avsnitt är en säkerhetsbaserad Azure-rollbaserad åtkomst kontroll (Azure RBAC), vilket innebär att du kan [skapa en anpassad roll](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) och tilldela priveldged användare till rollen så att de inte kan aktivera grundläggande autentisering på några platser. [Följ dessa instruktioner](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)för att konfigurera den anpassade rollen.

Du kan också använda [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) för att granska alla lyckade autentiseringsbegäranden och använda [Azure policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) för att genomdriva den här konfigurationen för alla platser i din prenumeration.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera din app från [lokal git](deploy-local-git.md) eller via [FTP/S](deploy-ftp.md).
