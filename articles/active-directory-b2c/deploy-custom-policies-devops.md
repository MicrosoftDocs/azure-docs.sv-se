---
title: Distribuera anpassade principer med Azure Pipelines
titleSuffix: Azure AD B2C
description: Lär dig hur du Azure AD B2C anpassade principer i en CI/CD-pipeline med hjälp av Azure Pipelines i Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529436"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Distribuera anpassade principer med Azure Pipelines

Genom att använda en pipeline för kontinuerlig integrering och leverans (CI/CD) som du konfigurerade i [Azure Pipelines][devops-pipelines]kan du inkludera dina anpassade Azure AD B2C-principer i automatiseringen av programvaruleverans och kodkontroll. När du distribuerar till Azure AD B2C miljöer, till exempel utvecklings-, test- och produktionsmiljöer, rekommenderar vi att du tar bort manuella processer och utför automatiserad testning med hjälp av Azure Pipelines.

Det krävs tre huvudsakliga steg för att göra det möjligt för Azure Pipelines att hantera anpassade principer inom Azure AD B2C:

1. Skapa en webbprogramregistrering i din Azure AD B2C klient
1. Konfigurera en Azure-lagringsplatsen
1. Konfigurera en Azure-pipeline

> [!IMPORTANT]
> Hantering Azure AD B2C anpassade principer med en  Azure Pipeline använder för närvarande förhandsgranskningsåtgärder som är tillgängliga Microsoft Graph `/beta` API-slutpunkten. Användning av dessa API:er i produktionsprogram stöds inte. Mer information finns i Microsoft Graph REST API [betaslutpunktsreferens.](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)

## <a name="prerequisites"></a>Förutsättningar

