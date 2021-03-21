---
title: Dölj ett företags program från användarens upplevelse i Azure AD
description: Hur du döljer ett företags program från användarens upplevelse i Azure Active Directory åtkomst paneler eller Microsoft 365 startare.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258888"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Dölj företags program från slutanvändare i Azure Active Directory

Instruktioner för hur du döljer program från slutanvändarens panel för Mina appar eller Microsoft 365 Launcher. När ett program är dolt har användarna fortfarande behörighet till programmet. 

## <a name="prerequisites"></a>Förutsättningar

Program administratörs behörighet krävs för att dölja ett program från panelen för Mina appar och Microsoft 365 Launcher.

Det krävs globala administratörs behörigheter för att dölja alla Microsoft 365-program.


## <a name="hide-an-application-from-the-end-user"></a>Dölj ett program från slutanvändaren
Använd följande steg för att dölja ett program från panelen för appar och Microsoft 365 program start.

1.  Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **företags program**. Bladet **företags program – alla program** öppnas.
4.  Under **program typ** väljer du **företags program**, om det inte redan är markerat.
5.  Sök efter det program som du vill dölja och klicka på programmet.  Programmets översikt öppnas.
6.  Klicka på **Egenskaper**. 
7.  För frågan **synlig för användare?** klickar du på **Nej**.
8.  Klicka på **Spara**.

> [!NOTE]
> Dessa anvisningar gäller endast för företags program.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Använd Azure AD PowerShell för att dölja ett program

Om du vill dölja ett program från panelen Mina appar kan du manuellt lägga till HideApp-taggen till tjänstens huvud namn för programmet. Kör följande [AzureAD PowerShell](/powershell/module/azuread/#service_principals) -kommandon för att ange programmets som **synlig för användarna?** -egenskapen till **Nej**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Dölj Microsoft 365-program från panelen för Mina appar

Använd följande steg för att dölja alla Microsoft 365-program på panelen för Mina appar. Programmen syns fortfarande i Office 365-portalen.

1.  Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **Användare**.
4.  Välj **Användarinställningar**.
5.  Under **företags program** klickar du på hantera hur slutanvändare **startar och visar sina program.**
6.  För **användare kan bara se office 365-appar i office 365-portalen**, klicka på **Ja**.
7.  Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg
* [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företags app](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp tilldelning från en företags app](./assign-user-or-group-access-portal.md)
* [Ändra namn eller logo typ för en företags app](./add-application-portal-configure.md)
