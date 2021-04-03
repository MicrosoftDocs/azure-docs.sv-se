---
title: Migrera principer för villkorlig åtkomst med Multi-Factor Authentication-Azure Active Directory
description: Den här artikeln visar hur du migrerar en klassisk princip som kräver Multi-Factor Authentication i Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87027401"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrera en klassisk princip i Azure Portal

Den här artikeln visar hur du migrerar en klassisk princip som kräver **Multi-Factor Authentication** för en molnbaserad app. Även om det inte är ett krav rekommenderar vi att du läser [migrera klassiska principer i Azure Portal](policy-migration.md) innan du börjar migrera dina klassiska principer.

![Klassisk princip information som kräver MFA för Salesforce-app](./media/policy-migration/33.png)

Migreringsprocessen består av följande steg:

1. [Öppna den klassiska principen](#open-a-classic-policy) för att hämta konfigurations inställningarna.
1. Skapa en ny Azure AD-princip för villkorlig åtkomst för att ersätta den klassiska principen. 
1. Inaktivera den klassiska principen.

## <a name="open-a-classic-policy"></a>Öppna en klassisk princip

1. I [Azure Portal](https://portal.azure.com)navigerar du till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj, **klassiska principer**.

   ![Vyn klassiska principer](./media/policy-migration-mfa/12.png)

1. I listan med klassiska principer väljer du den princip som du vill migrera. Dokumentera konfigurations inställningarna så att du kan återskapa med en ny princip för villkorlig åtkomst.

## <a name="create-a-new-conditional-access-policy"></a>Skapa en ny princip för villkorlig åtkomst

1. I [Azure Portal](https://portal.azure.com)navigerar du till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Om du vill skapa en ny princip för villkorlig åtkomst väljer du **ny princip**.
1. På sidan **nytt** i text rutan **namn** anger du ett namn för principen.
1. I avsnittet **tilldelningar** klickar du på **användare och grupper**.
   1. Om du har valt alla användare i den klassiska principen klickar du på **alla användare**. 
   1. Om du har valt grupper i den klassiska principen klickar du på **Välj användare och grupper** och väljer sedan de användare och grupper som krävs.
   1. Om du har exkluderade grupper klickar du på fliken **exkludera** och väljer sedan de användare och grupper som krävs. 
   1. Välj **färdig**
1. I avsnittet **tilldelning** klickar du på **molnappar eller åtgärder**.
1. Utför följande steg på sidan **Cloud Apps eller åtgärder** :
   1. Klicka på **Välj appar**.
   1. Klicka på **Välj**.
   1. På sidan **Välj** väljer du din Cloud App och klickar sedan på **Välj**.
   1. På sidan **molnappar** klickar du på **Slutför**.
1. Om du **behöver Multi-Factor Authentication** markerat:
   1. I avsnittet **åtkomst kontroller** klickar du på **bevilja**.
   1. På sidan **bevilja** klickar du på **bevilja åtkomst** och klickar sedan på **Kräv Multi-Factor Authentication**.
   1. Klicka på **Välj**.
1. Klicka **på på** för att aktivera principen och välj sedan **Spara**.

   ![Skapa princip för villkorlig åtkomst](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Inaktivera den klassiska principen

Om du vill inaktivera den klassiska principen klickar du på **inaktivera** i vyn **information** .

![Inaktivera klassiska principer](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om den klassiska princip migreringen finns [i Migrera klassiska principer i Azure Portal](policy-migration.md).
- [Använd endast rapport läge för villkorlig åtkomst för att fastställa effekten av nya princip beslut.](concept-conditional-access-report-only.md)