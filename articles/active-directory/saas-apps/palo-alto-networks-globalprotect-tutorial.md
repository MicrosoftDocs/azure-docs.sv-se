---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Palo-nätverk – GlobalProtect | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks – GlobalProtect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 72072feebfcf8dba249d2045a399e09714177698
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963697"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Palo-nätverk – GlobalProtect

I den här självstudien får du lära dig att integrera Palo-nätverk – GlobalProtect med Azure Active Directory (Azure AD). När du integrerar Palo-nätverk – GlobalProtect med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Palo-nätverk – GlobalProtect.
* Gör det möjligt för användarna att logga in automatiskt till Palo-GlobalProtect-nätverk – med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Palo-nätverk – GlobalProtect-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Palo Alto Networks – GlobalProtect stöder **SP**-initierad enkel inloggning
* Palo Alto Networks – GlobalProtect stöder **just-in-time**-användaretablering

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Lägga till Palo Alto Networks – GlobalProtect från galleriet

Om du vill konfigurera integrering av Palo Alto Networks – GlobalProtect till Azure AD så behöver du lägga till Palo Alto Networks – GlobalProtect från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Palo-GlobalProtect Networks-** i sökrutan.
1. Välj **Palo-nätverk – GlobalProtect** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Konfigurera och testa Azure AD SSO för Palo-nätverk – GlobalProtect

Konfigurera och testa Azure AD SSO med Palo-nätverk – GlobalProtect med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Palo-nätverk – GlobalProtect.

Utför följande steg för att konfigurera och testa Azure AD SSO med Palo-GlobalProtect-nätverk:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Palo-nätverk – GLOBALPROTECT SSO](#configure-palo-alto-networks---globalprotect-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Palo-nätverk – GlobalProtect test User](#create-palo-alto-networks---globalprotect-test-user)** – om du vill ha en motsvarighet till B. Simon i Palo-Networks-GlobalProtect som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan **Palo-nätverk – GlobalProtect** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<Customer Firewall URL>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Palo Alto Networks – GlobalProtect-klientens supportteam](https://support.paloaltonetworks.com/support) för att få de värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Palo-nätverk-GlobalProtect** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Palo-GlobalProtect-nätverk.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Palo-nätverk-GlobalProtect**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Konfigurera Palo-nätverk – GlobalProtect SSO

1. Öppna Palo-GlobalProtect-nätverken som administratör i ett annat webbläsarfönster.

2. Klicka på **enhet**.

    ![Konfigurera Palo-enkel inloggning 1](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Välj **SAML-identitetsprovider** från det vänstra navigeringsfönstret och klicka på Importera om du vill importera metadatafilen.

    ![Konfigurera Palo-enkel inloggning 2](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Utför följande åtgärder i Importera-fönstret

    ![Konfigurera Palo-enkel inloggning 3](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. I textrutan **Profilnamn** anger du ett namn t.ex Azure AD GlobalProtect.

    b. I **Metadata för identitetsprovider** klickar du på **Bläddra** och väljer metadata XML-filen som du hämtade från Azure Portal

    c. Klicka på **OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Skapa Palo Alto Networks – GlobalProtect-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i Palo-nätverk – GlobalProtect. Palo Alto Networks – GlobalProtect stöder just-in-time-användaretablering som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns en användare i Palo Alto Networks – GlobalProtect så skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Palo-GlobalProtect-nätverk – inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Palo--nätverk – GlobalProtect inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Palo-nätverk – GlobalProtect i Mina appar, bör du loggas in automatiskt på Palo-GlobalProtect-för vilka du konfigurerar SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Palo-nätverk – GlobalProtect kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).