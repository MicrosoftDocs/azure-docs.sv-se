---
title: Distribuera anpassade principer med Azure-pipeliner
titleSuffix: Azure AD B2C
description: Lär dig hur du distribuerar Azure AD B2C anpassade principer i en CI/CD-pipeline med hjälp av Azure pipelines i Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13f5f8da0bd58cef0974e8ea8f5f3c5172daa0ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928740"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Distribuera anpassade principer med Azure-pipeliner

Genom att använda en pipeline för kontinuerlig integrering och leverans (CI/CD) som du ställer in i [Azure-pipeline][devops-pipelines]kan du inkludera dina Azure AD B2C anpassade principer i din program varu leverans och kod kontroll automatisering. När du distribuerar till olika Azure AD B2C miljöer, t. ex. utveckling, testning och produktion, rekommenderar vi att du tar bort manuella processer och utför automatiserad testning med hjälp av Azure-pipeliner.

Det finns tre primära steg som krävs för att aktivera Azure-pipelines för att hantera anpassade principer i Azure AD B2C:

1. Skapa en registrering av webb program i din Azure AD B2C klient
1. Konfigurera en Azure-lagrings platsen
1. Konfigurera en Azure-pipeline

> [!IMPORTANT]
> Hantering av Azure AD B2C anpassade principer med en Azure-pipeline använder för närvarande för **hands versions** åtgärder som är tillgängliga i Microsoft Graph API- `/beta` slutpunkten. Användning av dessa API:er i produktionsprogram stöds inte. Mer information finns i referens för [Microsoft Graph REST API beta-slutpunkt](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Förutsättningar

* [Azure AD B2C klient organisation](tutorial-create-tenant.md)och autentiseringsuppgifter för en användare i katalogen med rollen [B2C IEF princip administratör](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Anpassade principer](custom-policy-get-started.md) har laddats upp till din klient
* [Hanterings appen](microsoft-graph-get-started.md) som registrerats i din klient organisation med behörighets principen Microsoft Graph-API *. readwrite. TrustFramework*
* [Azure pipeline](https://azure.microsoft.com/services/devops/pipelines/)och åtkomst till ett [Azure DevOps Services-projekt][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Flöde för beviljande av klientautentiseringsuppgifter

Scenariot som beskrivs här använder tjänst-till-tjänst-anrop mellan Azure-pipelines och Azure AD B2C genom att använda OAuth 2,0- [klientens autentiseringsuppgifter för beviljande av autentiseringsuppgifter](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Det här tilldelnings flödet tillåter en webb tjänst som Azure-pipelines (den konfidentiella klienten) att använda sina egna autentiseringsuppgifter i stället för att personifiera en användare för autentisering vid anrop till en annan webb tjänst (Microsoft Graph API, i det här fallet). Azure-pipelines får en token icke-interaktivt, och skickar sedan förfrågningar till Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registrera ett program för hanterings uppgifter

Som vi nämnt i [krav](#prerequisites)behöver du en program registrering som dina PowerShell-skript – som körs av Azure-pipelines – kan använda för att komma åt resurserna i din klient organisation.

Om du redan har en program registrering som du använder för automatiserings aktiviteter ser du till att den har beviljats **Microsoft Graph**  >  **princip**  >  **princip princip princip. readwrite. TrustFramework** -behörighet inom **API-behörigheterna** för appens registrering.

Anvisningar om hur du registrerar ett hanterings program finns i [hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurera en Azure-lagrings platsen

När ett hanterings program är registrerat är du redo att konfigurera en lagrings plats för principfiler.

1. Logga in på Azure DevOps Services-organisationen.
1. [Skapa ett nytt projekt][devops-create-project] eller Välj ett befintligt projekt.
1. I projektet navigerar du till **databaser** och väljer sidan **filer** . Välj en befintlig databas eller skapa en för den här övningen.
1. Skapa en mapp med namnet *B2CAssets*. Ge den nödvändiga plats hållar filen *Readme.MD* och **Spara** filen. Du kan ta bort den här filen senare, om du vill.
1. Lägg till dina Azure AD B2C princip-filer i mappen *B2CAssets* Detta omfattar *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* och andra principer som du har skapat. Registrera fil namnet för varje Azure AD B2C princip fil som ska användas i ett senare steg (de används som PowerShell-skript argument).
1. Skapa en mapp med namnet *scripts* i rot katalogen för lagrings platsen, namnge plats hållar filen *DeployToB2c.ps1*. Spara inte filen just nu, du kommer att göra det i ett senare steg.
1. Klistra in följande PowerShell-skript i *DeployToB2c.ps1* och **Spara** sedan filen. Skriptet hämtar en token från Azure AD och anropar Microsoft Graph-API: t för att överföra principerna i mappen *B2CAssets* till din Azure AD B2C-klient.

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

När du har initierat och fyllt i din databas med dina anpassade principfiler, är du redo att konfigurera pipelinen för version.

### <a name="create-pipeline"></a>Skapa pipeline

1. Logga in på din Azure DevOps Services-organisation och navigera till projektet.
1. I ditt projekt väljer du **pipelines**  >  **släpper**  >  **ny pipeline**.
1. Under **Välj en mall** väljer du **tomt jobb**.
1. Ange ett **namn på scenen**, till exempel *DeployCustomPolicies*, och stäng sedan fönstret.
1. Välj **Lägg till en artefakt** och välj **Azure-lagringsplats** under **typ av källa**.
    1. Välj käll databasen som innehåller den *skript* -mapp som du har fyllt i PowerShell-skriptet.
    1. Välj en **standard gren**. Om du har skapat en ny lagrings plats i föregående avsnitt är standard grenen *Master*.
    1. Lämna **standard versions** inställningen *senaste från standard grenen*.
    1. Ange ett **käll Ali Aset** för lagrings platsen. Till exempel *policyRepo*. Ta inte med några blank steg i aliasnamnet.
1. Välj **Lägg till**
1. Byt namn på pipelinen så att den återspeglar avsikten. Du kan till exempel *distribuera en anpassad princip pipeline*.
1. Välj **Spara** för att spara pipeline-konfigurationen.

### <a name="configure-pipeline-variables"></a>Konfigurera pipeline-variabler

1. Välj fliken **variabler** .
1. Lägg till följande variabler under **pipeline-variabler** och ange deras värden enligt vad som anges:

    | Name | Värde |
    | ---- | ----- |
    | `clientId` | **Program (klient) ID** för det program som du registrerade tidigare. |
    | `clientSecret` | Värdet för **klient hemligheten** som du skapade tidigare. <br /> Ändra variabel typen till **hemlighet** (Välj Lås ikonen). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, där *din-B2C-Tenant* är namnet på din Azure AD B2C klient. |

1. Spara variablerna genom att välja **Spara** .

### <a name="add-pipeline-tasks"></a>Lägg till pipeline-uppgifter

Lägg sedan till en aktivitet för att distribuera en princip fil.

1. Välj fliken **aktiviteter** .
1. Välj **Agent jobb** och välj sedan plus tecknet ( **+** ) för att lägga till en aktivitet till Agent jobbet.
1. Sök efter och välj **PowerShell**. Välj inte "Azure PowerShell", "PowerShell på mål datorer" eller någon annan PowerShell-post.
1. Välj nyligen tillagd **PowerShell-skript** aktivitet.
1. Ange följande värden för aktiviteten PowerShell-skript:
    * **Uppgifts version**: 2. *
    * **Visnings namn**: namnet på den princip som den här aktiviteten ska överföra. Till exempel *B2C_1A_TrustFrameworkBase*.
    * **Typ**: fil Sök väg
    * **Skript Sök väg**: Välj ellipsen (**_..._* _), navigera till mappen _Scripts * och välj sedan *DeployToB2C.ps1* filen.
    * **Ogiltiga**

        Ange följande värden för **argument**. Ersätt `{alias-name}` med det alias som du angav i föregående avsnitt.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Om det alias som du har angett exempelvis är *policyRepo*, ska argument raden vara:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Välj **Spara** för att spara Agent jobbet.

Uppgiften som du nyss lade till överför *en* princip fil till Azure AD B2C. Innan du fortsätter utlöser du jobbet manuellt (**Skapa version**) för att se till att det slutförs innan du skapar ytterligare aktiviteter.

Om uppgiften har slutförts lägger du till distributions uppgifter genom att utföra de föregående stegen för var och en av de anpassade principfiler. Ändra `-PolicyId` värdena och `-PathToFile` för varje princip.

`PolicyId`Är ett värde som finns i början av en XML-princip fil i TrustFrameworkPolicy-noden. Till exempel `PolicyId` är följande princip-XML *B2C_1A_TrustFrameworkBase*:

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

När du kör agenterna och laddar upp principfiler, se till att de överförs i följande ordning:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

I ramverket med identitets upplevelsen används den här ordningen eftersom fil strukturen bygger på en hierarkisk kedja.

## <a name="test-your-pipeline"></a>Testa din pipeline

Så här testar du din versions pipeline:

1. Välj **pipeliner** och sedan **versioner**.
1. Välj den pipeline som du skapade tidigare, till exempel *DeployCustomPolicies*.
1. Välj **Skapa version** och välj sedan **skapa** för att köa versionen.

Du bör se en meddelande banderoll som säger att en version har placerats i kö. Om du vill visa dess status väljer du länken i meddelande banderollen eller väljer den i listan på fliken **utgåvor** .

## <a name="next-steps"></a>Nästa steg

Läs mer om:

* [Tjänst-till-tjänst-anrop med klientautentiseringsuppgifterna](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
