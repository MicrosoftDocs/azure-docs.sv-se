---
title: Konfigurera autentiseringsuppgifter för distribution
description: Lär dig vilka typer av autentiseringsuppgifter för distributionen som finns Azure App Service och hur du konfigurerar och använder dem.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: e5793d21f27128162095e2d86e13006c5b6e7b7c
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008001"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
[Azure App Service](./overview.md) stöder två typer av autentiseringsuppgifter för [lokal Git-distribution](deploy-local-git.md) och [FTP/S-distribution](deploy-ftp.md). Autentiseringsuppgifterna är inte desamma som dina autentiseringsuppgifter för Azure-prenumerationen.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Konfigurera autentiseringsuppgifter på användar nivå

Du kan konfigurera dina autentiseringsuppgifter på användar nivå på alla appars [resurs sidor](../azure-resource-manager/management/manage-resources-portal.md#manage-resources). Oavsett vilken app du konfigurerar autentiseringsuppgifterna för, gäller den för alla appar och för alla prenumerationer i ditt Azure-konto. 

### <a name="in-the-cloud-shell"></a>I Cloud Shell

Om du vill konfigurera distributions användaren i [Cloud Shell](https://shell.azure.com)kör du kommandot [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) . Ersätt \<username> och \<password> med ett användar namn och lösen ord för distributions användare. 

- Användar namnet måste vara unikt inom Azure, och för lokala git-push-meddelanden får inte innehålla symbolen @. 
- Lösen ordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar lösen ordet som `null` . Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

### <a name="in-the-portal"></a>I portalen

I Azure Portal måste du ha minst en app innan du kan komma åt sidan autentiseringsuppgifter för distribution. Så här konfigurerar du dina autentiseringsuppgifter på användar nivå:

1. I [Azure Portal](https://portal.azure.com)väljer du **app Services**  >  **\<any_app>**  >  **distributions Center**  >  **FTP-**  >  **instrumentpanel** på menyn till vänster.

    ![Visar hur du kan välja FTP-instrumentpanelen från distributions centret i Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

    Eller, om du redan har konfigurerat Git-distribution väljer du **app Services**  >  **&lt; any_app>**  >  **distributions Center**  >  **FTP/autentiseringsuppgifter**.

    ![Visar hur du kan välja FTP-instrumentpanelen från distributions centret i Azure App Services för din konfigurerade Git-distribution.](./media/app-service-deployment-credentials/access-with-git.png)

2. Välj **användarautentiseringsuppgifter, konfigurera användar namn** och lösen ord och välj sedan **Spara autentiseringsuppgifter**.

När du har angett dina autentiseringsuppgifter för distribution kan du hitta användar namnet för *git* -distributionen på appens **översikts** sida.

![Visar hur du hittar användar namnet för git-distributionen på appens översikts sida.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Om Git-distribution har kon figurer ATS visar sidan ett **användar namn för Git/distribution**; annars är ett **FTP-/distributions användar namn**.

> [!NOTE]
> Azure visar inte lösen ordet för distribution på användar nivå. Om du glömmer bort lösen ordet kan du återställa dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Använd autentiseringsuppgifter på användar nivå med FTP/FTPS

Autentisering till en FTP/FTPS-slutpunkt med autentiseringsuppgifter på användar nivå kräver ett användar namn i följande format: `<app-name>\<user-name>`

Eftersom autentiseringsuppgifter på användar nivå är länkade till användaren och inte en speciell resurs, måste användar namnet vara i det här formatet för att dirigera inloggnings åtgärden till rätt app-slutpunkt.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Hämta och Återställ autentiseringsuppgifter på program nivå
Hämta autentiseringsuppgifter för program nivå:

1. I [Azure Portal](https://portal.azure.com)väljer du **app Services**  >  **&lt; any_app>**  >  **distributions Center**  >  **FTP/autentiseringsuppgifter** på menyn till vänster.

2. Välj **autentiseringsuppgifter för appen** och kopiera sedan användar namnet eller lösen ordet genom att välja **Kopiera** länk.

Om du vill återställa autentiseringsuppgifter för program nivå väljer du **Återställ autentiseringsuppgifter** i samma dialog ruta.

## <a name="disable-basic-authentication"></a>Inaktivera grundläggande autentisering

Vissa organisationer behöver uppfylla säkerhets kraven och ska i stället inaktivera åtkomst via FTP eller WebDeploy. På så sätt kan organisationens medlemmar bara komma åt sin App Services via API: er som styrs av Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Om du vill inaktivera FTP-åtkomst till platsen kör du följande CLI-kommando. Ersätt plats hållarna med din resurs grupp och plats namn. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

För att bekräfta att FTP-åtkomst är blockerad kan du försöka autentisera med en FTP-klient, till exempel FileZilla. Hämta autentiseringsuppgifterna för publicering genom att gå till bladet översikt på platsen och klicka på Hämta publicerings profil. Använd filens FTP-värdnamn, användar namn och lösen ord för att autentisera och du får ett 401-felsvar som anger att du inte har behörighet.

### <a name="webdeploy-and-scm"></a>WebDeploy och SCM

Kör följande CLI-kommando om du vill inaktivera åtkomst till grundläggande autentisering till WebDeploy-porten och SCM-platsen. Ersätt plats hållarna med din resurs grupp och plats namn. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Om du vill bekräfta att autentiseringsuppgifterna för publicerings profilen är blockerade på WebDeploy kan du försöka [publicera en webbapp med Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Inaktivera åtkomst till API: et

API: et i föregående avsnitt är en säkerhetsbaserad Azure-rollbaserad åtkomst kontroll (Azure RBAC), vilket innebär att du kan [skapa en anpassad roll](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) och tilldela priveldged användare till rollen så att de inte kan aktivera grundläggande autentisering på några platser. [Följ dessa instruktioner](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)för att konfigurera den anpassade rollen.

Du kan också använda [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) för att granska alla lyckade autentiseringsbegäranden och använda [Azure policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) för att genomdriva den här konfigurationen för alla platser i din prenumeration.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera din app från [lokal git](deploy-local-git.md) eller via [FTP/S](deploy-ftp.md).
