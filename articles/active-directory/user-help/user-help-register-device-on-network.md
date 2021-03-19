---
title: Registrera personliga enheter i en organisations nätverk – Azure AD
description: Lär dig hur du registrerar din personliga enhet i din organisations nätverk så att du kan komma åt din organisations skyddade resurser.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 0435b99525c34eb72d7cc5315ccb4359859cd528
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90033055"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrera din personliga enhet i din organisations nätverk
Registrera din personliga enhet (vanligt vis en telefon eller surfplatta) i din organisations nätverk. När enheten har registrerats kommer den att kunna komma åt din organisations begränsade resurser.

>[!Note]
>I den här artikeln används en Windows-enhet i demonstrations syfte, men du kan även registrera enheter som kör iOS, Android eller macOS.

## <a name="what-happens-when-you-register-your-device"></a>Vad händer när du registrerar din enhet
När du registrerar din enhet i din organisations nätverk sker följande åtgärder:

- Windows registrerar din enhet i din organisations nätverk.

- Du kan också, beroende på din organisations val, uppmanas att ställa [in tvåstegsverifiering via antingen tvåfaktorautentisering](multi-factor-authentication-end-user-first-time.md) eller [säkerhets information](./security-info-setup-signin.md).

- Du kan också, beroende på din organisations val, registreras automatiskt i hantering av mobila enheter, t. ex. Microsoft Intune. Mer information om hur du registrerar i Microsoft Intune finns i [Registrera din enhet i Intune](/intune-user-help/enroll-your-device-in-intune-all).

- Du går igenom inloggnings processen med hjälp av användar namn och lösen ord för ditt arbets-eller skol konto.

## <a name="to-register-your-windows-device"></a>Registrera din Windows-enhet

Följ de här stegen för att registrera din personliga enhet i nätverket.

1. Öppna **Inställningar** och välj sedan **konton**.

    ![Konton på skärmen Inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola** och välj sedan **Anslut** från skärmen för **åtkomst till arbetet eller skolan** .

    ![Åtkomst till arbets-eller skol skärm med alternativet Anslut markerat](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. På skärmen **Lägg till ett arbets-eller skol konto** skriver du in din e-postadress för ditt arbets-eller skol konto och väljer sedan **Nästa**. Till exempel alain@contoso.com.

4. Logga in på ditt arbets-eller skol konto och välj sedan **Logga** in.

5. Slutför resten av registrerings processen, inklusive att godkänna din begäran om identitets verifiering (om du använder tvåstegsverifiering) och konfigurera Windows Hello (om det behövs).

## <a name="to-verify-that-youre-registered"></a>För att kontrol lera att du är registrerad
Du kan se till att du har registrerat dig genom att titta på dina inställningar.

1. Öppna **Inställningar** och välj sedan **konton**.

    ![Konton på skärmen Inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola** och se till att du ser ditt arbets-eller skol konto.

    ![Få åtkomst till arbets-eller skol skärmen med det anslutna contoso-kontot](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Nästa steg
När du har registrerat din personliga enhet i din organisations nätverk bör du ha åtkomst till de flesta av dina resurser.

- Om din organisation vill att du ska ansluta till din arbets enhet, se [ansluta din arbets enhet till din organisations nätverk](user-help-join-device-on-network.md).