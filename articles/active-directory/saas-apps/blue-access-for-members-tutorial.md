---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med blå åtkomst för medlemmar (BAM) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och blå åtkomst för medlemmar (BAM).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: b0a12c7ce4ac5faac7103d0032feff7788431751
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553064"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blue-access-for-members-bam"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med blå åtkomst för medlemmar (BAM)

I den här självstudien får du lära dig hur du integrerar blå åtkomst för medlemmar (BAM) med Azure Active Directory (Azure AD). När du integrerar blå åtkomst för medlemmar (BAM) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till blå åtkomst för medlemmar (BAM).
* Gör det möjligt för användarna att logga in automatiskt till blå åtkomst för medlemmar (BAM) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) för enkel inloggning (SSO) med blå åtkomst för medlemmar (BAM).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.


* Blå åtkomst för medlemmar (BAM) stöder **IDP** -INITIERAd SSO




## <a name="adding-blue-access-for-members-bam-from-the-gallery"></a>Lägga till blå åtkomst för medlemmar (BAM) från galleriet

Om du vill konfigurera integrering av blå åtkomst för medlemmar (BAM) i Azure AD måste du lägga till blå åtkomst för medlemmar (BAM) från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **blå åtkomst för medlemmar (BAM)** i sökrutan.
1. Välj **blå åtkomst för medlemmar (BAM)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-blue-access-for-members-bam"></a>Konfigurera och testa enkel inloggning med Azure AD för blå åtkomst för medlemmar (BAM)

Konfigurera och testa Azure AD SSO med blå åtkomst för medlemmar (BAM) med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i blå åtkomst för medlemmar (BAM).

Om du vill konfigurera och testa Azure AD SSO med blå åtkomst för medlemmar (BAM) slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera blå åtkomst för medlemmar (BAM) SSO](#configure-blue-access-for-members-bam-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa en blå åtkomst för medlemmar (BAM)-test användare](#create-blue-access-for-members-bam-test-user)** – för att få en motsvarighet till B. Simon i blå åtkomst för medlemmar (BAM) som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering med **blå åtkomst för medlemmar (BAM)** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<Custom Domain Value>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<CUSTOMURL>/affwebservices/public/saml2assertionconsumer`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<CUSTOMURL>/BAMSSOServlet/sso/BamInboundSsoServlet`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och relä status. Få till gång till de här värdena genom att kontakta ett [support team med blå åtkomst för medlemmar (BAM)](https://www.bcbstx.com/contact-us) . Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I programmet för blå åtkomst för medlemmar (BAM) förväntas SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom över, förväntar sig att det finns fler attribut som ska skickas tillbaka i SAML-svar som visas nedan, med blå åtkomst för medlemmar (BAM). Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | ------------ | --------- |
    | ClientID | `<ClientID>` |
    | UID | `<UID>` |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera blå åtkomst för medlemmar (BAM)** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till blå åtkomst för medlemmar (BAM).

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **blå åtkomst för medlemmar (BAM)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-blue-access-for-members-bam-sso"></a>Konfigurera Blue Access för medlemmar (BAM) SSO

Om du vill konfigurera enkel inloggning på **blå åtkomst för medlemmar (BAM)** sidan, måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [stöd teamet för blå åtkomst för medlemmar (BAM)](https://www.bcbstx.com/contact-us). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-blue-access-for-members-bam-test-user"></a>Skapa test användare med blå åtkomst för medlemmar (BAM)

I det här avsnittet skapar du en användare som heter B. Simon i blå åtkomst för medlemmar (BAM). Arbeta med [support teamet för Blue Access for members (BAM)](https://www.bcbstx.com/contact-us) för att lägga till användare i den blå plattformen för åtkomst till medlemmar (BAM). Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen blå åtkomst för medlemmar (BAM) i åtkomst panelen, bör du loggas in automatiskt på den blå åtkomsten för medlemmar (BAM) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa blå åtkomst för medlemmar (BAM) med Azure AD](https://aad.portal.azure.com/)