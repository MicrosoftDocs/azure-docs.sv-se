---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Certent kapital hantering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Certent kapital hantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 360aad288b4f91cb784e0f0e0cfd7dea47a140fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727947"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-certent-equity-management"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Certent kapital hantering

I den här självstudien får du lära dig att integrera Certent aktierelaterade hantering med Azure Active Directory (Azure AD). När du integrerar Certent egendoms hantering med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Certent aktierelaterade hantering.
* Gör det möjligt för användarna att logga in automatiskt för att Certent kapital hantering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Certent kapitalhantering, enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Certent kapital hantering stöder **IDP** INITIERAd SSO

## <a name="adding-certent-equity-management-from-the-gallery"></a>Lägga till Certent egendoms hantering från galleriet

Om du vill konfigurera integrationen av Certent egendoms hantering i Azure AD måste du lägga till Certent aktierelaterade hantering från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Certent aktierelaterade hantering** i sökrutan.
1. Välj **Certent egendoms hantering** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-certent-equity-management"></a>Konfigurera och testa Azure AD SSO för Certent kapital hantering

Konfigurera och testa Azure AD SSO med Certent kapital hantering med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Certent kapital hantering.

Utför följande steg för att konfigurera och testa Azure AD SSO med Certent kapital hantering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Certent aktierelaterade hantering SSO](#configure-certent-equity-management-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Certent kapital hantering test User](#create-certent-equity-management-test-user)** -för att få en motsvarighet till B. Simon i Certent aktierelaterade hantering som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan för **Certent kapital hanterings** program integrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.certent.com/sys/sso/saml/acs.aspx`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.certent.com/sys/sso/saml/acs.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta Certent-integrering som har tilldelats av kund lyckas Manager för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Certent aktierelaterade hanterings program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom över, förväntar sig Certent aktierelaterade hanterings program att få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------------- |
    | Firm | user.companyname |
    | USER | user.userprincipalname |
    | ROLL | user.assignedroles |

    > [!NOTE]
    > Klicka [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om du vill veta mer om hur du konfigurerar **roller** i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Certent aktie hantering** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Certent kapital hantering.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Certent aktierelaterade hantering**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-certent-equity-management-sso"></a>Konfigurera Certent aktierelaterade hantering SSO

Om du vill konfigurera enkel inloggning på **Certent kapital hanterings** sida måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till Certent-integrering som tilldelats av kund lyckas Manager. De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-certent-equity-management-test-user"></a>Skapa Certent kapital hantering test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i Certent aktierelaterade hantering. Arbeta med Certent-integrering som har tilldelats av kund support ansvarig för att lägga till användarna i Certents aktie hanterings plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till den aktie hantering för Certent som du har konfigurerat för enkel inloggning

* Du kan använda Microsoft Mina appar. När du klickar på Certent aktie hanterings panel i Mina appar, bör du loggas in automatiskt på den Certent ägar hantering som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Certent kapital hantering kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
