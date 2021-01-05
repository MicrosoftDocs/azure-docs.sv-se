---
title: Visa rapporter & loggar i hantering av rättigheter – Azure AD
description: Lär dig hur du visar rapporten användar tilldelningar och gransknings loggar i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eb44c1efd683b6febe9a355ef72c80cc6f2e40d
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746634"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Visa rapporter och loggar i hantering av Azure AD-berättigande

Hanterings rapporterna för Azure AD-rapportering och Azure AD audit-loggen innehåller ytterligare information om vilka resurser som användare har åtkomst till. Som administratör kan du Visa åtkomst paketen och resurs tilldelningarna för en användare och Visa begär ande loggar för gransknings syfte eller för att fastställa status för en användares begäran. Den här artikeln beskriver hur du använder hanterings rapporter för rättigheter och Azure AD audit-loggar.

Titta på följande video och lär dig hur du visar vilka resurser som användare har åtkomst till i hantering av rättigheter:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Visa åtkomst paket för en användare

Med den här rapporten kan du Visa en lista över alla åtkomst paket som en användare kan begära och de åtkomst paket som för närvarande är tilldelade till användaren.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. På den vänstra menyn klickar du på **rapporter**.

1. Klicka på **åtkomst paket för en användare**.

1. Klicka på **Välj användare** för att öppna fönstret Välj användare.

1. Hitta användaren i listan och klicka sedan på **Välj**.

    Fliken **kan begära** visar en lista över de åtkomst paket som användaren kan begära. Den här listan bestäms av de [förfrågnings principer](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) som definierats för åtkomst paketen. 

    ![Åtkomst till paket för en användare](./media/entitlement-management-reports/access-packages-report.png)

1. Om det finns fler än en resurs roll eller principer för ett åtkomst paket klickar du på posten resurs roller eller principer för att se urvals information.

1. Klicka på fliken **tilldela** om du vill se en lista över de åtkomst paket som för närvarande har tilldelats användaren. När ett åtkomst paket tilldelas till en användare innebär det att användaren har åtkomst till alla resurs roller i åtkomst paketet.

## <a name="view-resource-assignments-for-a-user"></a>Visa resurstilldelningar för en användare

Med den här rapporten kan du Visa en lista över resurser som är kopplade till en användare i hantering av rättigheter. Observera att den här rapporten gäller resurser som hanteras med rättighets hantering. Användaren kan ha åtkomst till andra resurser i din katalog utanför hantering av rättigheter.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. På den vänstra menyn klickar du på **rapporter**.

1. Klicka på **resurstilldelningar för en användare**.

1. Klicka på **Välj användare** för att öppna fönstret Välj användare.

1. Hitta användaren i listan och klicka sedan på **Välj**.

    En lista över de resurser som för närvarande är tilldelade till användaren visas. I listan visas även det åtkomst paket och den princip som de fick resurs rollen från, tillsammans med start-och slutdatum för åtkomst.
    
    Om en användare har åtkomst till samma resurs i två eller flera paket kan du klicka på en pil för att se varje paket och princip.

    ![Resurstilldelningar för en användare](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Fastställa status för en användares begäran

Om du vill ha mer information om hur en användare begärde och fick åtkomst till ett Access-paket kan du använda Azure AD-gransknings loggen. I synnerhet kan du använda logg posterna i `EntitlementManagement` `UserManagement` kategorierna och för att få ytterligare information om bearbetnings stegen för varje begäran.  

1. Klicka på **Azure Active Directory** och klicka sedan på **gransknings loggar**.

1. Längst upp ändrar du **kategorin** till antingen `EntitlementManagement` eller `UserManagement` , beroende på vilken gransknings post du letar efter.  

1. Klicka på **Använd**.

1. Klicka på **Hämta** om du vill hämta loggarna.

När Azure AD tar emot en ny begäran skriver den en gransknings post i vilken **kategorin** är `EntitlementManagement` och **aktiviteten** är vanligt vis `User requests access package assignment` .  Om det finns en direkt tilldelning som skapats i Azure Portal, är **aktivitets** fältet för gransknings posten `Administrator directly assigns user to access package` och den användare som utför tilldelningen identifieras av **ActorUserPrincipalName**.

Azure AD skriver ytterligare gransknings poster medan begäran pågår, inklusive:

| Kategori | Aktivitet | Status för begäran |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Begäran kräver inte godkännande |
| `UserManagement` | `Create request approval` | Begäran kräver godkännande |
| `UserManagement` | `Add approver to request approval` | Begäran kräver godkännande |
| `EntitlementManagement` | `Approve access package assignment request` | Begäran godkänd |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Begäran godkänd eller kräver inte godkännande |

När en användare tilldelas åtkomst skriver Azure AD en gransknings post för `EntitlementManagement` kategorin med **aktivitet** `Fulfill access package assignment` .  Användaren som har tagit emot åtkomsten identifieras av **ActorUserPrincipalName** -fältet.

Om åtkomst inte har tilldelats skriver Azure AD en gransknings post för `EntitlementManagement` kategorin med **aktivitet** antingen `Deny access package assignment request` , om begäran nekades av en god kännare, eller om tids gränsen nåddes för `Access package assignment request timed out (no approver action taken)` begäran innan en god kännare kan godkänna.

När användarens åtkomst paket tilldelning går ut, annulleras av användaren eller tas bort av en administratör, skriver Azure AD en gransknings post för `EntitlementManagement` kategorin med **aktivitet** i `Remove access package assignment` .

## <a name="next-steps"></a>Nästa steg

- [Arkivera rapporter och loggar](entitlement-management-logs-and-reporting.md)
- [Felsöka hantering av Azure AD-berättigande](entitlement-management-troubleshoot.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
