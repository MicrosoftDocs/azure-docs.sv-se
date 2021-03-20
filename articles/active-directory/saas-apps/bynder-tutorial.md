---
title: 'Självstudie: Azure Active Directory integrering med Bynder | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Bynder.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: jeedes
ms.openlocfilehash: 9e233e30521e5c1fa05509fa6dc42a567f6ac4e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653107"
---
# <a name="tutorial-integrate-bynder-with-azure-active-directory"></a>Självstudie: integrera Bynder med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Bynder med Azure Active Directory (Azure AD). När du integrerar Bynder med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Bynder.
* Gör det möjligt för användarna att logga in automatiskt till Bynder med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Bynder för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Bynder har stöd för **SP- och IDP**-initierad enkel inloggning
* Bynder stöder **Just-in-time**-användaretablering

## <a name="add-bynder-from-the-gallery"></a>Lägg till Bynder från galleriet

När du konfigurerar integreringen av Bynder i Azure AD, måste du lägga till Bynder från galleriet i din lista med hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Bynder** i sökrutan.
1. Välj **Bynder** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

Konfigurera och testa Azure AD SSO med Bynder med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Bynder.

Utför följande steg för att konfigurera och testa Azure AD SSO med Bynder:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
   1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera BYNDER SSO](#configure-bynder-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
   1. **[Skapa Bynder-testanvändare](#create-bynder-test-user)** – för att ha en motsvarighet till Britta Simon i Bynder som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Bynder** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    
    För en standard domän:  `https://<company name>.getbynder.com`
    
    För en anpassad domän:  `https://<subdomain>.<domain>.com`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    
     För en standard domän:  `https://<company name>.getbynder.com/sso/SAML/authenticate/`
    
    För en anpassad domän:  `https://<subdomain>.<domain>.com/sso/SAML/authenticate/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
     För en standard domän:  `https://<company name>.getbynder.com/login/`
    
     För en anpassad domän:  ` https://<subdomain>.<domain>.com/login/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Bynder-klientens supportteam](https://www.bynder.com/en/support/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enskilda Sign-On med SAML** , i avsnittet SAML- **signeringscertifikat** , letar du upp **metadata-XML** och väljer **Hämta** för att ladda ned certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Bynder** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Bynder.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan med program väljer du **Bynder**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-bynder-sso"></a>Konfigurera Bynder SSO

Om du vill konfigurera enkel inloggning på **Bynder** sida måste du skicka den hämtade **metadata-XML** och lämpliga kopierade url: er från Azure Portal till [support teamet för Bynder](https://www.bynder.com/en/support/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.


### <a name="create-bynder-test-user"></a>Skapa Bynder-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Bynder. Bynder stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Bynder skapas en ny efter autentiseringen.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för Bynder](https://www.bynder.com/en/support/).

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Bynder-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Bynder-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Bynder som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Bynder i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Bynder som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Bynder kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).