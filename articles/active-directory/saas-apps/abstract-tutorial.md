---
title: 'Självstudie: Azure Active Directory integrering med abstrakt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och abstrakt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 3f19733f01cf601c1145d5e2bb5deb58db828dc4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538339"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Självstudie: integrera abstrakta med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar abstrakt med Azure Active Directory (Azure AD). När du integrerar abstrakt med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till abstrakt.
* Gör det möjligt för användarna att logga in automatiskt för att skapa abstrakta med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Abstrakt enkel inloggnings prenumeration (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Abstrakt stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-abstract-from-the-gallery"></a>Lägga till abstrakta från galleriet

Om du vill konfigurera integreringen av abstrakta i Azure AD måste du lägga till abstrakt från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri skriver du** **abstrakt** i sökrutan.
1. Välj **abstrakt** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med abstrakt användning av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i abstrakta.

Om du vill konfigurera och testa Azure AD SSO med abstrakt, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera abstrakt SSO](#configure-abstract-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa abstrakta test användare](#create-abstract-test-user)** – för att få en motsvarighet till Britta Simon i abstrakt som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **abstrakt** program integration i [Azure Portal](https://portal.azure.com/), letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP** initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://app.abstract.com/signin`

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurera abstrakt SSO

Se till att hämta `App Federation Metadata Url` och `Azure AD Identifier` från Azure Portal, eftersom du behöver dem för att konfigurera SSO på abstrakt.

Du hittar informationen på sidan **Konfigurera enkel inloggning med SAML** :

* Finns `App Federation Metadata Url` i avsnittet certifikat för **SAML-signering** .
* Finns `Azure AD Identifier` i avsnittet **Konfigurera abstrakt** .


Du är nu redo att konfigurera SSO på abstrakt:

>[!Note]
>Du måste autentisera med ett organisations administratörs konto för att få åtkomst till SSO-inställningarna på abstrakt.

1. Öppna den [abstrakta webbappen](https://app.abstract.com/).
2. Gå till sidan **behörigheter** i det vänstra sid fältet.
3. I avsnittet **Konfigurera SSO** anger du din **metadata-URL** och **entitets-ID**.
4. Ange eventuella manuella undantag som du kan ha. E-postmeddelanden som anges i avsnittet manuella undantag hoppar över SSO och kan logga in med e-post och lösen ord. 
5. Klicka på **Spara ändringar**.

>[!Note] 
>Du måste använda primära e-postadresser i listan över manuella undantag. SSO-aktivering kommer att Miss inaktive ras om e-postmeddelandet är en användares sekundära e-post Om detta inträffar visas ett fel meddelande med det primära e-postmeddelandet för det misslyckade kontot. Lägg till det primära e-postmeddelandet i listan över manuella undantag när du har verifierat att du känner till användaren.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till abstrakt.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **abstrakt**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-abstract-test-user"></a>Skapa abstrakt test användare

Testa SSO vid abstrakthet:

1. Öppna den [abstrakta webbappen](https://app.abstract.com/).
2. Gå till sidan **behörigheter** i det vänstra sid fältet.
3. Klicka på **testa med mitt konto**. Om testet Miss lyckas kontaktar du [vårt support team](https://www.abstract.com/help/contact/).

>[!Note]
>Du måste autentisera med ett organisations administratörs konto för att få åtkomst till SSO-inställningarna på abstrakt.
Det här organisations administratörs kontot måste tilldelas till abstrakta på Azure Portal.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den abstrakta panelen i åtkomst panelen bör du loggas in automatiskt på den sammanfattning som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

