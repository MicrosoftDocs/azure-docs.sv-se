---
title: Lägga till och ta bort administrativa enheter – Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för att begränsa omfånget för roll behörigheter i Azure Active Directory.
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
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012129"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Hantera administrativa enheter i Azure Active Directory

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med en omfattning som är begränsad till en eller flera administrativa enheter.

## <a name="get-started"></a>Kom igång

1. Om du vill köra frågor från följande instruktioner via [Graph Explorer](https://aka.ms/ge)gör du följande:

    a. I Azure Portal går du till Azure AD. 
    
    b. I listan program väljer du **Graph Explorer**.
    
    c. I rutan **behörigheter** väljer du **bevilja administrativt godkännande för Graph Explorer**.

    ![Skärm bild som visar länken bevilja administrativt medgivande för Graph Explorer.](./media/admin-units-manage/select-graph-explorer.png)


1. Använd [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="add-an-administrative-unit"></a>Lägg till en administrativ enhet

Du kan lägga till en administrativ enhet genom att använda antingen Azure Portal eller PowerShell.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal går du till Azure AD. Välj sedan **administrativa enheter** i den vänstra rutan.

    ![Skärm bild av länken "administrations enheter" i Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Välj knappen **Lägg till** i den övre delen av fönstret och skriv sedan namnet på den administrativa enheten i rutan **namn** . Du kan också lägga till en beskrivning av den administrativa enheten.

    ![Skärm bild som visar knappen Lägg till och rutan namn för att ange namnet på den administrativa enheten.](./media/admin-units-manage/add-new-admin-unit.png)

1. Klicka på knappen blå **Lägg** till för att slutföra den administrativa enheten.

### <a name="use-powershell"></a>Använd PowerShell

Installera [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) innan du försöker köra följande kommandon:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Du kan ändra de värden som omges av citat tecken, om det behövs.

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

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

I Azure AD kan du ta bort en administrativ enhet som du inte längre behöver som enhets omfång för administrativa roller.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal går du till **Azure AD** och väljer sedan **administrativa enheter**. 
1. Välj den administrativa enhet som ska tas bort och välj sedan **ta bort**. 
1. Bekräfta att du vill ta bort den administrativa enheten genom att välja **Ja**. Den administrativa enheten tas bort.

![Skärm bild av den administrativa enhets knappen Ta bort och bekräftelse fönstret.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Använd PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

Du kan ändra de värden som omges av citat tecken, vilket krävs för den aktuella miljön.

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
