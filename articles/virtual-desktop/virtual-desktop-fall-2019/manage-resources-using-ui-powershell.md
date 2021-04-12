---
title: Distribuera ett hanterings verktyg för Windows Virtual Desktop (klassisk) med tjänstens huvud namn – Azure
description: Distribuera hanterings verktyget för Windows Virtual Desktop (klassisk) med hjälp av PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 60eec4580e222123795db9554f56e74cb01c5257
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444453"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>Distribuera ett hanterings verktyg för Windows Virtual Desktop (klassiskt) med PowerShell

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows.

I den här artikeln visas hur du distribuerar hanterings verktyget med PowerShell.

## <a name="important-considerations"></a>Att tänka på

Varje Azure Active Directory (Azure AD)-klient organisations prenumeration måste ha en egen separat distribution av hanterings verktyget. Det här verktyget har inte stöd för Azure AD-scenarier för Business-to-Business (B2B).

Det här hanterings verktyget är ett exempel. Microsoft kommer att tillhandahålla viktiga säkerhets-och kvalitets uppdateringar. [Käll koden finns i GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Oavsett om du är kund eller partner rekommenderar vi att du anpassar verktyget för att uppfylla dina affärs behov.

Följande webbläsare är kompatibla med hanterings verktyget:

- Google Chrome 68 eller senare
- Microsoft Edge 40,15063 eller senare
- Mozilla Firefox 52,0 eller senare
- Safari 10 eller senare (endast macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Vad du behöver för att distribuera hanterings verktyget

Innan du distribuerar hanterings verktyget behöver du en Azure Active Directory (Azure AD)-användare för att skapa en app-registrering och distribuera hanterings gränssnittet. Den här användaren måste:

- Har behörighet att skapa resurser i din Azure-prenumeration
- Har behörighet att skapa ett Azure AD-program. Följ de här stegen för att kontrol lera om användaren har de behörigheter som krävs genom att följa instruktionerna i de [behörigheter som krävs](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

När du har distribuerat och konfigurerat hanterings verktyget rekommenderar vi att du ber användaren att starta användar gränssnittet för hantering för att se till att allt fungerar. Användaren som startar hanterings gränssnittet måste ha en roll tilldelning som gör det möjligt för dem att visa eller redigera Windows-klienten för virtuella skriv bord.

## <a name="set-up-powershell"></a>Konfigurera PowerShell

Kom igång genom att logga in på både AZ-och Azure AD PowerShell-modulerna. Så här loggar du in:

1. Öppna PowerShell som administratör och navigera till den katalog där du sparade PowerShell-skripten.
2. Logga in på Azure med ett konto som har ägar-eller deltagar behörighet för den Azure-prenumeration som du planerar att använda för att skapa hanterings verktyget genom att köra följande cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Kör följande cmdlet för att logga in på Azure AD med samma konto som du använde för AZ PowerShell-modulen:

    ```powershell
    Connect-AzureAD
    ```

4. Efter det navigerar du till mappen där du sparade de två PowerShell-skripten från RDS-Templates GitHub-lagrings platsen.

Behåll PowerShell-fönstret som du använde för att logga in öppna för att köra ytterligare PowerShell-cmdletar när du är inloggad.

## <a name="create-an-azure-active-directory-app-registration"></a>Skapa en Azure Active Directory app-registrering

För att kunna distribuera och konfigurera hanterings verktyget måste du först ladda ned följande PowerShell-skript från [GitHub-lagrings platsen för RDS-mallar](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Kör följande kommandon för att skapa appens registrering med nödvändiga API-behörigheter:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu när du har slutfört registreringen av Azure AD-appen kan du distribuera hanterings verktyget.

## <a name="deploy-the-management-tool"></a>Distribuera hanteringsverktyget

Kör följande PowerShell-kommandon för att distribuera hanterings verktyget och koppla det till tjänstens huvud namn som du nyss skapade:

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

När du har skapat webbappen måste du lägga till en omdirigerings-URI till Azure AD-programmet för att kunna logga in användare.

## <a name="set-the-redirect-uri"></a>Ange omdirigerings-URI

Kör följande PowerShell-kommandon för att hämta webbappens URL och ange den som omdirigerings-URI för autentisering (kallas även en svars-URL):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Nu när du har lagt till en omdirigerings-URI måste du uppdatera API-URL: en så att hanterings verktyget kan interagera med API-backend-tjänsten.

## <a name="update-the-api-url-for-the-web-application"></a>Uppdatera API-URL: en för webb programmet

Kör följande skript för att uppdatera API-URL-konfigurationen i webb program klient delen:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu när du har konfigurerat webbappen för hanterings verktyget är det dags att verifiera Azure AD-programmet och ge ditt medgivande.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verifiera Azure AD-programmet och ge medgivande

Så här kontrollerar du Azure AD-programkonfigurationen och ger godkännande:

1. Öppna din webbläsare och logga in på [Azure Portal](https://portal.azure.com/) med ditt administratörs konto.
2. Från Sök fältet överst i Azure Portal söker du efter **Appregistreringar** och väljer objektet under **tjänster**.
3. Välj **alla program** och Sök efter det unika app-namn som du angav för PowerShell-skriptet i [skapa en Azure Active Directory app-registrering](#create-an-azure-active-directory-app-registration).
4. I panelen till vänster i webbläsaren väljer du **autentisering** och kontrollerar att omdirigerings-URI: n är samma som webbappens URL för hanterings verktyget, som du ser i följande bild.

   [![Sidan autentisering med angiven omdirigerings-URI ](../media/management-ui-redirect-uri-inline.png)](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. I den vänstra panelen väljer du **API-behörigheter** för att bekräfta att behörigheterna har lagts till. Om du är global administratör väljer du knappen **bevilja administratörs medgivande för `tenantname`** och följer dialog rutorna för att ge din organisation ett administrativt medgivande.

    [![Sidan ](../media/management-ui-permissions-inline.png) API-behörigheter](../media/management-ui-permissions-expanded.png#lightbox)

Nu kan du börja använda hanterings verktyget.

## <a name="use-the-management-tool"></a>Använd hanterings verktyget

Nu när du har konfigurerat hanterings verktyget kan du när som helst starta det när som helst, var som helst. Så här startar du verktyget:

1. Öppna URL-adressen för webbappen i en webbläsare. Om du inte kommer ihåg URL: en kan du logga in på Azure, hitta den app service som du har distribuerat för hanterings verktyget och sedan välja URL: en.
2. Logga in med dina Windows-autentiseringsuppgifter för virtuella skriv bord.

   > [!NOTE]
   > Om du inte beviljade administrativt medgivande när du konfigurerade hanterings verktyget måste varje användare som loggar in ange sitt eget användar medgivande för att kunna använda verktyget.

3. När du uppmanas att välja en klient grupp väljer du **standard grupp för klient organisation** i list rutan.
4. När du väljer **standard klient grupp** visas en meny på vänster sida i fönstret. I den här menyn letar du reda på namnet på din klient grupp och väljer den.

   > [!NOTE]
   > Om du har en anpassad klient grupp anger du namnet manuellt i stället för att välja i list rutan.

## <a name="report-issues"></a>Rapportera problem

Om du stöter på problem med hanterings verktyget eller andra Windows-verktyg för virtuella skriv bord, följer du anvisningarna i [Azure Resource Manager mallar för Fjärrskrivbordstjänster](https://github.com/Azure/RDS-Templates/blob/master/README.md) för att rapportera dem på GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar och ansluter till hanterings verktyget kan du lära dig hur du använder Azure Service Health för att övervaka tjänst problem och hälso rekommendationer. Mer information finns i [själv studie kursen konfigurera service Alerts](set-up-service-alerts-2019.md).
