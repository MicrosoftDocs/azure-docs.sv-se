---
title: 'Självstudie: Azure Active Directory integrering med Aha! | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Aha!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.openlocfilehash: b11955c040225f66bc3eda9fe762cb1cbfc2d308
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542861"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Självstudie: integrera Aha! med Azure Active Directory

I den här självstudien får du lära dig att integrera Aha! med Azure Active Directory (Azure AD). När du integrerar Aha! med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Aha!.
* Gör det möjligt för användarna att logga in automatiskt till Aha! med deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aha! enkel inloggning (SSO) aktive rad prenumeration.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Aha! stöder **SP**-initierad enkel inloggning
* Aha! stöder **just-in-time**-användaretablering

## <a name="adding-aha-from-the-gallery"></a>Lägga till Aha! från galleriet

För att konfigurera integrering av Aha! med Azure AD behöver du lägga till Aha! från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **AHA!** i sökfältet.
1. Välj **AHA!** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Konfigurera och testa enkel inloggning med Azure AD för Aha!

Konfigurera och testa Azure AD SSO med Aha! använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Aha!.

Om du vill konfigurera och testa Azure AD SSO med Aha! slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera Aha! SSO](#configure-aha-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa AHA! test User](#create-aha-test-user)** – om du vill ha en motsvarighet till B. Simon i Aha! som är länkad till en Azure AD-representation av användaren.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för **Aha!**-programintegrering Sidan program integrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.aha.io/session/new`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.aha.io`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Aha! Klient support teamet](https://www.aha.io/company/contact) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Aha!** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Aha!.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Aha!**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-aha-sso"></a>Konfigurera enkel inloggning Enkel inloggning

1. Om du vill automatisera konfigurationen i AHA! måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren klickar du på **installations AHA!** leder dig till Aha! applicering. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Aha!. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-8.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Aha! öppna ett nytt webbläsarfönster manuellt och logga in på din Aha! företags plats som administratör och utför följande steg:

4. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/aha-tutorial/IC798950.png "Inställningar")

5. Klicka på **Konto**.
  
    ![Profil](./media/aha-tutorial/IC798951.png "Profil")

6. Klicka på **Säkerhet och enkel inloggning**.

    ![Säkerhet och enkel inloggning](./media/aha-tutorial/IC798952.png "Säkerhet och enkel inloggning")

7. I avsnittet **Enkel inloggning** anger du **Identitetsprovider** till **SAML2.0**.

    ![Säkerhet och enkel inloggning](./media/aha-tutorial/IC798953.png "Säkerhet och enkel inloggning")

8. På sidan för konfiguration av **Enkel inloggning** utför du följande steg:

    ![Enkel inloggning](./media/aha-tutorial/IC798954.png "för Aha!")

    a. I textrutan **Namn** skriver du ett namn för konfigurationen.

    b. För **Configure using** (Konfigurera med hjälp av) väljer du **Metadata File** (Metadatafil).

    c. För att ladda upp den nedladdade metadatafilen klickar du på **Bläddra**.

    d. Klicka på **Uppdatera**.

### <a name="create-aha-test-user"></a>Skapa testanvändare för Aha!

I det här avsnittet skapas en användare som heter B. Simon i Aha!. Aha! stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Aha! skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Aha! i åtkomstpanelen bör du automatiskt loggas in på Aha! som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

