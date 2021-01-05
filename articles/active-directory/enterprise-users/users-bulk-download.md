---
title: Hämta en lista med användare i Azure Active Directory Portal | Microsoft Docs
description: Hämta användar poster i bulk i Azure administrations centret i Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861601"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Hämta en lista med användare i Azure Active Directory Portal

Azure Active Directory (Azure AD) stöder åtgärder för Mass import av användare (skapa).

## <a name="required-permissions"></a>Behörigheter som krävs

För att ladda ned listan med användare från Azure AD-administrationscentret måste du vara inloggad med en användare som tilldelats en eller flera administratörsroller i Azure AD (rollen Användaradministratör eller högre krävs). Gästdeltagare och programutvecklare betraktas inte som administratörsroller.

## <a name="to-download-a-list-of-users"></a>Hämta en lista med användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett användar administratörs konto i organisationen.
2. Gå till Azure Active Directory > Användare. Välj sedan de användare som du vill lägga till i nedladdningen genom att kryssa i rutan i den vänstra kolumnen bredvid varje användare. Obs! För närvarande går det inte att markera alla användare samtidigt för export. De måste markeras en i taget.
3. I Azure AD väljer **du användare**  >  **Ladda ned användare**.
4. På sidan **Ladda ned användare** väljer du **Start** för att ta emot en CSV-fil som visar egenskaper för användar profiler. Om det finns fel kan du ladda ned och visa resultatfilen på sidan med massåtgärdsresultat. I filen kan du se orsaken till varje fel.

   ![Välj var du vill att listan över användare som du vill ladda ned ska hämtas](./media/users-bulk-download/bulk-download.png)

   Nedladdningsfilen innehåller den filtrerade listan över användare.

   Följande användarattribut finns med:

   - userPrincipalName
   - displayName
   - surname
   - e-post
   - förnamn
   - objectId
   - userType
   - jobTitle
   - avdelning
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - land
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobil
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för väntande Mass begär Anden på sidan med **Mass åtgärds resultat** .

[![Kontrol lera status på sidan med Mass åtgärds resultat.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Begränsningar för Mass nedladdnings tjänst

Varje Mass aktivitet för att skapa en lista med användare kan köras i upp till en timme. På så sätt kan du skapa och ladda ned en lista med minst 500 000 användare.

## <a name="next-steps"></a>Nästa steg

- [Masstillägg av användare](users-bulk-add.md)
- [Massborttag användare](users-bulk-delete.md)
- [Massåterställ användare](users-bulk-restore.md)
