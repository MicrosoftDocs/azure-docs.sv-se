---
title: Stäng ett arbets-eller skol konto i en ohanterad Azure AD-organisation
description: Hur du stänger ditt arbets-eller skol konto i en ohanterad Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/03/2020
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 252c993f5679d80cda97a6fade08f5f92489840d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575592"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>Stäng ditt arbets-eller skol konto i en ohanterad Azure AD-organisation

Om du är en användare i en ohanterad Azure Active Directory (Azure AD)-organisation och du inte längre behöver använda appar från den organisationen eller upprätthålla någon koppling till den, kan du när som helst stänga ditt konto. En ohanterad organisation har inte en global administratör. Användare i en ohanterad organisation kan stänga sina konton på egen hand, utan att behöva kontakta en administratör.

Användare i en ohanterad organisation skapas ofta under självbetjänings registrering. Ett exempel kan vara en informations anställd i en organisation som registrerar sig för en kostnads fri tjänst. Mer information om självbetjänings registrering finns i [Vad är självbetjänings registrering för Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan avsluta ditt konto bör du bekräfta följande objekt:

* Se till att du är en användare av en ohanterad Azure AD-organisation. Du kan inte stänga ditt konto om du tillhör en hanterad organisation. Om du tillhör en hanterad organisation och vill avsluta ditt konto måste du kontakta administratören. Information om hur du avgör om du tillhör en ohanterad organisation finns i [ta bort användaren från en ohanterad klient](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Spara alla data som du vill behålla. Information om hur du skickar en export förfrågan finns i [komma åt och exportera systemgenererade loggar för ohanterade klienter](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Om du stänger ditt konto går det inte att ångra. När du stänger ditt konto tas all personlig information bort. Du kommer inte längre att ha åtkomst till ditt konto och dina data som är kopplade till ditt konto.

## <a name="close-your-account"></a>Stänga konto

Följ dessa steg om du vill stänga ett ohanterat arbets-eller skol konto:

1. Logga in för att [stänga ditt konto](https://go.microsoft.com/fwlink/?linkid=873123)med det konto som du vill stänga.

1. På **mina data förfrågningar** väljer du **Stäng konto**.

    ![Mina data förfrågningar – Stäng konto](./media/users-close-account/close-account.png)

1. Granska bekräftelse meddelandet och välj sedan **Ja**.

    ![Mina data förfrågningar – Bekräfta stängning](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är självbetjänings registrering för Azure Active Directory?](directory-self-service-signup.md)
- [Ta bort användaren från en ohanterad klientorganisation](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Åtkomst till och export av systemgenererade loggar för ohanterade klientorganisationer](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)