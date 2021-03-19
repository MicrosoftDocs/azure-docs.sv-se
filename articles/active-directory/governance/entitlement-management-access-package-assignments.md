---
title: Visa, lägga till och ta bort tilldelningar för ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig att visa, lägga till och ta bort tilldelningar för ett Access-paket i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bc7ca9e108a35fd27f7b3155f186221a8caae3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980058"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Visa, lägga till och ta bort tilldelningar för ett Access-paket i hantering av Azure AD-rättigheter

I hantering av Azure AD-rättigheter kan du se vem som har tilldelats åtkomst till paket, deras principer och status. Om ett Access-paket har en lämplig princip kan du också direkt tilldela användare till ett Access-paket. Den här artikeln beskriver hur du visar, lägger till och tar bort tilldelningar för åtkomst paket.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda hantering av Azure AD-rättigheter och tilldela användare åtkomst till paket måste du ha någon av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licens

## <a name="view-who-has-an-assignment"></a>Visa vem som har en tilldelning

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare, Access Package Manager eller Access Package Assignment Manager

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **tilldelningar** för att visa en lista över aktiva tilldelningar.

    ![Lista över tilldelningar till ett Access-paket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klicka på en uppgift om du vill visa mer information.

1. Om du vill se en lista över tilldelningar som inte har alla resurs roller korrekt etablerade klickar du på filter status och väljer **leverera**.

    Du kan se ytterligare information om leverans fel genom att leta upp användarens motsvarande begäran på sidan **begär Anden** .

1. Om du vill se förfallna tilldelningar klickar du på filter status och väljer **upphör att gälla**.

1. Klicka på **Hämta** om du vill ladda ned en CSV-fil för den filtrerade listan.

### <a name="viewing-assignments-programmatically"></a>Visa tilldelningar program mässigt

Du kan också hämta tilldelningar i ett Access-paket med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att [Visa accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Tilldela en användare direkt

I vissa fall kanske du vill tilldela vissa användare direkt till ett Access-paket så att användarna inte behöver gå igenom processen för att begära åtkomst paketet. För att kunna tilldela användare direkt måste åtkomst paketet ha en princip som tillåter administratörs direkta tilldelningar.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare, Access Package Manager eller Access Package Assignment Manager

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **tilldelningar** på den vänstra menyn.

1. Klicka på **ny tilldelning** för att öppna Lägg till användare för att komma åt paketet.

    ![Tilldelningar – Lägg till användare till Access-paket](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klicka på **Lägg till användare** för att välja de användare som du vill tilldela det här åtkomst paketet till.

1. I listan **Välj princip** väljer du en princip som användarnas framtida begär Anden och livs cykel ska styras av och spåras av. Om du vill att de valda användarna ska ha olika princip inställningar kan du klicka på **Skapa ny princip** för att lägga till en ny princip.

1. Ange datum och tid då du vill att de valda användarnas tilldelning ska starta och sluta. Om inget slutdatum anges används principens livs cykel inställningar.

1. Du kan också ange en motivering för den direkta tilldelningen för post hållning.

1. Klicka på **Lägg till** för att tilldela de valda användarna direkt till åtkomst paketet.

    Efter en liten stund klickar du på **Uppdatera** för att se användarna i listan tilldelningar.

### <a name="directly-assigning-users-programmatically"></a>Direkt tilldela användare program mässigt

Du kan också tilldela en användare direkt till ett Access-paket med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att [skapa en accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Ta bort en tilldelning

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare, Access Package Manager eller Access Package Assignment Manager

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **tilldelningar** på den vänstra menyn.
 
1. Klicka på kryss rutan bredvid den användare vars tilldelning du vill ta bort från åtkomst paketet. 

1. Klicka på knappen **ta bort** högst upp i det vänstra fönstret. 
 
    ![Tilldelningar – ta bort användare från Access-paket](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Ett meddelande visas som talar om att tilldelningen har tagits bort. 

## <a name="next-steps"></a>Nästa steg

- [Ändringsbegäran och inställningar för ett Access-paket](entitlement-management-access-package-request-policy.md)
- [Visa rapporter och loggar](entitlement-management-reports.md)