---
title: 'Självstudie: Azure Active Directory integrering med ANAQUA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ANAQUA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.openlocfilehash: cf6f146265562e287df2cb3c4574742335d62efe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92318649"
---
# <a name="tutorial-integrate-anaqua-with-azure-active-directory"></a>Självstudie: integrera ANAQUA med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar ANAQUA med Azure Active Directory (Azure AD). När du integrerar ANAQUA med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ANAQUA.
* Gör det möjligt för användarna att logga in automatiskt till ANAQUA med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ANAQUA för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. ANAQUA stöder **SP-och IDP** -initierad SSO och stöder **just-in-Time** User-etablering.

## <a name="adding-anaqua-from-the-gallery"></a>Lägga till ANAQUA från galleriet

Om du vill konfigurera integreringen av ANAQUA i Azure AD måste du lägga till ANAQUA från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **ANAQUA** i sökrutan.
1. Välj **ANAQUA** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med ANAQUA med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ANAQUA.

Om du vill konfigurera och testa Azure AD SSO med ANAQUA, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera ANAQUA](#configure-anaqua)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa ANAQUA test User](#create-anaqua-test-user)** för att ha en motsvarighet till B. Simon i ANAQUA som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **ANAQUA** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.anaqua.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.anaqua.com/anaqua/Public/login.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.anaqua.com/anaqua/Public/login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [ANAQUA client support team](https://go.anaqua.com/contact-us) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enskilda Sign-On med SAML** , i avsnittet SAML- **signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned metadatafilen och spara den på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera ANAQUA** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-anaqua"></a>Konfigurera ANAQUA

Om du vill konfigurera enkel inloggning på **ANAQUA** sida måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [support teamet för ANAQUA](https://go.anaqua.com/contact-us). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ANAQUA.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **ANAQUA**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-anaqua-test-user"></a>Skapa ANAQUA test användare

I det här avsnittet skapas en användare som kallas Britta Simon i ANAQUA. ANAQUA stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i ANAQUA skapas en ny efter autentiseringen.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen ANAQUA på åtkomst panelen, bör du loggas in automatiskt på den ANAQUA som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)