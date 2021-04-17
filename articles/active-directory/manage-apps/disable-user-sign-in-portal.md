---
title: Inaktivera användar inloggningar för en företagsapp i Azure AD
description: Så här inaktiverar du ett företagsprogram så att inga användare kan logga in på det i Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/12/2019
ms.author: iangithinji
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9af580ef8b3691a5f188ac15069dda00a5f5802
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374258"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Inaktivera användar inloggningar för en företagsapp i Azure Active Directory

Det är enkelt att inaktivera ett företagsprogram så att inga användare kan logga in på det i Azure Active Directory (Azure AD). Du behöver rätt behörigheter för att hantera företagsappen. Och du måste vara global administratör för katalogen.

## <a name="how-do-i-disable-user-sign-ins"></a>Hur gör jag för att inaktivera användar inloggningar?

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **Alla tjänster,** **Azure Active Directory** i textrutan och välj sedan **Retur.**
1. I fönstret **Azure Active Directory** directoryname _ (det vill säga Azure AD-fönstret för den katalog som du  -   ***hanterar) väljer du _* Företagsprogram**.
1. I fönstret **Företagsprogram – Alla** program visas en lista över de appar som du kan hantera. Välj en app.
1. I fönstret ***appname** _ (det vill säga fönstret med namnet på den valda appen i rubriken) väljer du _*Egenskaper**.
1. I fönstret ***appname** _ - _ *Properties** väljer du **Nej** för **Aktiverad för användare att logga in?**.
1. Välj **kommandot** Spara.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Använda Azure AD PowerShell för att inaktivera en olistad app

Om du känner till AppId för en app som inte visas i listan Med företagsappar (till exempel eftersom du har tagit bort appen eller om tjänstens huvudnamn ännu inte har skapats på grund av att appen har auktoriserats av Microsoft) kan du manuellt skapa tjänstens huvudnamn för appen och sedan inaktivera det med hjälp av [AzureAD PowerShell-cmdleten](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Nästa steg

* [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användar- eller grupptilldelning från en företagsapp](./assign-user-or-group-access-portal.md)
* [Ändra namn eller logotyp för en företagsapp](./add-application-portal-configure.md)
