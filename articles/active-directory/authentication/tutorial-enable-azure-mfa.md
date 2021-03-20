---
title: Aktivera Azure AD-multifaktorautentisering
description: I den här självstudien får du lära dig hur du aktiverar Azure AD-Multi-Factor Authentication för en grupp användare och testar den sekundära faktorn vid en inloggnings händelse.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e8bf2ccbf7a53563013c7ba653a6f6e8905337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97881318"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Självstudie: säkra användar inloggnings händelser med Azure AD Multi-Factor Authentication

Multi-Factor Authentication (MFA) är en process där en användare uppmanas att logga in för ytterligare identifierings former. Den här frågan kan vara att ange en kod på sin mobil telefon eller för att tillhandahålla en finger avsökning. När du behöver en andra form av autentisering, ökar säkerheten eftersom den här ytterligare faktorn inte är något som är lätt för en angripare att hämta eller duplicera.

Azure AD Multi-Factor Authentication-och principer för villkorlig åtkomst ger flexibiliteten att aktivera MFA för användare under särskilda inloggnings händelser. Här är en [video om hur du konfigurerar och framtvingar Multi-Factor Authentication i din klient](https://www.youtube.com/watch?v=qNndxl7gqVM) (**rekommenderas**)

> [!IMPORTANT]
> I den här självstudien visas en administratör för att aktivera Azure AD Multi-Factor Authentication.
>
> Om ditt IT-team inte har aktiverat möjligheten att använda Azure AD Multi-Factor Authentication eller om du har problem under inloggningen kan du kontakta supportavdelningen för ytterligare hjälp.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en princip för villkorlig åtkomst för att aktivera Azure AD Multi-Factor Authentication för en grupp användare
> * Konfigurera princip villkor som begär MFA
> * Testa MFA-processen som en användare

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En fungerande Azure AD-klient med minst en Azure AD Premium P1-eller utvärderings licens aktive rad.
    * Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med *Global administratörs* behörighet.
* En icke-administratörs användare med ett lösen ord som du känner till, till exempel *testuser*. Du testar slutanvändarens Azure AD Multi-Factor Authentication-upplevelse med det här kontot i den här självstudien.
    * Om du behöver skapa en användare, se [snabb start: Lägg till nya användare i Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* En grupp som inte är administratörs användare som är medlem i, till exempel *MFA-test-Group*. Du aktiverar Azure AD-Multi-Factor Authentication för den här gruppen i den här självstudien.
    * Om du behöver skapa en grupp, se så här [skapar du en grupp och lägger till medlemmar i Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Det rekommenderade sättet att aktivera och använda Azure AD Multi-Factor Authentication är med principer för villkorlig åtkomst. Med villkorlig åtkomst kan du skapa och definiera principer som reagerar på inloggnings händelser och begära ytterligare åtgärder innan en användare beviljas åtkomst till ett program eller en tjänst.

![Översikts diagram över hur villkorlig åtkomst fungerar för att skydda inloggnings processen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Principer för villkorlig åtkomst kan vara detaljerad och speciell, med målet att göra det möjligt för användarna att vara produktiva oavsett var de befinner sig, men även skydda din organisation. I den här självstudien ska vi skapa en grundläggande princip för villkorlig åtkomst för att fråga efter MFA när en användare loggar in till Azure Portal. I en senare självstudie i den här serien konfigurerar du Azure AD Multi-Factor Authentication att använda en riskfylld princip för villkorlig åtkomst.

Börja med att skapa en princip för villkorlig åtkomst och tilldela din test grupp till användare på följande sätt:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto med *globala administratörs* behörigheter.
1. Sök efter och välj **Azure Active Directory** och välj sedan **säkerhet** på menyn på vänster sida.
1. Välj **villkorlig åtkomst** och välj sedan **+ ny princip**.
1. Ange ett namn för principen, till exempel *MFA pilot*.
1. Under **tilldelningar** väljer **du användare och grupper**, sedan alternativ knappen **Välj användare och grupper** .
1. Markera kryss rutan för **användare och grupper** och **Välj** sedan för att bläddra bland tillgängliga Azure AD-användare och-grupper.
1. Bläddra efter och välj din Azure AD-grupp, till exempel *MFA-test-Group*, och välj sedan **Välj**.

    [![Välj din Azure AD-grupp som ska användas med principen ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) för villkorlig åtkomst](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Om du vill tillämpa principen för villkorlig åtkomst för gruppen väljer du **slutförd**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Konfigurera villkoren för Multi-Factor Authentication

När du har skapat en princip för villkorlig åtkomst och en test grupp med tilldelade användare definierar du nu de molnappar eller åtgärder som utlöser principen. Dessa molnappar eller åtgärder är de scenarier du bestämmer kräver ytterligare bearbetning, till exempel för att fråga om MFA. Du kan till exempel bestämma att åtkomsten till ett finansiellt program eller användning av hanterings verktyg kräver som en ytterligare verifierings varning.

I den här självstudien konfigurerar du principen för villkorlig åtkomst så att den kräver MFA när en användare loggar in till Azure Portal.

1. Välj **Molnappar eller åtgärder**. Du kan välja att tillämpa principen för villkorlig åtkomst för *alla molnappar* eller *välja appar*. För att ge flexibilitet kan du även utesluta vissa appar från principen.

    I den här självstudien väljer du alternativ knappen **Välj appar** på sidan *Inkludera* .

1. Välj **Välj** och bläddra i listan över tillgängliga inloggnings händelser som kan användas.

    I den här självstudien väljer du **Microsoft Azure hantering** så att principen gäller inloggnings händelser till Azure Portal.

1. Om du vill använda de valda apparna väljer du **Välj** och sedan **Slutför**.

    ![Välj den Microsoft Azure hanterings app som ska ingå i principen för villkorlig åtkomst](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Med åtkomst kontroller kan du definiera kraven för en användare som ska beviljas åtkomst, till exempel att behöva en godkänd klient app eller använda en enhet som är hybrid Azure AD-ansluten. I den här självstudien konfigurerar du åtkomst kontroller för att kräva MFA under en inloggnings händelse till Azure Portal.

1. Under *åtkomst kontroller* väljer du **bevilja** och kontrollerar sedan att alternativ knappen **bevilja åtkomst** är markerad.
1. Markera kryss rutan för **Kräv Multi-Factor Authentication** och välj sedan **Välj**.

Principer för villkorlig åtkomst kan anges till *endast rapporter* om du vill se hur konfigurationen skulle påverka användare eller *av* om du inte vill använda principen just nu. Som en test grupp för användare har varit mål för den här själv studie kursen aktiverar du principen och testar sedan Azure AD-Multi-Factor Authentication.

1. Ange *aktiverings principen* växla till **på**.
1. Om du vill tillämpa principen för villkorlig åtkomst väljer du **skapa**.

## <a name="test-azure-ad-multi-factor-authentication"></a>Testa Azure AD-Multi-Factor Authentication

Nu ska vi se din princip för villkorlig åtkomst och Azure AD Multi-Factor Authentication. Logga först in på en resurs som inte kräver MFA på följande sätt:

1. Öppna ett nytt webbläsarfönster i InPrivate-eller Incognito-läge och bläddra till [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Logga in med en test användare som inte är administratör, till exempel *testuser*. Det finns ingen fråga om du vill slutföra MFA.
1. Stäng webbläsarfönstret.

Logga nu in på Azure Portal. Eftersom Azure Portal konfigurerades i principen för villkorlig åtkomst för att kräva ytterligare verifiering, får du en Azure AD Multi-Factor Authentication-prompt.

1. Öppna ett nytt webbläsarfönster i InPrivate-eller Incognito-läge och bläddra till [https://portal.azure.com](https://portal.azure.com) .
1. Logga in med en test användare som inte är administratör, till exempel *testuser*. Du måste registrera dig för och använda Azure AD Multi-Factor Authentication. Följ anvisningarna för att slutföra processen och kontrol lera att du har loggat in på Azure Portal.

    ![Följ webbläsarens prompter och klicka sedan på din registrerade Multi-Factor Authentication-prompt för att logga in](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Stäng webbläsarfönstret.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda den villkorliga åtkomst principen för att aktivera Azure AD Multi-Factor Authentication konfigurerat som en del av den här självstudien tar du bort principen med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory** och välj sedan **säkerhet** på menyn på vänster sida.
1. Välj **villkorlig åtkomst** och välj sedan den princip som du skapade, till exempel *MFA pilot*
1. Välj **ta bort** och bekräfta sedan att du vill ta bort principen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat Azure AD Multi-Factor Authentication att använda principer för villkorlig åtkomst för en viss grupp av användare. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en princip för villkorlig åtkomst för att aktivera Azure AD Multi-Factor Authentication för en grupp med Azure AD-användare
> * Konfigurera princip villkor som begär MFA
> * Testa MFA-processen som en användare

> [!div class="nextstepaction"]
> [Aktivera tillbakaskrivning av lösen ord för självbetjäning för återställning av lösen ord (SSPR)](./tutorial-enable-sspr-writeback.md)
