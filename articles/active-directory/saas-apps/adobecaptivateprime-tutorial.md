---
title: 'Självstudie: Azure Active Directory integrering med Adobe Captivate primtal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Captivate Prime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: 87ce580e16d1ca3b90eb66562f06828d775b09ea
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285711"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Självstudie: Azure Active Directory integrering med Adobe Captivate primtal

I den här självstudien får du lära dig hur du integrerar Adobe Captivate primtal med Azure Active Directory (Azure AD). När du integrerar Adobe Captivate primtal med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Adobe Captivate primtal.
* Gör det möjligt för användarna att logga in automatiskt till Adobe Captivate-primtalen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Adobe Captivate-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Adobe Captivate primtal stöder **IDP** initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-adobe-captivate-prime-from-the-gallery"></a>Lägg till Adobe Captivate primtal från galleriet

För att konfigurera integreringen av Adobe Captivate Prime i Azure AD måste du lägga till Adobe Captivate Prime från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Adobe Captivate primtal** i sökrutan.
1. Välj **Adobe Captivate primtal** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-adobe-captivate-prime"></a>Konfigurera och testa Azure AD SSO för Adobe Captivate primtal

Konfigurera och testa Azure AD SSO med Adobe Captivate primtal med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Adobe Captivate primtal.

Utför följande steg för att konfigurera och testa Azure AD SSO med Adobe Captivate primtal:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Adobe Captivate primtal](#configure-adobe-captivate-prime-sso)** -för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Adobe Captivate-test User](#create-adobe-captivate-prime-test-user)** -för att få en motsvarighet till B. Simon i Adobe Captivate-blocket som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Adobe Captivate** -program integration i letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    a. Skriv webb adressen i text rutan **identifierare** : `https://captivateprime.adobe.com`

    b. Skriv webb adressen i text rutan **svars-URL** : `https://captivateprime.adobe.com/saml/SSO`

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Adobe Captivate Prime** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

7. Gå till fliken **Egenskaper**, kopiera **URL för användaråtkomst** och klistra in den i Anteckningar.

    ![Länk för användaråtkomst](./media/adobecaptivateprime-tutorial/adobe.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Adobe Captivate primtal.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Adobe Captivate Prime**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-adobe-captivate-prime-sso"></a>Konfigurera Adobe Captivate-primär SSO

För att konfigurera enkel inloggning på **Adobe Captivate Prime**-sidan behöver du skicka nedladdad **federationsmetadata-XML**, kopierad **URL för användaråtkomst** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-adobe-captivate-prime-test-user"></a>Skapa Adobe Captivate Prime-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Adobe Captivate Prime. Arbeta med [Adobe captivates huvud support grupp](mailto:captivateprimesupport@adobe.com) för att lägga till användare i den inbyggda plattformen Adobe Captivate. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den Adobe Captivate-huvud server som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Adobe Captivate primärt i Mina appar, bör du loggas in automatiskt till det Adobe Captivate-huvud som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Adobe Captivate primtal kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
