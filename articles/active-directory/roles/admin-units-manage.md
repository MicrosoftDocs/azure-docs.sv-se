---
title: Lägga till och ta bort administrativa enheter – Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för att begränsa omfånget för rollbehörigheter i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fad9356d3379e76aa259d67711d18f14a4e266f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505283"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Hantera administrativa enheter i Azure Active Directory

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med ett omfång som är begränsat till en eller flera administrativa enheter.

## <a name="get-started"></a>Kom igång

1. Om du vill köra frågor från följande instruktioner via [Graph Explorer](https://aka.ms/ge)gör du följande:

    a. I Azure Portal du till Azure AD. 
    
    b. I programlistan väljer du **Graph-testaren**.
    
    c. I fönstret **Behörigheter** väljer du **Bevilja administratörsmedgivande för Graph-testaren**.

    ![Skärmbild som visar länken "Bevilja administratörsmedgivande för Graph-testaren".](./media/admin-units-manage/select-graph-explorer.png)


1. Använd [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="add-an-administrative-unit"></a>Lägga till en administrativ enhet

Du kan lägga till en administrativ enhet med hjälp av antingen Azure Portal eller PowerShell.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal du till Azure AD. Välj sedan Administrativa enheter i den **vänstra rutan.**

    ![Skärmbild av länken "Administrativa enheter" i Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Välj **knappen** Lägg till i den övre delen av  fönstret och ange sedan namnet på den administrativa enheten i rutan Namn. Du kan också lägga till en beskrivning av den administrativa enheten.

    ![Skärmbild som visar knappen Lägg till och rutan Namn för att ange namnet på den administrativa enheten.](./media/admin-units-manage/add-new-admin-unit.png)

1. Välj den blå **knappen Lägg** till för att slutföra den administrativa enheten.

### <a name="use-powershell"></a>Använd PowerShell

Installera [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) innan du försöker köra följande kommandon:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Du kan ändra de värden som omges av citattecken efter behov.

### <a name="use-microsoft-graph"></a>Använda Microsoft Graph

Förfrågan

```http
POST /administrativeUnits
```

Brödtext

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Ta bort en administrativ enhet

I Azure AD kan du ta bort en administrativ enhet som du inte längre behöver som en omfångsenhet för administrativa roller.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I den Azure Portal du till **Azure AD** och väljer sedan Administrativa **enheter**. 
1. Välj den administrativa enhet som ska tas bort och välj sedan Ta **bort.** 
1. Bekräfta att du vill ta bort den administrativa enheten genom att välja **Ja.** Den administrativa enheten tas bort.

![Skärmbild av knappen Ta bort på den administrativa enheten och bekräftelsefönstret.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Använd PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

Du kan ändra de värden som omges av citattecken efter behov för den specifika miljön.

### <a name="use-the-graph-api"></a>Använda Graph API

Förfrågan

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Brödtext

```http
{}
```

## <a name="next-steps"></a>Nästa steg

* [Hantera användare i en administrativ enhet](admin-units-add-manage-users.md)
* [Hantera grupper i en administrativ enhet](admin-units-add-manage-groups.md)
