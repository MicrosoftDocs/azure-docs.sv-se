---
title: Visa, lägga till och ta bort tilldelningar för ett åtkomstpaket i Azure AD-berättigandehantering – Azure Active Directory
description: Lär dig hur du visar, lägger till och tar bort tilldelningar för ett åtkomstpaket i Azure Active Directory berättigandehantering.
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
ms.openlocfilehash: 6e00fe3761824462252ce4984beb754385f3eca9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532146"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Visa, lägga till och ta bort tilldelningar för ett åtkomstpaket i Azure AD-berättigandehantering

I Azure AD-berättigandehantering kan du se vem som har tilldelats åtkomstpaket, deras princip och status. Om ett åtkomstpaket har en lämplig princip kan du också direkt tilldela användare till ett åtkomstpaket. Den här artikeln beskriver hur du visar, lägger till och tar bort tilldelningar för åtkomstpaket.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure AD-rättighetshantering och tilldela användare åtkomstpaket måste du ha någon av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licens

## <a name="view-who-has-an-assignment"></a>Visa vem som har en tilldelning

**Förutsättningsroll:** Global administratör, användaradministratör, katalogägare, åtkomstpakethanterare eller tilldelningshanterare för åtkomstpaket

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Åtkomstpaket** och öppnar sedan åtkomstpaketet.

1. Klicka **på Tilldelningar** om du vill se en lista över aktiva tilldelningar.

    ![Lista över tilldelningar till ett åtkomstpaket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klicka på en specifik tilldelning om du vill se mer information.

1. Om du vill se en lista över tilldelningar som inte har alla resursroller korrekt etablerade klickar du på filterstatusen och väljer **Leverera.**

    Du kan se ytterligare information om leveransfel genom att hitta användarens motsvarande begäran på **sidan** Begäranden.

1. Om du vill se förfallna tilldelningar klickar du på filterstatusen och väljer **Har upphört att gälla.**

1. Om du vill ladda ned en CSV-fil för den filtrerade listan klickar du på **Ladda ned**.

### <a name="viewing-assignments-programmatically"></a>Visa tilldelningar programmatiskt

Du kan också hämta tilldelningar i ett åtkomstpaket med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har delegerad behörighet kan anropa API:et för att lista `EntitlementManagement.ReadWrite.All` [accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true).

## <a name="directly-assign-a-user"></a>Tilldela en användare direkt

I vissa fall kanske du vill tilldela specifika användare direkt till ett åtkomstpaket så att användarna inte behöver gå igenom processen med att begära åtkomstpaketet. För att kunna tilldela användare direkt måste åtkomstpaketet ha en princip som tillåter direkttilldelning av administratörer.

**Förutsättningsroll:** Global administratör, användaradministratör, katalogägare, åtkomstpakethanterare eller tilldelningshanterare för åtkomstpaket

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Åtkomstpaket** och öppnar sedan åtkomstpaketet.

1. I den vänstra menyn klickar du **på Tilldelningar.**

1. Klicka **på Ny tilldelning** för att öppna Lägg till användare för att komma åt paketet.

    ![Tilldelningar – Lägg till användare för åtkomstpaket](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klicka **på Lägg till** användare för att välja de användare som du vill tilldela det här åtkomstpaketet till.

1. I listan **Välj princip** väljer du en princip som användarnas framtida begäranden och livscykel ska styras och spåras av. Om du vill att de valda användarna ska ha olika principinställningar kan du klicka på **Skapa ny princip för** att lägga till en ny princip.

1. Ange det datum och den tid som du vill att de valda användarnas tilldelning ska starta och avsluta. Om inget slutdatum anges används principens livscykelinställningar.

1. Du kan också ange en motivering för din direkttilldelning för registerhållning.

1. Klicka **på Lägg** till för att direkt tilldela de valda användarna till åtkomstpaketet.

    Efter en liten stund klickar du **på** Uppdatera för att se användarna i listan Tilldelningar.

### <a name="directly-assigning-users-programmatically"></a>Direkt tilldelning av användare programmatiskt

Du kan också direkt tilldela en användare till ett åtkomstpaket med hjälp av Microsoft Graph.  En användare i en lämplig roll med ett program som har den delegerade behörigheten kan anropa API:et för att skapa `EntitlementManagement.ReadWrite.All` [en accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true).

## <a name="remove-an-assignment"></a>Ta bort en tilldelning

**Förutsättningsroll:** Global administratör, användaradministratör, katalogägare, åtkomstpakethanterare eller tilldelningshanterare för åtkomstpaket

1. I dialogrutan Azure Portal du på **Azure Active Directory** sedan på **Identity Governance**.

1. I den vänstra menyn klickar du **på Åtkomstpaket** och öppnar sedan åtkomstpaketet.

1. I den vänstra menyn klickar du **på Tilldelningar.**
 
1. Klicka på kryssrutan bredvid den användare vars tilldelning du vill ta bort från åtkomstpaketet. 

1. Klicka på **knappen** Ta bort längst upp i det vänstra fönstret. 
 
    ![Tilldelningar – Ta bort användare från åtkomstpaket](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Ett meddelande visas som informerar dig om att tilldelningen har tagits bort. 

## <a name="next-steps"></a>Nästa steg

- [Ändra begäran och inställningar för ett åtkomstpaket](entitlement-management-access-package-request-policy.md)
- [Visa rapporter och loggar](entitlement-management-reports.md)