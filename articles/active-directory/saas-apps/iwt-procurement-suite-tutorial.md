---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med IWT anskaffnings paket | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IWT-inköps paket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/23/2020
ms.author: jeedes
ms.openlocfilehash: 97e246020aa1afe2b1e37eb7034638e213d44932
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552648"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iwt-procurement-suite"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med IWT anskaffnings paket

I den här självstudien får du lära dig att integrera IWT-inköps paket med Azure Active Directory (Azure AD). När du integrerar IWT-inköps paket med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till IWT-inköps paket.
* Gör det möjligt för användarna att logga in automatiskt till IWT-inköps paket med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* IWT för enkel inloggning (SSO) för inköps paket.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* IWT anskaffnings paket stöder **IDP** INITIERAd SSO
* När du har konfigurerat IWT anskaffnings paket kan du framtvinga kontroll över sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iwt-procurement-suite-from-the-gallery"></a>Lägga till IWT tillvaratagande Suite från galleriet

Om du vill konfigurera integreringen av IWT-inköps paket i Azure AD måste du lägga till IWT anskaffnings Suite från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **IWT anskaffnings Suite** i sökrutan.
1. Välj **IWT anskaffnings Suite** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-iwt-procurement-suite"></a>Konfigurera och testa enkel inloggning med Azure AD för IWT-inköps paket

Konfigurera och testa Azure AD SSO med IWT anskaffnings Svit med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i IWT anskaffnings paket.

Om du vill konfigurera och testa Azure AD SSO med IWT anskaffnings Suite slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera IWT tillvaratagande Suite SSO](#configure-iwt-procurement-suite-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa IWT-test för inköps Suite](#create-iwt-procurement-suite-test-user)** för att få en motsvarighet till B. Simon i IWT-inköps paket som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **IWT anskaffnings Suite** -programintegration, hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://[customersubdomain].ionwave.net/sso/[customerid]`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://[customersubdomain].ionwave.net/sso/[customerid]`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [IWT för anskaffnings Suite](mailto:support@ionwave.net) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. IWT-programmet för inköps paket förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärm bild visar en lista över standardattribut, där as **EmailAddress** mappas med **User. mail**. IWT för anskaffnings paket förväntar sig att **EmailAddress** mappas med **User. UserPrincipalName**, så du måste redigera mappningen av attributet genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/default-attributes.png)



1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till IWT-inköps paket.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **IWT anskaffnings paket**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-iwt-procurement-suite-sso"></a>Konfigurera IWT tillvaratagande Suite SSO

Om du vill konfigurera enkel inloggning på IWT-sidan för **inköps paket** måste du skicka **URL: en för appens Federations-metadata** till [IWT support team för anskaffnings Suite](mailto:support@ionwave.net). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-iwt-procurement-suite-test-user"></a>Skapa IWT-test användare för inköps Suite

I det här avsnittet skapar du en användare med namnet Britta Simon i IWT-inköps paketet. Arbeta med [IWT-teamet för anskaffnings Suite](mailto:support@ionwave.net) för att lägga till användarna i IWT-plattformen för inköps Suite. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen IWT anskaffnings paket på åtkomst panelen, bör du loggas in automatiskt till IWT-inköps paketet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova IWT anskaffnings paket med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du IWT tillvaratagande Suite med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

