---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Broadcom DX SaaS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 5aa13598133b55547484609fc6d26d9375c04045
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055655"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Broadcom DX SaaS

I den här självstudien får du lära dig att integrera Broadcom DX SaaS med Azure Active Directory (Azure AD). När du integrerar Broadcom DX SaaS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Broadcom DX SaaS.
* Gör det möjligt för användarna att logga in automatiskt till Broadcom DX SaaS med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Broadcom DX SaaS-prenumeration (enkel inloggning) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Broadcom DX SaaS stöder **IDP** initierad SSO.

* Broadcom DX SaaS stöder **just-in-Time** User-etablering.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Lägg till Broadcom DX SaaS från galleriet

Om du vill konfigurera en integrering av Broadcom DX SaaS i Azure AD måste du lägga till Broadcom DX SaaS från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Broadcom DX SaaS** i sökrutan.
1. Välj **Broadcom DX SaaS** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Konfigurera och testa Azure AD SSO för Broadcom DX SaaS

Konfigurera och testa Azure AD SSO med Broadcom DX SaaS med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Broadcom DX SaaS.

Utför följande steg för att konfigurera och testa Azure AD SSO med Broadcom DX SaaS:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Broadcom DX SaaS SSO](#configure-broadcom-dx-saas-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Broadcom DX SaaS test User](#create-broadcom-dx-saas-test-user)** – om du vill ha en motsvarighet till B. Simon i Broadcom DX SaaS som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Broadcom DX SaaS** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `DXI_<TENANT_NAME>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Broadcom DX SaaS client support team](mailto:dxi-na1@saas.broadcom.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SaaS-programmet för Broadcom DX förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar sig ett SaaS-program med Broadcom DX fler attribut att skickas tillbaka i SAML-svar, som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Namn |  Källattribut|
    | --------------- | --------- |
    | Group | användare. grupper |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Broadcom DX-SaaS** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Broadcom DX SaaS.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Broadcom DX SaaS**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-broadcom-dx-saas-sso"></a>Konfigurera Broadcom DX SaaS SSO

1. Logga in på din Broadcom DX-SaaS företags webbplats som administratör.

2. Gå till **Inställningar** och klicka på fliken **användare** .

    ![Användare](./media/broadcom-dx-saas-tutorial/settings.png "Användare")

3. I avsnittet **användar hantering** klickar du på ellipser och väljer **SAML**.

    ![Användarhantering](./media/broadcom-dx-saas-tutorial/local.png "Användarhantering")

4. I avsnittet **identifiera SAML-konto** utför du följande steg.
   
    ![Redovisning](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Konto") 

    a. I text rutan **utfärdare** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.

    b. I text rutan **identitets leverantör (IdP) inloggnings-URL** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    c. I text rutan för **utloggnings-URL för identitets leverantör (IdP)** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.

    d. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan för **identitets leverantörs certifikat** .

    e. Klicka på **Nästa**.

5. I avsnittet **Mappa attribut** fyller du i de obligatoriska SAML-attributen på följande sida och klickar på **Nästa**.

    ![Attribut](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Attribut") 


6. I avsnittet **identifiera användar grupp** anger du objekt-ID för den gruppen och klickar på **Nästa**.

    ![Lägg till grupp](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Lägg till grupp") 

7. I avsnittet **fylla i SAML-konto** kontrollerar du de fyllda värdena och klickar på **Spara**.

    ![SAML-konto](./media/broadcom-dx-saas-tutorial/broadcom-4.png "SAML-konto") 

### <a name="create-broadcom-dx-saas-test-user"></a>Skapa en Broadcom DX SaaS-test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Broadcom DX SaaS. Broadcom DX SaaS stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Broadcom DX SaaS skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på Broadcom DX-SaaS som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Broadcom DX SaaS i Mina appar, bör du loggas in automatiskt på den Broadcom DX-SaaS som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Broadcom DX SaaS kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
