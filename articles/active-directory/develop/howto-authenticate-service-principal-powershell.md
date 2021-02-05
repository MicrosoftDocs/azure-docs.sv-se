---
title: Skapa en Azure App-identitet (PowerShell) | Azure
titleSuffix: Microsoft identity platform
description: Beskriver hur du använder Azure PowerShell för att skapa en Azure Active Directory-app och tjänstens huvudnamn och bevilja åtkomst till resurser via rollbaserad åtkomstkontroll. Visar hur du autentiserar appar med ett certifikat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev , devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: b07679c6dd5e03131fde1439213783893971a088
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581830"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Gör så här: använda Azure PowerShell för att skapa ett huvud namn för tjänsten med ett certifikat

När du har en app eller ett skript som behöver åtkomst till resurser, kan du ställa in en identitet för appen och autentisera den med sina egna autentiseringsuppgifter. Den här identiteten kallas tjänstens huvudnamn. Med den här metoden kan du:

* Tilldela behörigheter till app-identiteten som skiljer sig från din egen behörighet. Vanligen är dessa behörigheter begränsade till exakt vad appen behöver göra.
* Använda ett certifikat för autentisering när du kör oövervakade skript.

> [!IMPORTANT]
> I stället för att skapa ett huvud namn för tjänsten bör du överväga att använda hanterade identiteter för Azure-resurser för din program identitet. Om din kod körs på en tjänst som har stöd för hanterade identiteter och åtkomst till resurser som stöder Azure Active Directory (Azure AD)-autentisering, är Managed identiteter ett bättre alternativ. Mer information om hanterade identiteter för Azure-resurser, inklusive vilka tjänster som för närvarande stöder det, finns i [Vad är Managed identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

Den här artikeln visar hur du skapar ett huvudnamn för tjänsten som autentiserar med ett certifikat. Om du vill konfigurera ett huvudnamn för tjänsten med lösenord, se [Skapa tjänstens huvudnamn för Azure med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Du måste ha den [senaste versionen](/powershell/azure/install-az-ps) av PowerShell för den här artikeln.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Behörigheter som krävs

För att slutföra den här artikeln måste du ha tillräcklig behörighet i både din Azure AD-och Azure-prenumeration. Mer specifikt måste du kunna skapa en app i Azure AD och tilldela tjänstens huvud namn till en roll.

Det enklaste sättet att kontrollera om kontot har tillräcklig behörighet är via portalen. Se [Kontrollera behörighet som krävs](howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="assign-the-application-to-a-role"></a>Tilldela programmet till en roll
Du måste tilldela programmet till en roll för att få åtkomst till resurser i din prenumeration. Bestäm vilken roll som har rätt behörigheter för programmet. Mer information om tillgängliga roller finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Du kan ange omfång på nivån för prenumerationen, resurs gruppen eller resursen. Behörigheter ärvs till lägre omfattnings nivåer. Om du till exempel lägger till ett program till rollen *läsare* för en resurs grupp innebär det att den kan läsa resurs gruppen och alla resurser som den innehåller. Om du vill tillåta att programmet kör åtgärder som starta om, starta och stoppa instanser väljer du *deltagar* rollen.

## <a name="create-service-principal-with-self-signed-certificate"></a>Skapa huvudnamn för tjänsten med självsignerade certifikat

Följande exempel visar ett enkelt scenario. Den använder [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) för att skapa ett huvud namn för tjänsten med ett självsignerat certifikat och använder [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) för att tilldela rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) rollen som tjänstens huvud namn. Rolltilldelningen är begränsad till den valda Azure-prenumerationen. Om du vill välja en annan prenumeration använder du [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> New-SelfSignedCertificate-cmdlet och PKI-modulen stöds för närvarande inte i PowerShell Core. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

Exemplet försätts i vilo läge i 20 sekunder så att det nya tjänstens huvud namn kan spridas över Azure AD. Om skriptet inte väntar tillräckligt länge visas ett felmeddelande: "Principal {ID} does not exist in the directory {DIR-ID}." (Huvudkontot {ID} finns inte i katalogen {DIR-ID}). För att lösa det här felet väntar du en stund och kör sedan kommandot **New-AzRoleAssignment** igen.

Du kan ange omfånget för rolltilldelningen till en viss resursgrupp med hjälp av parametern **ResourceGroupName**. Du kan också ange omfånget för en viss resurs med parametrarna **ResourceType** och **ResourceName**. 

Om du **inte har Windows 10 eller Windows Server 2016** laddar du ned den [självsignerade certifikat generatorn](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) från Microsoft Script Center. Extrahera innehållet och importera den cmdlet som du behöver.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Ersätt följande två rader i skriptet för att generera certifikatet.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Ange certifikat via det automatiska PowerShell-skriptet

När du loggar in som ett huvud namn för tjänsten anger du klient-ID för katalogen för din AD-App. En klient organisation är en instans av Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

$Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Skapa tjänstens huvudnamn med certifikat från certifikatutfärdaren

I följande exempel används ett certifikat som utfärdats från en certifikatutfärdare för att skapa tjänstens huvudnamn. Tilldelningen begränsas till den angivna Azure-prenumerationen. Tjänsten lägger till tjänstens huvud namn i rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) . Om ett fel inträffar under rolltilldelningen försöker det göra om tilldelningen.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }

 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Ange certifikat via det automatiska PowerShell-skriptet
När du loggar in som ett huvud namn för tjänsten anger du klient-ID för katalogen för din AD-App. En klient organisation är en instans av Azure AD.

```powershell
Param (

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

App-ID och klientorganisations-ID är inte känsliga data, så du kan bädda in dem direkt i skriptet. Om du behöver hämta klientorganisations-ID, använd:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Om du behöver hämta app-ID, använd:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Ändra autentiseringsuppgifter

Om du vill ändra autentiseringsuppgifterna för en AD-App, antingen på grund av en säkerhets kompromiss eller förfallo datum, använder du cmdletarna [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) och [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) .

För att ta bort alla autentiseringsuppgifter för en app, använd:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Skapa ett självsignerat certifikat för att lägga till ett certifikatvärde som visas i den här artikeln. Använd sedan:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Felsökning

Du kan få följande fel när du skapar ett huvudnamn för tjänsten:

* **"Authentication_Unauthorized"** (Ej auktoriserad autentisering) eller **"No subscription found in the context."** (Ingen prenumeration hittades i sammanhanget). – Du ser det här felet om ditt konto inte har de [behörigheter som krävs](#required-permissions) för Azure AD för att registrera en app. Normalt visas det här felet när endast administratörs användare i Azure Active Directory kan registrera appar, och ditt konto är inte en administratör. Be administratören att antingen tilldela dig till en administratörs roll eller att låta användare registrera appar.

* Ditt konto **har inte behörighet att utföra åtgärden "Microsoft. Authorization/roleAssignments/Write" över omfånget "/Subscriptions/{GUID}".** – du ser det här felet om ditt konto inte har tillräcklig behörighet för att tilldela en roll till en identitet. Be din prenumerationsadministratör att ge dig rollen som administratör för användaråtkomst.

## <a name="next-steps"></a>Nästa steg

* Om du vill konfigurera ett huvudnamn för tjänsten med lösenord, se [Skapa tjänstens huvudnamn för Azure med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* En mer detaljerad förklaring av program och tjänstens huvudnamn finns i [Programobjekt och tjänstobjekt](app-objects-and-service-principals.md).
* Mer information om Azure AD-autentisering finns i [autentiserings scenarier för Azure AD](./authentication-vs-authorization.md).
* Information om hur du arbetar med app-registreringar med hjälp av **Microsoft Graph** finns i API-referens för [program](/graph/api/resources/application) .