* [Azure AD B2C klientorganisation](tutorial-create-tenant.md)och autentiseringsuppgifter för en användare i katalogen med [rollen B2C IEF-principadministratör](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Anpassade principer som](tutorial-create-user-flows.md?pivots=b2c-custom-policy) laddats upp till din klientorganisation
* [Hanteringsapp](microsoft-graph-get-started.md) som registrerats i din klientorganisation med Microsoft Graph-API-behörighet *Policy.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)och åtkomst till ett [Azure DevOps Services-projekt][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Beviljandeflöde för klientautentiseringsuppgifter

Det scenario som beskrivs här använder tjänst-till-tjänst-anrop mellan Azure Pipelines och Azure AD B2C med [](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)hjälp av beviljandeflödet för OAuth 2.0-klientautentiseringsuppgifter. Det här beviljandeflödet gör det möjligt för en webbtjänst som Azure Pipelines (den konfidentiella klienten) att använda sina egna autentiseringsuppgifter i stället för att personifiera en användare för att autentisera vid anrop till en annan webbtjänst (i det här fallet Microsoft Graph-API:et). Azure Pipelines hämtar en token icke-interaktivt och gör sedan begäranden till Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registrera ett program för hanteringsuppgifter

Som vi nämnde [i Krav](#prerequisites)behöver du en programregistrering som dina PowerShell-skript – som körs av Azure Pipelines – kan använda för att komma åt resurserna i din klientorganisation.

Om du redan har en programregistrering som du använder för automatiseringsuppgifter ser du till att den har beviljats **behörigheten Microsoft Graph**  >  **Policy**  >  **Policy.ReadWrite.TrustFramework** i **API-behörigheterna** för appregistreringen.

Anvisningar om hur du registrerar ett hanteringsprogram finns i [Hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurera en Azure-lagringsplatsen

När du har registrerat ett hanteringsprogram är du redo att konfigurera en lagringsplats för dina principfiler.

1. Logga in på din Azure DevOps Services-organisation.
1. [Skapa ett nytt projekt][devops-create-project] eller välj ett befintligt projekt.
1. I projektet navigerar du **till Lagringsplatsen** och väljer **sidan** Filer. Välj en befintlig lagringsplats eller skapa en för den här övningen.
1. Skapa en mapp med *namnet B2CAssets*. Ge platshållarfilen namnet *README.md* **spara och** spara filen. Du kan ta bort den här filen senare om du vill.
1. Lägg till Azure AD B2C principfiler i *mappen B2CAssets.* Detta inkluderar *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* och andra principer som du har skapat. Registrera filnamnet för varje Azure AD B2C principfil för användning i ett senare steg (de används som PowerShell-skriptargument).
1. Skapa en mapp med *namnet Skript* i lagringsplatsens rotkatalog och ge platshållarfilen *namnetDeployToB2c.ps1*. Spara inte filen nu. Du kommer att göra det i ett senare steg.
1. Klistra in följande PowerShell-skript *iDeployToB2c.ps1* och **spara** sedan filen. Skriptet hämtar en token från Azure AD och anropar Microsoft Graph-API:et för att ladda upp principerna i *mappen B2CAssets* till din Azure AD B2C klientorganisation.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Konfigurera din Azure-pipeline

När lagringsplatsen har initierats och fyllts i med dina anpassade principfiler är du redo att konfigurera lanseringspipelinen.

### <a name="create-pipeline"></a>Skapa pipeline

1. Logga in på din Azure DevOps Services-organisation och gå till projektet.
1. I projektet väljer du **Pipelines Releases**  >  New pipeline (Nya **pipelines).**  >  
1. Under **Välj en mall** väljer du Tomt **jobb.**
1. Ange ett **fasnamn,** till *exempel DeployCustomPolicies,* och stäng sedan fönstret.
1. Välj **Lägg till en artefakt** och välj Azure **Repository** under **Källtyp.**
    1. Välj den källdatabas som innehåller *mappen Skript* som du har fyllt i med PowerShell-skriptet.
    1. Välj en **standardgren**. Om du skapade en ny lagringsplats i föregående avsnitt är standardgrenen *master*.
    1. Lämna inställningen **Standardversion** på *Senaste från standardgrenen*.
    1. Ange ett **källalias** för lagringsplatsen. Till exempel *policyRepo*. Ta inte med några blanksteg i aliasnamnet.
1. Välj **Lägg till**
1. Byt namn på pipelinen så att den återspeglar dess avsikt. Du kan till *exempel distribuera en pipeline för anpassad princip.*
1. Välj **Spara** för att spara pipelinekonfigurationen.

### <a name="configure-pipeline-variables"></a>Konfigurera pipelinevariabler

1. Välj **fliken Variabler.**
1. Lägg till följande variabler under **Pipeline-variabler och** ange deras värden enligt givna:

    | Name | Värde |
    | ---- | ----- |
    | `clientId` | **Program-ID (klient)** för det program som du registrerade tidigare. |
    | `clientSecret` | Värdet för **klienthemligheten som** du skapade tidigare. <br /> Ändra variabeltypen till **hemlighet** (välj låsikonen). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, *där your-b2c-tenant* är namnet på din Azure AD B2C klientorganisation. |

1. Välj **Spara** för att spara variablerna.

### <a name="add-pipeline-tasks"></a>Lägga till pipeline-uppgifter

Lägg sedan till en uppgift för att distribuera en principfil.

1. Välj **fliken** Uppgifter.
1. Välj **Agentjobb** och välj sedan plustecknet ( **+** ) för att lägga till en uppgift i agentjobbet.
1. Sök efter och välj **PowerShell**. Välj inte "Azure PowerShell", "PowerShell på måldatorer" eller någon annan PowerShell-post.
1. Välj den nyligen **tillagda PowerShell-skriptuppgiften.**
1. Ange följande värden för PowerShell-skriptuppgiften:
    * **Uppgiftsversion:** 2.*
    * **Visningsnamn:** Namnet på den princip som den här aktiviteten ska ladda upp. Du kan till *exempel B2C_1A_TrustFrameworkBase*.
    * **Typ:** Filsökväg
    * **Skriptsökväg:** Välj ellipsen (**_..._* _), navigera till mappen _Scripts* och välj sedan *DeployToB2C.ps1* fil.
    * **Argument:**

        Ange följande värden för **Argument**. Ersätt `{alias-name}` med aliaset som du angav i föregående avsnitt.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Om aliaset som du angav till exempel är *policyRepo* ska argumentraden vara:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Välj **Spara** för att spara agentjobbet.

Den uppgift som du just lade till *laddar upp en* principfil till Azure AD B2C. Innan du fortsätter utlöser du jobbet manuellt (**Skapa version**) för att säkerställa att det slutförs korrekt innan du skapar ytterligare uppgifter.

Om uppgiften slutförs korrekt lägger du till distributionsuppgifter genom att utföra föregående steg för var och en av de anpassade principfilerna. Ändra `-PolicyId` `-PathToFile` argumentvärdena och för varje princip.

`PolicyId`är ett värde som finns i början av en XML-principfil i noden TrustFrameworkPolicy. I följande princip `PolicyId` är XML till exempel *B2C_1A_TrustFrameworkBase*:

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

När du kör agenterna och laddar upp principfilerna ska du kontrollera att de har laddats upp i den här ordningen:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Den Identity Experience Framework tillämpar den här ordningen när filstrukturen bygger på en hierarkisk kedja.

## <a name="test-your-pipeline"></a>Testa din pipeline

Så här testar du din lanseringspipeline:

1. Välj **Pipelines** och **sedan Versioner.**
1. Välj den pipeline som du skapade tidigare, till *exempel DeployCustomPolicies*.
1. Välj **Skapa version och** välj sedan Skapa **för** att köa versionen.

Du bör se en meddelandebanderoll som säger att en version har lagts i kö. Om du vill visa dess status väljer du länken i meddelandebanderollen eller väljer den i listan **på fliken** Versioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

* [Tjänst-till-tjänst-anrop med klientautentiseringsuppgifter](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
