---
title: 'Självstudie: Azure Active Directory integrering med TigerConnect Secure Messenger | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182163"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Självstudie: Azure Active Directory integrering med TigerConnect Secure Messenger

I den här självstudien får du lära dig att integrera TigerConnect Secure Messenger med Azure Active Directory (Azure AD).

Att integrera TigerConnect Secure Messenger med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till TigerConnect Secure Messenger.
* Du kan göra det möjligt för användarna att logga in automatiskt till TigerConnect Secure Messenger (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Mer information om program vara som en tjänst (SaaS) för program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TigerConnect Secure Messenger behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
* En TigerConnect säker Messenger-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö och integrerar TigerConnect Secure Messenger med Azure AD.

* TigerConnect Secure Messenger stöder **SP** -INITIERAd SSO
* När du har konfigurerat TigerConnect Secure Messenger kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>Lägga till TigerConnect Secure Messenger från galleriet

Om du vill konfigurera integrationen av TigerConnect Secure Messenger i Azure AD måste du lägga till TigerConnect Secure Messenger från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **TigerConnect Secure Messenger** i sökrutan.
1. Välj **TigerConnect Secure Messenger** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TigerConnect Secure Messenger baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i TigerConnect Secure Messenger.

Om du vill konfigurera och testa enkel inloggning med Azure AD med TigerConnect Secure Messenger måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera Britta Simon att använda enkel inloggning i Azure AD.
1. **[Konfigurera TigerConnect Secure Messenger SSO](#configure-tigerconnect-secure-messenger-sso)** för att konfigurera inställningar för enkel inloggning på program sidan.
    * **[Skapa en TigerConnect säker Messenger-test användare](#create-a-tigerconnect-secure-messenger-test-user)** så att det finns en användare med namnet Britta Simon i TigerConnect Secure Messenger som är länkad till Azure AD-användaren med namnet Britta Simon.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med TigerConnect Secure Messenger i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **säker Messenger-TigerConnect** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. I rutan **inloggnings-URL** anger du en URL:

       `https://home.tigertext.com`

    1. I rutan **identifierare (enhets-ID)** anger du en URL med hjälp av följande mönster:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Värdet för **identifieraren (enhets-ID)** är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [support teamet för TigerConnect Secure Messenger](mailto:prosupport@tigertext.com)för att få värdet. Du kan också se de mönster som visas i fönstret **grundläggande konfiguration av SAML** i Azure Portal.

1. I avsnittet **Konfigurera enskilda Sign-On med SAML** i avsnittet **SAML-signeringscertifikat** väljer du **Ladda ned** för att ladda ned **XML-metadata för federationsmetadata** från de angivna alternativen och sparar dem på din dator.

    ![Hämtnings alternativ för XML-metadata för federationsmetadata](common/metadataxml.png)

1. I avsnittet **Konfigurera TigerConnect Secure Messenger** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TigerConnect säkra Messenger.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **TigerConnect Secure Messenger**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Konfigurera TigerConnect Secure Messenger SSO

Om du vill konfigurera enkel inloggning på den TigerConnect säkra Messenger-sidan måste du skicka den hämtade XML-koden för federationsmetadata och lämpliga kopierade URL: er från Azure Portal till [support teamet för TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). TigerConnect Secure Messenger-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Skapa en TigerConnect säker Messenger-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i TigerConnect Secure Messenger. Arbeta med [TigerConnect Secure Messenger support team](mailto:prosupport@tigertext.com) för att lägga till Britta Simon som en användare i TigerConnect Secure Messenger. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer **TigerConnect Secure Messenger** i portalen Mina appar, bör du loggas in automatiskt till den TigerConnect-säkra Messenger-prenumeration som du ställer in enkel inloggning för. Mer information om My Apps-portalen finns i [komma åt och använda appar på portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova TigerConnect Secure Messenger med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)