---
title: 'Självstudie: Azure Active Directory integrering med Zscaler-administratör för Internet åtkomst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Internet Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 8af8d92ca66cfbd3d6223bc9a73125c457164d82
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735552"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Självstudie: Azure Active Directory integrering med Zscaler-administratör för Internet åtkomst

I den här självstudien får du lära dig att integrera Zscaler-administratör för Internet åtkomst med Azure Active Directory (Azure AD). När du integrerar Zscaler Internet åtkomst administratör med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler-administratör för Internet åtkomst.
* Gör det möjligt för användarna att logga in automatiskt för att Zscaler åtkomst till Internet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Zscaler Internet Access Administrator behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Zscaler Internet Access Administrator-prenumeration

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler Internet Access Administrator har stöd för **IDP**-initierad SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Lägger till Zscaler Internet Access Administrator från galleriet

För att konfigurera integrering av Zscaler Internet Access Administrator till Azure AD måste du lägga till Zscaler Internet Access Administrator från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Zscaler Internet Access Administrator** i sökrutan.
1. Välj **Zscaler Internet åtkomst administratör** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Konfigurera och testa Azure AD SSO för Zscaler Internet åtkomst administratör

Konfigurera och testa Azure AD SSO med Zscaler för Internet åtkomst med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler Internet Access Administrator.

Utför följande steg för att konfigurera och testa Azure AD SSO med Zscaler-administratör för Internet åtkomst:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera Zscaler Internet Access Administrator SSO](#configure-zscaler-internet-access-administrator-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa testanvändare för Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** – du har en motsvarighet till Britta Simon i Zscaler Internet Access Administrator som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Zscaler Internet åtkomst-administratör** , letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Identifierare** skriver du en URL enligt dina behov:

    | Identifierare |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. I textrutan **Svars-URL** skriver du en URL enligt dina behov:

    | Svars-URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Programmet Zscaler Internet Access Administrator förväntar sig SAML-påståenden i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan för **användarattribut och anspråk**.

    ![Länken Attribut](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Name  | Källattribut  |
    | ---------| ------------ |
    | Roll | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I listan **källattribut** väljer du attributvärdet.

    c. Klicka på **OK**.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Klicka [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om du vill veta mer om hur du konfigurerar roller i Azure AD.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera Zscaler Internet Access Administrator** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler Internet åtkomst administratör.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Zscaler-administratör för Internet åtkomst**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .


## <a name="configure-zscaler-internet-access-administrator-sso"></a>Konfigurera Zscaler Internet Access Administrator SSO

1. I ett annat webbläsarfönster loggar du in på ditt Zscaler Internet Access Admin-användargränssnitt.

2. Gå till **Administration > Administratörshantering**, utför följande steg och klicka på Spara:

    ![Skärm bild som visar administratörs hantering med alternativ för att aktivera SAML-autentisering, ladda upp S S L-certifikat och ange en utfärdare.](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Administration")

    a. Markera **Aktivera SAML-autentisering**.

    b. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    c. Om du vill ha ytterligare säkerhet kan du lägga till information om **Utfärdare** för att verifiera utfärdaren av SAML-svaret.

3. I användargränssnittet för administration utför du följande steg:

    ![Skärm bild som visar admin U I där du kan utföra stegen.](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Skapa testanvändare för Zscaler Internet Access Administrator

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i Zscaler Internet Access Administrator. Zscaler Internet-åtkomst har inte stöd för just-in-time-etablering för enkel inloggning för administratör. Du måste manuellt skapa ett administratörskonto.
Anvisningar om hur du skapar ett administratörskonto finns i Zscaler-dokumentationen:

https://help.zscaler.com/zia/adding-admins

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till den Zscaler Internet åtkomst administratör som du har konfigurerat SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på ikonen Zscaler Internet åtkomst administratör i Mina appar, bör du loggas in automatiskt till Zscaler-administratören för Internet-åtkomst som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Zscaler Internet Access-administratör kan du framtvinga kontroll av sessionen, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
