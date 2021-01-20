---
title: Villkorlig åtkomst – blockera åtkomst efter plats – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att blockera åtkomst till resurser via IP-adress
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e257ab39257b23c52aaadbe32f0325e8d71a8409
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597974"
---
# <a name="conditional-access-block-access-by-location"></a>Villkorlig åtkomst: blockera åtkomst efter plats

Med plats villkoret i villkorlig åtkomst kan du styra åtkomsten till dina molnappar baserat på användarens nätverks plats. Plats villkoret används ofta för att blockera åtkomst från länder/regioner där organisationen vet att trafiken inte kommer från.

## <a name="define-locations"></a>Definiera platser

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **villkorlig åtkomst till**  >  **namngivna platser**.
1. Välj **ny plats**.
1. Namnge din plats.
1. Välj **IP-intervall** om du känner till de enskilda externa IPv4-adressintervall som utgör den platsen eller **länder/regioner**.
   1. Ange **IP-intervall** eller Välj **land/region** för den plats som du anger.
      * Om du väljer länder/regioner kan du välja att inkludera okända områden.
1. Välj **Spara**

Mer information om plats villkoret i villkorlig åtkomst finns i artikeln, [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** och väljer du **alla molnappar**.
1. Under **villkor**  >  **plats**.
   1. **Konfigurera** till **Ja**
   1. Under **Inkludera** väljer du **valda platser**
   1. Välj den blockerade plats som du har skapat för din organisation.
   1. Klicka på **Välj**.
1. Under **åtkomst kontroller** > väljer du **blockera åtkomst** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa en princip för villkorlig åtkomst.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
