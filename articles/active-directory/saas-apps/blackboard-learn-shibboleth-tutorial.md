---
title: 'Självstudie: Azure Active Directory integration med svart tavla lär dig – Shibboleth | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Blackboard Learn – Shibboleth.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: b1b3f265d0e1fcad2953292c5227c2630c6df229
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649921"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Självstudie: Azure Active Directory integration med svart tavla lär dig – Shibboleth

I den här självstudien får du lära dig hur du integrerar svart tavla lära-Shibboleth med Azure Active Directory (Azure AD). När du integrerar svart tavla lär du dig Shibboleth med Azure AD kan du:

* Kontroll i Azure AD som har till gång till svart, lär dig Shibboleth.
* Gör det möjligt för användarna att logga in automatiskt till svart tavla lär-Shibboleth med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:
 
* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En svart tavla lär dig – Shibboleth-aktiverad prenumeration (Single Sign-on).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Blackboard Learn – Shibboleth har stöd för **SP**-initierad enkel inloggning

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>Lägg till svart tavla lär dig – Shibboleth från galleriet

För att konfigurera integrering av Blackboard Learn – Shibboleth i Azure AD behöver du lägga till Blackboard Learn – Shibboleth från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **svart lära-Shibboleth** i sökrutan.
1. Välj **svart tavla lär dig – Shibboleth** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Konfigurera och testa Azure AD SSO för svart lär dig – Shibboleth

Konfigurera och testa Azure AD SSO med svart tavla lär dig Shibboleth med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i svart – lär dig Shibboleth.

Gör så här om du vill konfigurera och testa Azure AD SSO med svart tavla lär dig Shibboleth:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera svart tavla lär dig – SHIBBOLETH SSO](#configure-blackboard-learn---shibboleth-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa svart tavla lär dig – Shibboleth test User](#create-blackboard-learn---shibboleth-test-user)** – om du vill ha en motsvarighet till B. Simon i svart tavla lär du Shibboleth som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Blackboard Learn – Shibboleth:

1. I Azure Portal väljer du **enkel inloggning** på sidan för svart program integrering på **svart tavla-Shibboleth** .

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

3. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [kundsupporten för Blackboard Learn – Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Blackboard Learn – Shibboleth** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att ge åtkomst till svart – lär dig Shibboleth.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Blackboard Learn – Shibboleth**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-blackboard-learn---shibboleth-sso"></a>Konfigurera svart tavla lär-Shibboleth SSO

För att konfigurera enkel inloggning på **Blackboard Learn – Shibboleth**-sidan behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Blackboard Learn – Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Skapa testanvändare för Blackboard Learn – Shibboleth

I det här avsnittet skapar du en användare med namnet Britta Simon i Blackboard Learn – Shibboleth. Arbeta med [svart lär dig – Shibboleth support team](https://www.blackboard.com/forms/contact-us_form.aspx) för att lägga till användarna på svart-Shibboleth-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta kommer att omdirigera till svart tavla lär dig Shibboleth inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till svart tavla lär dig Shibboleth inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på ikonen lär dig Shibboleth på svart tavla i Mina appar, bör du loggas in automatiskt på svart tavla-Shibboleth som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat svart tavla lär du dig Shibboleth, som skyddar exfiltrering och intrånget av organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).