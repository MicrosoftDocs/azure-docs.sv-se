---
title: Dölja ett företagsprogram från användarens vy i Azure AD
description: Hur du döljer ett företagsprogram från användarens upplevelse i Azure Active Directory åtkomstpaneler eller Microsoft 365 uppskjutningsprogram.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90f3e3aeb1d68c6c6e6eeea29c04ff7880dccd3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374207"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Dölj företagsprogram från slutanvändare i Azure Active Directory

Anvisningar för hur du döljer program från slutanvändarna MyApps-panelen eller Microsoft 365-startprogrammet. När ett program är dolt har användarna fortfarande behörighet till programmet. 

## <a name="prerequisites"></a>Förutsättningar

Programadministratör behörigheter krävs för att dölja ett program från panelen MyApps och Microsoft 365 startprogrammet.

Global administratör behörigheter krävs för att dölja alla Microsoft 365 program.


## <a name="hide-an-application-from-the-end-user"></a>Dölja ett program från slutanvändaren
Använd följande steg för att dölja ett program från panelen MyApps och Microsoft 365 programstartaren.

1.  Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **Företagsprogram**. Bladet **Företagsprogram – Alla** program öppnas.
4.  Under **Programtyp** väljer **du Företagsprogram** om det inte redan är markerat.
5.  Sök efter det program som du vill dölja och klicka på programmet.  Programmets översikt öppnas.
6.  Klicka på **Egenskaper**. 
7.  För frågan **Synlig för användare?** klickar du på **Nej.**
8.  Klicka på **Spara**.

> [!NOTE]
> De här anvisningarna gäller endast företagsprogram.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Använda Azure AD PowerShell för att dölja ett program

Om du vill dölja ett program från panelen MyApps kan du manuellt lägga till taggen HideApp i tjänstens huvudnamn för programmet. Kör följande [AzureAD PowerShell-kommandon](/powershell/module/azuread/#service_principals) för att ange programmets **visible to Users?** -egenskap till **Nej.** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Dölj Microsoft 365 program från panelen MyApps

Använd följande steg för att dölja Microsoft 365 program från panelen MyApps. Programmen är fortfarande synliga i Office 365-portalen.

1.  Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **Användare**.
4.  Välj **Användarinställningar**.
5.  Under **Företagsprogram** klickar du **på Hantera hur slutanvändarna startar och visar sina program.**
6.  För **Användare kan bara se Office 365-appar i Office 365-portalen klickar** du på **Ja.**
7.  Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg
* [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användar- eller grupptilldelning från en företagsapp](./assign-user-or-group-access-portal.md)
* [Ändra namn eller logotyp för en företagsapp](./add-application-portal-configure.md)
