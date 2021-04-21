---
title: Konfigurera autentiseringsuppgifter för distribution
description: Lär dig vilka typer av autentiseringsuppgifter för distribution Azure App Service och hur du konfigurerar och använder dem.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 008bfa58c117fc1b43227ba73902d921cec25795
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830583"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
För att skydda appdistributionen från en lokal [dator Azure App Service](./overview.md) två typer av autentiseringsuppgifter för [lokal Git-distribution](deploy-local-git.md) och [FTP/S-distribution.](deploy-ftp.md) Dessa autentiseringsuppgifter är inte samma som autentiseringsuppgifterna för din Azure-prenumeration.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> Sidan **Development Center (klassisk)** i Azure Portal, som är den gamla distributionsupplevelsen, kommer att bli inaktuell i mars 2021. Den här ändringen påverkar inte befintliga distributionsinställningar i din app och du kan fortsätta att hantera appdistributionen på **sidan Distributionscenter.**

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Konfigurera autentiseringsuppgifter för användaromfång

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör kommandot [az webapp deployment user set.](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) Ersätt \<username> och med ett användarnamn och lösenord för \<password> distributionsanvändaren. 

- Användarnamnet måste vara unikt i Azure och för lokala Git-pushar får det inte innehålla @-symbolen. 
- Lösenordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar lösenordet som `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Du kan inte konfigurera autentiseringsuppgifterna för användaromfång med Azure PowerShell. Använd en annan metod eller överväg att använda [autentiseringsuppgifter för programomfång.](#appscope) 

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Du kan konfigurera dina autentiseringsuppgifter för användaromfång på valfri [appresurssida.](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) Oavsett i vilken app du konfigurerar dessa autentiseringsuppgifter gäller den för alla appar för alla prenumerationer i ditt Azure-konto. 

I [Azure Portal](https://portal.azure.com)måste du ha minst en app innan du kan komma åt sidan med autentiseringsuppgifter för distribution. Så här konfigurerar du autentiseringsuppgifter för användaromfång:

1. I den vänstra menyn i appen väljer du FTPS-autentiseringsuppgifter > **Deployment Center** eller  >   Lokala **Git/FTPS-autentiseringsuppgifter.**

    ![Visar hur du kan välja FTP-instrumentpanelen från distributionscentret i Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Rulla ned till **Användaromfång,** konfigurera **Användarnamn** **och Lösenord** och välj sedan **Spara.**

När du har angett dina autentiseringsuppgifter för distribution hittar du *Git-distributionens* användarnamn på appens **översiktssida.**

![Visar hur du hittar användarnamnet för Git-distributionen på appens översiktssida.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Om Git-distributionen har konfigurerats visas ett **Git-/distributionsnamn** på sidan. annars ett **FTP/distribution användarnamn**.

> [!NOTE]
> Azure visar inte ditt distributionslösenord för användaromfång. Om du glömmer lösenordet kan du återställa dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Använda autentiseringsuppgifter för användaromfång med FTP/FTPS

Autentisering till en FTP/FTPS-slutpunkt med autentiseringsuppgifter för användaromfång kräver ett användarnamn i följande format: `<app-name>\<user-name>`

Eftersom autentiseringsuppgifterna för användaromfång är länkade till användaren och inte en specifik resurs måste användarnamnet ha det här formatet för att inloggningsåtgärden ska dirigeras till rätt appslutpunkt.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Hämta autentiseringsuppgifter för programomfång

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Hämta autentiseringsuppgifterna för programomfånget med [kommandot az webapp deployment list-publishing-profiles.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) Exempel:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

För [lokal Git-distribution](deploy-local-git.md)kan du också använda kommandot [az webapp deployment list-publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) för att hämta en Fjärransluten URI för Git för din app, med autentiseringsuppgifterna för programomfånget redan inbäddade. Exempel:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Hämta autentiseringsuppgifterna för programomfånget med hjälp av kommandot [Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) Exempel:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Välj FTPS-autentiseringsuppgifter för **Distributionscenter** eller Lokala  >  **Git/FTPS-autentiseringsuppgifter** på den vänstra menyn i appen.

    ![Visar hur du kan välja FTP-instrumentpanelen från Distributionscenter i Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. I avsnittet **Programomfång** väljer du länken **Kopiera** för att kopiera användarnamnet eller lösenordet.

-----

## <a name="reset-application-scope-credentials"></a>Återställa autentiseringsuppgifter för programomfång

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Återställ autentiseringsuppgifterna för programomfånget med [kommandot az resource invoke-action:](/cli/azure/resource#az_resource_invoke_action)

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Återställ autentiseringsuppgifterna för programomfånget [med kommandot Invoke-AzResourceAction:](/powershell/module/az.resources/invoke-azresourceaction)

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. På den vänstra menyn i appen väljer du **FTPS-autentiseringsuppgifter för**  >  **distributionscenter eller** **Lokala Git/FTPS-autentiseringsuppgifter.**

    ![Visar hur du kan välja FTP-instrumentpanelen från distributionscentret i Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. I avsnittet **Programomfång** väljer du **Återställ**.

-----

## <a name="disable-basic-authentication"></a>Inaktivera grundläggande autentisering

Vissa organisationer måste uppfylla säkerhetskraven och inaktiverar i stället åtkomst via FTP eller WebDeploy. På så sätt kan organisationens medlemmar bara komma åt sina App Services via API:er som styrs av Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Om du vill inaktivera FTP-åtkomst till platsen kör du följande CLI-kommando. Ersätt platshållarna med resursgruppen och platsnamnet. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Om du vill bekräfta att FTP-åtkomsten är blockerad kan du försöka autentisera med hjälp av en FTP-klient, till exempel FileZilla. Om du vill hämta autentiseringsuppgifterna för publicering går du till översiktsbladet på webbplatsen och klickar på Ladda ned publiceringsprofil. Använd filens FTP-värdnamn, användarnamn och lösenord för att autentisera, så får du ett 401-felsvar som anger att du inte har behörighet.

### <a name="webdeploy-and-scm"></a>WebDeploy och SCM

Om du vill inaktivera grundläggande autentiseringsåtkomst till WebDeploy-porten och SCM-platsen kör du följande CLI-kommando. Ersätt platshållarna med resursgruppen och platsnamnet. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Om du vill bekräfta att autentiseringsuppgifterna för publiceringsprofilen är blockerade i WebDeploy kan du försöka publicera en [webbapp med hjälp Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Inaktivera åtkomst till API:et

API:et i föregående avsnitt backas upp med rollbaserad åtkomstkontroll [](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) i Azure (Azure RBAC), vilket innebär att du kan skapa en anpassad roll och tilldela användare med lägre behörighet till rollen så att de inte kan aktivera grundläggande autentisering på alla webbplatser. Följ dessa instruktioner för att [konfigurera den anpassade rollen.](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)

Du kan också använda [Azure Monitor för att](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) granska alla lyckade autentiseringsförfrågningar och använda [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) för att framtvinga den här konfigurationen för alla platser i din prenumeration.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder dessa autentiseringsuppgifter för att distribuera din app [från lokal Git](deploy-local-git.md) eller via [FTP/S.](deploy-ftp.md)
