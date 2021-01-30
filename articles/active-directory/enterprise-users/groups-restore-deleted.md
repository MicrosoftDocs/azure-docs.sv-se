---
title: Återställa en borttagen Microsoft 365 grupp – Azure AD | Microsoft Docs
description: Så återställer du en borttagen grupp, visar återställningsbara grupper och tar bort en grupp permanent i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1eacba47e68310dd45976bcd9ddff052fa193436
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090145"
---
# <a name="restore-a-deleted-microsoft-365-group-in-azure-active-directory"></a>Återställa en borttagen Microsoft 365 grupp i Azure Active Directory

När du tar bort en Microsoft 365 grupp i Azure Active Directory (Azure AD) behålls den borttagna gruppen men visas inte i 30 dagar från borttagnings datumet. På så sätt kan gruppen och dess innehåll återställas om det behövs. Den här funktionen är begränsad enbart till Microsoft 365 grupper i Azure AD. Den är inte tillgänglig för säkerhetsgrupper och distributionsgrupper. Observera att återställnings perioden på 30 dagar inte kan anpassas.

> [!NOTE]
> Använd inte `Remove-MsolGroup` eftersom det gör att gruppen tas bort permanent. Använd alltid `Remove-AzureADMSGroup` för att ta bort en Microsoft 365 grupp.

Behörigheterna som krävs för att återställa en grupp kan vara någon av följande:

Roll | Behörigheter
--------- | ---------
Global administratör, grupp administratör, partner – nivå 2-stöd och Intune-administratör | Kan återställa borttagna Microsoft 365s grupper
Stöd för användar administratör och partner 1 | Kan återställa borttagna Microsoft 365s grupper förutom de grupper som har tilldelats rollen som global administratör
User | Kan återställa borttagna Microsoft 365s grupper som de äger

## <a name="view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore"></a>Visa och hantera de borttagna Microsoft 365 grupper som kan återställas

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett användar administratörs konto.

2. Välj **Grupper** och sedan **Borttagna grupper** för att visa de borttagna grupper som är tillgängliga för återställning.

    ![Visa grupper som är tillgängliga för återställning](./media/groups-restore-deleted/deleted-groups3.png)

3. På bladet **Borttagna grupper** kan du:

   - Återställa den borttagna gruppen och dess innehåll genom att välja **Återställ grupp**.
   - Ta bort den borttagna gruppen permanent genom att välja **Ta bort permanent**. Du måste vara administratör för att ta bort en grupp permanent.

## <a name="view-the-deleted-microsoft-365-groups-that-are-available-to-restore-using-powershell"></a>Visa de borttagna Microsoft 365 grupper som är tillgängliga för återställning med hjälp av PowerShell

Du kan använda följande cmdlets för att visa de borttagna grupperna och kontrollera att den eller de grupper som du är intresserad av inte har raderats permanent än. Dessa cmdlets är en del av [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/). Mer information om den här modulen finns i artikeln [Azure Active Directory PowerShell Version 2](/powershell/azure/active-directory/install-adv2).

1.  Kör följande cmdlet för att visa alla borttagna Microsoft 365 grupper i din Azure AD-organisation som fortfarande är tillgängliga för återställning.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Om du har objectID för en specifik grupp (och du kan hämta det från cmdleten i steg 1), kan du även köra följande cmdlet för att kontrollera att den specifika borttagna gruppen inte har raderats permanent än.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-microsoft-365-group-using-powershell"></a>Så här återställer du den borttagna Microsoft 365s gruppen med hjälp av PowerShell

När du har kontrollerat att gruppen fortfarande är tillgänglig för återställning kan du återställa den borttagna gruppen med hjälp av något av följande steg. Om gruppen innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt, kan det ta upp till 24 timmar att helt återställa en grupp och dess innehåll.

1. Kör följande cmdlet för att återställa gruppen och dess innehåll.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. Du kan också köra följande cmdlet om du vill ta bort den borttagna gruppen permanent.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Hur vet du att åtgärden har fungerat?

Kontrol lera att du har återställt en Microsoft 365 grupp genom att köra `Get-AzureADGroup –ObjectId <objectId>` cmdleten för att visa information om gruppen. När återställningsbegäran har slutförts:

- Gruppen visas i det vänstra navigeringsfältet i Exchange
- Planen för gruppen visas i Planner
- Eventuella SharePoint-webbplatser och allt deras innehåll är tillgängliga
- Gruppen kan nås från alla Exchange-slutpunkter och andra Microsoft365-arbetsbelastningar som stöder Microsoft 365 grupper

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure Active Directory-grupper.

* [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
