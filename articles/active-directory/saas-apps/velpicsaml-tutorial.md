---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med SAML-integrering med Velpic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Velpic SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.openlocfilehash: 85dfa6b57171ce68c3d962bac27016434d587080
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Velpic SAML

I den här självstudien får du lära dig hur du integrerar Velpic SAML med Azure Active Directory (Azure AD). När du integrerar Velpic SAML med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Velpic SAML.
* Gör det möjligt för användarna att logga in automatiskt för att Velpic SAML med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Velpic SAML-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Velpic SAML stöder **SP** -INITIERAd SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Lägga till Velpic SAML från galleriet

Om du vill konfigurera integrationen av Velpic SAML i Azure AD måste du lägga till Velpic SAML från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Velpic SAML** i sökrutan.
1. Välj **VELPIC SAML** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Konfigurera och testa enkel inloggning med Azure AD för Velpic SAML

Konfigurera och testa Azure AD SSO med Velpic SAML med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Velpic SAML.

Om du vill konfigurera och testa Azure AD SSO med Velpic SAML slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera VELPIC SAML SSO](#configure-velpic-saml-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa VELPIC SAML test User](#create-velpic-saml-test-user)** – om du vill ha en motsvarighet till B. Simon i Velpic SAML som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **VELPIC SAML** application integration i [Azure Portal](https://portal.azure.com/)letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<sub-domain>.velpicsaml.net`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Observera att inloggnings-URL: en för Velpic SAML-teamet och ID-värdet är tillgänglig när du konfigurerar SSO-plugin-programmet på Velpic SAML-sidan. Du måste kopiera det värdet från sidan Velpic SAML Application och klistra in det här.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera VELPIC SAML** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Velpic SAML.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **VELPIC SAML**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-velpic-saml-sso"></a>Konfigurera Velpic SAML SSO

1. Om du vill automatisera konfigurationen inom Velpic SAML måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klickar du på **Konfigurera VELPIC SAML** dirigerar dig till Velpic SAML-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Velpic SAML. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-8.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera SAML-Velpic manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Velpic SAML-företags webbplats som administratör och utför följande steg:

4. Klicka på fliken **Hantera** och gå till avsnittet **integration** där du behöver klicka på **plugin** -program för att skapa ett nytt plugin-program för inloggning.

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_1.png)

5. Klicka på knappen **Lägg till plugin'** .
    
    ![Plugin-program](./media/velpicsaml-tutorial/velpic_2.png)

6. Klicka på **SAML** -panelen på sidan Lägg till plugin-program.
    
    ![Plugin-program](./media/velpicsaml-tutorial/velpic_3.png)

7. Ange namnet på det nya SAML-plugin-programmet och klicka på **Lägg till** .

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_4.png)

8. Ange informationen på följande sätt:

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_5.png)

    a. I text rutan **namn** anger du namnet på SAML-plugin-programmet.

    b. I text rutan **utfärdar-URL** klistrar du in den **Azure AD-identifierare** som du kopierade från fönstret **Konfigurera inloggning** i Azure Portal.

    c. I **konfiguration av providerns metadata** laddar du upp metadata-XML-filen som du laddade ned från Azure Portal.

    d. Du kan också välja att aktivera SAML just i tids etablering genom att aktivera kryss rutan **Skapa automatiskt nya användare** . Om en användare inte finns i Velpic och den här flaggan inte är aktive rad kommer inloggningen från Azure att Miss förväntas. Om flaggan är aktive rad tillhandahålls användaren automatiskt till Velpic vid inloggningen. 

    e. Kopiera **URL: en för enkel inloggning** från text rutan och klistra in den i Azure Portal.
    
    f. Klicka på **Spara**.

### <a name="create-velpic-saml-test-user"></a>Skapa Velpic SAML test User

Det här steget krävs vanligt vis inte eftersom programmet bara stöder användar etablering i tid. Om den automatiska användar etableringen inte är aktive rad kan du utföra manuell skapande av användare enligt beskrivningen nedan.

Logga in på din Velpic SAML-företags webbplats som administratör och utför följande steg:
    
1. Klicka på fliken Hantera och gå till användare-avsnittet och klicka på knappen nytt för att lägga till användare.

    ![Lägg till användare](./media/velpicsaml-tutorial/velpic_7.png)

2. Utför följande steg på dialog sidan **Skapa ny användare** .

    ![användare](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Skriv det första namnet på B i text rutan för **förnamn** .

    b. I text rutan **efter namn** skriver du det sista namnet på Simon.

    c. Skriv användar namnet för B. Simon i text rutan **användar namn** .

    d. Skriv e-postadressen för kontot i text rutan för **e-post** B.Simon@contoso.com .

    e. Resten av informationen är valfri, du kan fylla det om det behövs.
    
    f. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

1. När du klickar på panelen Velpic SAML på åtkomst panelen, ska du få inloggnings sidan för SAML-programmet Velpic. Du bör se knappen **Logga in med Azure AD** på inloggnings sidan.

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_6.png)

1. Klicka på knappen **Logga in med Azure AD** för att logga in på Velpic med ditt Azure AD-konto.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Velpic SAML med Azure AD](https://aad.portal.azure.com/)

