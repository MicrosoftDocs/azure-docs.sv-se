---
title: 'Självstudie: Azure Active Directory integrering med Costpoint | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 781d1509b69ecb09189772f4a31d4872201d0b2e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652937"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Självstudie: integrera Costpoint med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Costpoint med Azure Active Directory (Azure AD). När du integrerar Costpoint med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Costpoint.
* Gör det möjligt för användarna att logga in automatiskt till Costpoint med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Costpoint för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kommer du att konfigurera och testa Azure AD SSO i en test miljö. 

* Costpoint stöder **SP-och IDP** -initierad SSO.

## <a name="generate-costpoint-metadata"></a>Generera Costpoint metadata

Costpoint SAML SSO-konfiguration beskrivs i **DeltekCostpoint711Security.pdfs** guiden. Hämta den här guiden från Deltek Costpoint Support Site och se **installations programmet för SAML enkel inloggning**  >  **Konfigurera SAML enkel inloggning mellan Costpoint och Microsoft Azure** avsnittet. Följ instruktionerna och generera en **XML-fil för COSTPOINT SP-federationsmetadata** . 

![Skärm bild som visar "produkt konfigurations verktyget" med fliken "WebLogic-Security" vald.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Lägg till Costpoint från galleriet

Om du vill konfigurera integreringen av Costpoint i Azure AD måste du lägga till Costpoint från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Costpoint** i sökrutan.
1. Välj **Costpoint** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Konfigurera och testa Azure AD SSO för Costpoint

Konfigurera och testa Azure AD SSO med Costpoint med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Costpoint.

Utför följande steg för att konfigurera och testa Azure AD SSO med Costpoint:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera COSTPOINT SSO](#configure-costpoint-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Costpoint test User](#create-costpoint-test-user)** -om du vill ha en motsvarighet till B. Simon i Costpoint som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. På sidan **Costpoint** Application Integration väljer du **enkel inloggning**.

1. I avsnittet **grundläggande SAML-konfiguration** , om du har **metadata-filen för tjänste leverantören**, Slutför följande steg:

   > [!NOTE]
   > Du hämtar metadata-filen för tjänst leverantören i [generera metadata för Costpoint](#generate-costpoint-metadata). Hur du använder filen beskrivs senare i självstudien.
 
   1. Välj knappen **Ladda upp metadatafil** och välj **XML-filen för Costpoint SP-federationsmetadata** som tidigare genererades av Costpoint och välj sedan knappen **Lägg till** för att ladda upp filen.

      ![Ladda upp metadatafilen](./media/costpoint-tutorial/upload-metadata.png)
    
   1. När metadatafilen har laddats upp fylls **ID** och **svars-URL** -värden automatiskt i Costpoint-avsnittet.

      > [!NOTE]
      > Om **ID** -och **svars-URL** -värdena inte är automatiska polulated anger du värdena manuellt enligt ditt krav. Verifiera att **identifieraren (entitets-ID)** och **svars-URL (intygets konsument tjänst-URL)** är korrekt inställda och att **ACS-URL:** en är en giltig Costpoint-URL som slutar med **/LoginServlet.CPS**.

   1. Välj **Ange ytterligare URL: er**. För **relä tillstånd** anger du ett värde med hjälp av följande mönster: `system=[your system]` (till exempel **system = DELTEKCP**).

1. På sidan **Konfigurera enskilda Sign-On med SAML** , i avsnittet SAML- **signeringscertifikat** , väljer du **kopierings** ikonen för att kopiera **URL: en för appens Federations-metadata** och spara den i anteckningar.

   ![SAML-signeringscertifikat](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Costpoint.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Costpoint**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-costpoint-sso"></a>Konfigurera Costpoint SSO

1. Återgå till konfigurations verktyget för Costpoint. I text rutan **XML-IDP för federationsmetadata** klistrar du in innehållet i URL-filen för *app Federation-Metadata* . 

   ![Konfigurations verktyg för Costpoint](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Fortsätt med instruktionerna från **DeltekCostpoint711Security.pdfs** guiden för att avsluta SAML-installationen av Costpoint.

### <a name="create-costpoint-test-user"></a>Skapa Costpoint test användare

I det här avsnittet skapar du en användare i Costpoint. Anta att användar-ID: t är **b. Simon** och att användarens namn är **b. Simon**. Arbeta med [Costpoint-klientens support team](https://www.deltek.com/about/contact-us) för att lägga till användaren på Costpoint-plattformen. Användaren måste skapas och aktive ras innan de kan använda enkel inloggning.

När användaren har skapats måste du välja användarens **autentiseringsmetod** **Active Directory**, kryss rutan **SAML-enkel inloggning** måste vara markerad och användar namnet från Azure Active Directory måste vara **Active Directory eller certifikat-ID** (visas i följande skärm bild).

![Costpoint-användare](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Costpoint-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Costpoint-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Costpoint som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Costpoint i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Costpoint som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Costpoint kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).