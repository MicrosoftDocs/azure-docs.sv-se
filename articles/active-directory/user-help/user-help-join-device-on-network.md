---
title: Anslut din arbets enhet till din organisations nätverk – AD
description: Lär dig hur du ansluter din arbets enhet till din organisations nätverk.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: efb3ee24add847baf8264eccdf71278be5ee9496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91536978"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Anslut din arbets enhet till din organisations nätverk
Anslut din arbetsägda Windows 10-enhet till din organisations nätverk så att du kan komma åt potentiellt begränsade resurser.

## <a name="what-happens-when-you-join-your-device"></a>Vad händer när du ansluter till din enhet
När du ansluter till din Windows 10-enhet till din organisations nätverk sker följande åtgärder:

- Windows registrerar din enhet i din organisations nätverk, så att du kan komma åt dina resurser med ditt personliga konto. När enheten har registrerats ansluter Windows enheten till nätverket så att du kan använda din organisations användar namn och lösen ord för att logga in och få åtkomst till begränsade resurser.

- Om du vill, baserat på din organisations val, kan du uppmanas att konfigurera tvåstegsverifiering genom antingen [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) eller [säkerhets information](./security-info-setup-signin.md).

- Du kan också, beroende på din organisations val, registreras automatiskt i hantering av mobila enheter, t. ex. Microsoft Intune. Mer information om hur du registrerar i Microsoft Intune finns i [Registrera din enhet i Intune](/intune-user-help/enroll-your-device-in-intune-all).

- Du går igenom inloggnings processen med hjälp av automatisk inloggning med ditt organisations konto.

## <a name="to-join-a-brand-new-windows-10-device"></a>Så här ansluter du till en helt ny Windows 10-enhet
Om enheten är helt ny och inte har kon figurer ATS ännu kan du gå igenom OOBE-processen (Windows out of Box Experience) för att ansluta enheten till nätverket.

1. Starta den nya enheten och påbörja OOBE-processen.

2. Skriv din e-postadress till arbetet eller skolan på skärmen **Logga in med Microsoft** .

    ![Inloggnings skärmen med e-postadress](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Skriv ditt lösen ord på skärmen **Ange ditt lösen ord** .

    ![Ange lösen ords skärmen](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Godkänn enheten på din mobila enhet så att den kan komma åt ditt konto. 

    ![Skärm bild som visar meddelande skärmen "Ange ditt lösen ord".](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Slutför OOBE-processen, inklusive att ställa in dina sekretess inställningar och konfigurera Windows Hello (om det behövs).

    Enheten är nu ansluten till din organisations nätverk.

## <a name="to-make-sure-youre-joined-new-device"></a>För att se till att du är ansluten (ny enhet)
Du kan se till att du är ansluten genom att titta på dina inställningar.

1. Öppna **Inställningar** och välj sedan **konton**.

    ![Konton på skärmen Inställningar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola** och se till att du ser text som säger något som är **ansluten till *\<your_organization>* Azure AD**.

    ![Skärm bild som visar fönstret "åtkomst till arbets plats eller skola" med kontot "ansluten till (din organisation) Azure AD" valt.](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Så här ansluter du en redan konfigurerad Windows 10-enhet
Om du har haft enheten en stund och den redan har kon figurer ATS, kan du följa dessa steg för att ansluta enheten till nätverket.

1. Öppna **Inställningar** och välj sedan **konton**.

2. Välj **åtkomst till arbete eller skola** och välj sedan **Anslut**.

    ![Åtkomst till arbets-eller skol-och anslutnings länkar](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. På skärmen **Konfigurera ett arbets-eller skol konto** väljer **du Anslut den här enheten till Azure Active Directory**.

    ![Konfigurera ett arbets-eller skol konto-fönster](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Skriv din e-postadress (till exempel) på sidan **Låt oss bli inloggad** alain@contoso.com och välj sedan **Nästa**.

    ![Låt oss bli inloggad på skärmen](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. På skärmen **Ange lösen ord** skriver du ditt lösen ord och väljer sedan **Logga** in.

    ![Ange lösenord](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Godkänn enheten på din mobila enhet så att den kan komma åt ditt konto. 

    ![Skärmen för mobila meddelanden](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. På skärmen  **kontrol lera att det här är din organisation** , granskar du informationen för att se till att den är rätt och väljer sedan **Anslut**.

    ![Kontrol lera att det här är din organisations verifierings skärm](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Klicka på **klar** på skärmen **allt är** klart.

    ![Allt är klart](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>För att se till att du är ansluten
Du kan se till att du är ansluten genom att titta på dina inställningar.

1. Öppna **Inställningar** och välj sedan **konton**.

    ![Konton på skärmen Inställningar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola** och se till att du ser text som säger något som är **ansluten till *\<your_organization>* Azure AD**.

    ![Få åtkomst till arbets-eller skol skärmen med det anslutna contoso-kontot](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Nästa steg
När du har anslutit enheten till din organisations nätverk, bör du kunna komma åt alla dina resurser med hjälp av konto informationen för ditt arbets-eller skol konto.

- Om din organisation vill registrera din personliga enhet, till exempel din telefon, kan du läsa [Registrera din personliga enhet i din organisations nätverk](user-help-register-device-on-network.md).

- Om din organisation hanteras med hjälp av Microsoft Intune och du har frågor om registrering, inloggning eller andra Intune-relaterade problem kan du läsa mer i [användar hjälpen för Intune](/intune-user-help/use-managed-devices-to-get-work-done).