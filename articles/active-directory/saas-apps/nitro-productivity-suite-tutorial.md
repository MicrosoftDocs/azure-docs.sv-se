---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Nitro Productivity Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nitro Productivity Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181925"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Nitro Productivity Suite

I den här självstudien får du lära dig att integrera Nitro Productivity Suite med Azure Active Directory (Azure AD). När du integrerar Nitro Productivity Suite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Nitro Productivity Suite.
* Gör det möjligt för användarna att logga in automatiskt till Nitro Productivity Suite med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Nitro Productivity Suite [Enterprise-prenumeration](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Nitro Productivity Suite stöder **SP** -och **IDP** -initierad SSO.
* Nitro Productivity Suite stöder **just-in-Time** User-etablering.

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Lägg till Nitro Productivity Suite från galleriet

Om du vill konfigurera integrationen av Nitro Productivity Suite i Azure AD måste du lägga till Nitro Productivity Suite från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du **Azure Active Directory**.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Nitro Productivity Suite** i sökrutan.
1. Välj **Nitro Productivity Suite** från resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Konfigurera och testa enkel inloggning med Azure AD för Nitro Productivity Suite

Konfigurera och testa Azure AD SSO med Nitro Productivity Suite genom att använda en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i Nitro Productivity Suite.

Om du vill konfigurera och testa Azure AD SSO med Nitro Productivity Suite slutför du följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.

    a. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    
    b. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
    
2. [Skapa en Nitro Productivity Suite-test](#create-a-nitro-productivity-suite-test-user) för att få en motsvarighet till B. Simon i Nitro Productivity Suite som är länkat till användarens Azure AD-representation.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. Leta upp avsnittet **Hantera** i Azure Portal på sidan **Nitro Productivity Suite** Application Integration. Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. I avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Skärm bild av avsnittet SAML-signerings certifikat med hämtnings länken markerad](common/certificatebase64.png)
    
1. I avsnittet **Konfigurera Nitro Productivity Suite** väljer du kopierings ikonen bredvid **inloggnings-URL**.
    
    ![Skärm bild av avsnittet Konfigurera Nitro Productivity Suite med URL: er och kopierings ikoner markerade](common/copy-configuration-urls.png)
    
1. I [Nitro administrations Portal](https://admin.gonitro.com/)går du till sidan **företags inställningar** och letar reda på avsnittet **enkel inloggning** . Välj **Konfigurera SAML SSO**.

    a. Klistra in **inloggnings-URL:** en från föregående steg i fältet för **inloggnings-URL** .
    
    b. Ladda upp **certifikatet (base64)** från det tidigare steget i fältet **X509 signerings certifikat** .
    
    c. Välj **Skicka**.
    
    d. Välj **aktivera enkel inloggning**.


1. Gå tillbaka till [Azure-portalen](https://portal.azure.com/). På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkla Sign-On med SAML-sidan med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** kopierar du och klistrar in fältet **SAML-entitets-ID** från [administrations portalen för Nitro](https://admin.gonitro.com/). Den bör ha följande mönster: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. I text rutan **svars-URL** kopierar och klistrar du in URL-fältet för **ACS** från [Nitro-administrations portalen](https://admin.gonitro.com/). Den bör ha följande mönster: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Välj **Ange ytterligare URL: er** och utför följande steg om du vill konfigurera programmet i **SP** -initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://sso.gonitro.com/login`

1. Välj **Spara**.

1. Nitro Productivity Suite-programmet förväntar sig att SAML-intygen ska vara i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild av standardattribut](common/default-attributes.png)

1. Förutom föregående attribut förväntar sig Nitro Productivity Suite-programmet några fler attribut att skickas tillbaka i SAML-svaret. Dessa attribut fylls i i förväg, men du kan granska dem efter dina behov.
    
    | Name  |  Källattribut|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan lösen ordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Nitro Productivity Suite.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **Nitro Productivity Suite**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Skapa en Nitro Productivity Suite-test användare

Nitro Productivity Suite stöder just-in-Time-etablering av användare, som är aktiverat som standard. Det finns ingen ytterligare åtgärd att vidta. Om en användare inte redan finns i Nitro Productivity Suite skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Nitro Productivity Suite-inloggnings-URL där du kan starta inloggnings flödet.  

2. Gå till Nitro Productivity Suite-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal och du bör logga in automatiskt till Nitro Productivity Suite som du ställer in SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen Nitro Productivity Suite på åtkomst panelen, om den har kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till Nitro Productivity-sviten som du konfigurerade SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Nitro Productivity Suite kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget av organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).