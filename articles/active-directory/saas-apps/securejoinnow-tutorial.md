---
title: 'Självstudie: Azure Active Directory integrering med SecureW2 JoinNow-anslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SecureW2 JoinNow-anslutning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 024de5acfbc4e6c4aaa510de84c0bceb6bde2b3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92672961"
---
# <a name="tutorial-integrate-securew2-joinnow-connector-with-azure-active-directory"></a>Självstudie: integrera SecureW2 JoinNow-anslutning med Azure Active Directory

I den här självstudien får du lära dig att integrera SecureW2 JoinNow-anslutningsprogram med Azure Active Directory (Azure AD). När du integrerar SecureW2 JoinNow Connector med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SecureW2 JoinNow-anslutningsprogrammet.
* Gör det möjligt för användarna att logga in automatiskt till SecureW2 JoinNow Connector med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SecureW2 JoinNow Connector Single Sign-on (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SecureW2 JoinNow-anslutning stöder **SP** -INITIERAd SSO





## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Lägga till SecureW2 JoinNow Connector från galleriet

Om du vill konfigurera integreringen av SecureW2 JoinNow-anslutningen i Azure AD måste du lägga till SecureW2 JoinNow Connector från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SecureW2 JoinNow Connector** i sökrutan.
1. Välj **SecureW2 JoinNow Connector** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med SecureW2 JoinNow Connector med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SecureW2 JoinNow-anslutningen.

Om du vill konfigurera och testa Azure AD SSO med SecureW2 JoinNow Connector slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SecureW2 JoinNow Connector SSO](#configure-securew2-joinnow-connector-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa SecureW2 JoinNow Connector test User](#create-securew2-joinnow-connector-test-user)** – om du vill ha en motsvarighet till B. Simon i SecureW2 JoinNow-anslutning som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **SecureW2 JoinNow Connector** Application Integration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [SecureW2 JoinNow Connector client support team](mailto:support@securew2.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enskilda Sign-On med SAML** , i avsnittet SAML- **signeringscertifikat** , letar du upp **metadata-XML** och väljer **Hämta** för att ladda ned certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera SecureW2 JoinNow-anslutning** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-securew2-joinnow-connector-sso"></a>Konfigurera SecureW2 JoinNow Connector SSO

Om du vill konfigurera enkel inloggning på **SecureW2 JoinNow Connector** -sidan måste du skicka den hämtade **metadata-XML-** filen och lämpliga kopierade url: er från Azure Portal till [SecureW2 JoinNow Connector support team](mailto:support@securew2.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SecureW2 JoinNow Connector.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SecureW2 JoinNow Connector**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-securew2-joinnow-connector-test-user"></a>Skapa SecureW2 JoinNow Connector test User

I det här avsnittet skapar du en användare med namnet Britta Simon i SecureW2 JoinNow Connector. Arbeta med [SecureW2 JoinNow Connector support team](mailto:support@securew2.com) för att lägga till användare i SecureW2 JoinNow Connector-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SecureW2 JoinNow Connector-panelen på åtkomst panelen, bör du loggas in automatiskt till SecureW2 JoinNow-anslutaren som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)