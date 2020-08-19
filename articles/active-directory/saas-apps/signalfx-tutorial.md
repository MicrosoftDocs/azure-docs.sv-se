---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SignalFx | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 3eae423763d2d4a05b33e4e1d4b6e9e087a47ba3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521577"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SignalFx

I den här självstudien får du lära dig hur du integrerar SignalFx med Azure Active Directory (Azure AD). När du integrerar SignalFx med Azure AD kan du:

* Kontroll från Azure AD som har åtkomst till SignalFx;
* Gör det möjligt för användarna att logga in automatiskt till SignalFx med sina Azure AD-konton. särskilt
* Hantera dina konton på en plats (Azure Portal).

Mer information om SaaS program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du:

* En Azure AD-prenumeration
    * Om du inte har någon prenumeration kan du skaffa ett [kostnads fritt konto här](https://azure.microsoft.com/free/).
* SignalFx för enkel inloggning (SSO) med aktiverade prenumerationer

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kommer du att konfigurera och testa Azure AD SSO i en test miljö.

* SignalFx stöder **IDP** INITIERAd SSO
* SignalFx stöder **just-in-Time** User-etablering
* När du har konfigurerat SignalFx kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Steg 1: Lägg till SignalFx-programmet i Azure

Använd de här anvisningarna för att lägga till SignalFx-programmet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. I navigerings fönstret på den vänstra sidan väljer du **Azure Active Directory**.
1. Välj **företags program**och välj sedan **alla program**.
1. Välj **Nytt program**.
1. I avsnittet **Lägg till från galleriet** i sökrutan anger du och väljer **SignalFx**.
     * Du kan behöva vänta några minuter för att programmet ska läggas till i din klient organisation.
1. Lämna Azure Portal öppen och öppna sedan en ny webb-flik.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Steg 2: starta SignalFx SSO-konfiguration

Använd de här anvisningarna för att starta konfigurations processen för SignalFx SSO.

1. Öppna och logga in på SignalFx-ANVÄNDARGRÄNSSNITTET på den nyligen öppnade fliken. 
1. Klicka på **integration**i den översta menyn. 
1. I Sök fältet, ange och välj **Azure Active Directory**.
1. Klicka på **Skapa ny integrering**.
1. I **namn**anger du ett lätt igenkännbart namn som användarna kan förstå.
1. Markera **Visa på inloggnings sidan**.
    * Den här funktionen visar en anpassad knapp på inloggnings sidan som användarna kan klicka på. 
    * Den information som du angav i **namn** visas på knappen. Därför anger du ett **namn** som användarna kommer att känna igen. 
    * Det här alternativet fungerar bara om du använder en anpassad under domän för SignalFx-programmet, till exempel **yourcompanyname.signalfx.com**. Kontakta SignalFx-supporten om du vill hämta en anpassad under domän. 
1. Kopiera **integrations-ID: t**. Du kommer att behöva den här informationen i ett senare steg. 
1. Lämna SignalFx-ANVÄNDARGRÄNSSNITTET öppet. 

## <a name="step-3-configure-azure-ad-sso"></a>Steg 3: Konfigurera Azure AD SSO

Använd de här instruktionerna för att aktivera Azure AD SSO i Azure Portal.

1. Gå tillbaka till [Azure Portal](https://portal.azure.com/)och leta upp avsnittet **Hantera** på sidan **SignalFx** application integration och välj sedan **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen (redigera) för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** fyller du i följande fält: 

    a. I **identifierare**anger du följande URL `https://api.<realm>.signalfx.com/v1/saml/metadata` och ersätter `<realm>` med din SignalFx-sfär. 

    b. I **svars-URL**anger du följande URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` och ersätter `<realm>` med din SignalFx-sfär, samt `<integration ID>` med det **integrations-ID** som du kopierade tidigare från SignalFx-användargränssnittet.

1. SignalFx-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. 
    
1. Granska och verifiera att följande anspråk mappar till de källattribut som är ifyllda i Active Directory. 

    | Name |  Källattribut|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User. email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Den här processen kräver att din Active Directory har kon figurer ATS med minst en verifierad anpassad domän, samt har åtkomst till e-postkontona i den här domänen. Kontakta SignalFx-supporten om du är osäker eller behöver hjälp med den här konfigurationen.  

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)** och väljer sedan **Hämta**. Hämta certifikatet och spara det på din dator. Kopiera sedan URL-värdet för **app Federation-Metadata** ; du behöver den här informationen i ett senare steg i SignalFx-ANVÄNDARGRÄNSSNITTET. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera värdet för **Azure AD-identifieraren** i avsnittet **Konfigurera SignalFx** . Du behöver den här informationen i ett senare steg i SignalFx-ANVÄNDARGRÄNSSNITTET. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Steg 4: skapa en Azure AD-test användare

Använd de här anvisningarna för att skapa en test användare i Azure Portal som kallas **B. Simon**.

1. I Azure Portal i navigerings fönstret till vänster väljer du **Azure Active Directory**och väljer sedan **användare**. Välj sedan **alla användare**.
1. Välj **ny användare**längst upp på sidan.
1. I **användar** egenskaperna:
   1. I **användar namn**anger `username@companydomain.extension` du, till exempel `b.simon@contoso.com` .
   1. I **namn**anger du `B.Simon` .
   1. Markera **Visa lösen ord**och kopiera sedan det visade värdet i **lösen ord**. Du behöver den här informationen i senare steg för att kunna testa den här integrationen. 
   1. Klicka på **Skapa**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Steg 5: tilldela Azure AD-test användare

Använd de här instruktionerna för att göra det möjligt för test användaren att använda enkel inloggning med Azure för SignalFx.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SignalFx**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer sedan **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper**i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** , i listan **användare** , väljer du **B. Simon**och klickar sedan på **Välj**längst ned på sidan.
1. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på **Välj** längst ned på sidan.
1. Klicka på **tilldela**i dialog rutan **Lägg till tilldelning** .

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Steg 6: Slutför SignalFx SSO-konfigurationen 

1. Öppna föregående flik och gå tillbaka till SignalFx-ANVÄNDARGRÄNSSNITTET för att visa den aktuella Azure Active Directory integrations sidan. 
1. Bredvid **certifikat (base64)** klickar du på **överför fil**och letar sedan upp den **base64-kodade certifikat** filen som du tidigare laddade ned från Azure Portal.
1. Bredvid **Azure AD-identifieraren**klistrar du in värdet för **Azure AD-identifieraren** som du kopierade tidigare från Azure Portal. 
1. Bredvid **URL för federationsmetadata**klistrar du in URL-värdet för **app Federation-Metadata** som du kopierade tidigare från Azure Portal. 
1. Klicka på **Spara**.

## <a name="step-7-test-sso"></a>Steg 7: testa SSO

Läs följande information om hur du testar SSO, samt förväntningar för att logga in på SignalFx för första gången. 

### <a name="test-logins"></a>Testa inloggningar

* För att testa inloggningen bör du använda ett privat/Incognito-fönster, eller så kan du logga ut från Azure Portal. Om inte, kommer cookies för den användare som har konfigurerat programmet att störa och förhindra en lyckad inloggning med test användaren.

* När en ny test användare loggar in för första gången tvingar Azure dig att ändra lösen ordet. När detta inträffar slutförs inte inloggnings processen för SSO. test användaren dirigeras till Azure Portal. För att felsöka ska test användaren ändra sitt lösen ord och navigera till SignalFx-inloggnings sidan eller till åtkomst panelen och försöka igen.
    * När du klickar på panelen SignalFx på åtkomst panelen, ska du loggas in automatiskt på SignalFx. 
        * Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

* SignalFx-programmet kan nås från åtkomst panelen eller via en anpassad inloggnings sida som tilldelats organisationen. Test användaren bör testa integreringen med början från någon av dessa platser.
    * Test användaren kan använda de autentiseringsuppgifter som skapades tidigare i den här processen för **b.simon@contoso.com** .

### <a name="first-time-logins"></a>Inloggningar i första gången

* När en användare loggar in i SignalFx från SAML SSO för första gången, får användaren ett SignalFx-e-postmeddelande med en länk. Användaren måste klicka på länken för att autentiseringen ska kunna användas. Den här e-postverifieringen sker bara för användare i första gången. 

* SignalFx stöder **just-in-Time** -användare, vilket innebär att om en användare inte finns i SignalFx, kommer användarens konto att skapas vid det första inloggnings försöket.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Prova SignalFx med Azure AD](https://aad.portal.azure.com/)