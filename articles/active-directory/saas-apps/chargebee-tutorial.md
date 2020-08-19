---
title: 'Självstudie: Azure Active Directory integrering med Chargebee | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Chargebee.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.openlocfilehash: 6358c41ab6d63baa8ed1fa6960d7d887825a1d78
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88529494"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>Självstudie: integrera Chargebee med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Chargebee med Azure Active Directory (Azure AD). När du integrerar Chargebee med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Chargebee.
* Gör det möjligt för användarna att logga in automatiskt till Chargebee med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Chargebee för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Chargebee stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-chargebee-from-the-gallery"></a>Lägga till Chargebee från galleriet

Om du vill konfigurera integreringen av Chargebee i Azure AD måste du lägga till Chargebee från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Chargebee** i sökrutan.
1. Välj **Chargebee** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>Konfigurera och testa enkel inloggning med Azure AD för Chargebee

Konfigurera och testa Azure AD SSO med Chargebee med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Chargebee.

Om du vill konfigurera och testa Azure AD SSO med Chargebee, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera CHARGEBEE SSO](#configure-chargebee-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Chargebee test User](#create-chargebee-test-user)** -om du vill ha en motsvarighet till B. Simon i Chargebee som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Chargebee** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<domainname>.chargebee.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://app.chargebee.com/saml/<domainname>/acs`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>` är namnet på den domän som användaren skapar efter att ha gjort anspråk på kontot. Kontakta [support teamet för Chargebee-klienten](mailto:support@chargebee.com)om du har annan information. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Chargebee** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Chargebee.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Chargebee**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-chargebee-sso"></a>Konfigurera Chargebee SSO

1. Öppna ett nytt webbläsarfönster och logga in på din Chargebee företags webbplats som administratör.

4. Klicka på **Inställningar**  >  **säkerhets**  >  **hantering**på vänster sida av menyn.

    ![Chargebee-konfiguration](./media/chargebee-tutorial/config01.png)

5. Utför följande steg på popup-fönstret för **enkel inloggning** :

    ![Chargebee-konfiguration](./media/chargebee-tutorial/config02.png)

    a. Välj **SAML**.

    b. I text rutan **inloggnings-URL** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    c. Öppna det Base64-kodade certifikatet i anteckningar, kopiera dess innehåll och klistra in det i text rutan **SAML-certifikat** .

    d. Klicka på **Bekräfta**.

### <a name="create-chargebee-test-user"></a>Skapa Chargebee test användare

Om du vill aktivera Azure AD-användare loggar du in på Chargebee, de måste vara etablerade i Chargebee. I Chargebee är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Chargebee som säkerhets administratör i ett annat webbläsarfönster.

2. På den vänstra sidan av menyn, klickar du på **kunder** och navigerar sedan för att **skapa en ny kund**.

    ![Freedcamp-konfiguration](./media/chargebee-tutorial/config03.png)

3. På sidan **ny kund** fyller du i respektive fält som visas nedan och klickar på **Skapa kund** för att skapa användare.

    ![Freedcamp-konfiguration](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Chargebee på åtkomst panelen, bör du loggas in automatiskt på den Chargebee som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

