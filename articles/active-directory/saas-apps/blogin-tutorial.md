---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med bloggen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och blogg.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: jeedes
ms.openlocfilehash: 2a50d7b037cec2c10f83fdbbd875f80513c00a6d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517099"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blogin"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med blogg

I den här självstudien får du lära dig hur du integrerar blogg med Azure Active Directory (Azure AD). När du integrerar blogg med Azure AD kan du:

* Kontroll i Azure AD som har till gång till blogg.
* Gör det möjligt för användarna att logga in automatiskt till blogg med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Blogg med enkel inloggning aktive rad (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Blogg inlägg stöder **SP-och IDP** -INITIERAd SSO
* Bloggin stöder **just-in-Time** -etablering av användare
* När du har konfigurerat blogg kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-blogin-from-the-gallery"></a>Lägga till blogg från galleriet

Om du vill konfigurera en blogg integrering i Azure AD måste du lägga till bloggen från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri** skriver du **blogg** i rutan Sök.
1. Välj **blogg** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-blogin"></a>Konfigurera och testa Azure AD SSO för blogg

Konfigurera och testa Azure AD SSO med blogg med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i bloggen.

Om du vill konfigurera och testa Azure AD SSO med blogg, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera blogg inloggning SSO](#configure-blogin-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa en test användare](#create-blogin-test-user)** för att få en motsvarighet till B. Simon i blogg filen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **bloggen** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.blogin.co/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.blogin.co/sso/saml/callback`

1. Klicka på **Ange ytterligare URL: er** och utför följande steg om du vill konfigurera programmet i läget **SP** -initierat:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.blogin.co/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL: en. Du kan hämta de exakta värdena för dessa fält på sidan **Inställningar** på bloggen (fliken**användar ATHENTICATION** > **Konfigurera SSO och användar etablering**). Alternativt kan du kontakta [support teamet för blogg klienten](mailto:support@blogin.co) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Blogg programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar sig blogg program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name | Källattribut |
    | ------ | --------- |
    | rubrik |user.jobtitle |
    

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till bloggen.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **blogg**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-blogin-sso"></a>Konfigurera blogg inloggning

Så här konfigurerar du enkel inloggning på **blogg** inloggnings sidan till ditt blogg konto och följer de här stegen:

1. Gå till **Settings**  >  **User Authentication**  >  **Configure SSO & User etablering**.
2. På nästa skärm ändrar du status för enkel inloggning till **på** och väljer ett eget namn för inloggnings knappen för SSO som visas på inloggnings skärmen.

3. Om du har sparat **URL: en för appens Federations-URL** i det sista steget i föregående avsnitt, väljer du **URL: en** för konfigurations metoden metadata och klistra in **URL för app Federation-Metadata** i fältet metadata URL. Annars kan du ändra konfigurations metoden till **manuell**, fylla i **URL: en för inloggnings-URL för identitetsprovider (inloggnings-URL)** och **identifiera** **certifikatet (BASE64)**   som du fick från Azure AD.

4. Välj standard användar rollen för nya användare som ansluter till blogg med SSO.

5. Välj **Spara ändringar**.

En mer detaljerad förklaring av hur du konfigurerar SSO på bloggen finns i [så här konfigurerar du SSO för Microsoft Azure AD på blogg](https://blogin.co/blog/how-to-set-up-single-sign-on-sso-for-microsoft-azure-active-directory-azure-ad-267/). Du kan när som helst kontakta [bloggens support team](mailto:support@blogin.co) om du har några frågor eller behöver hjälp.

### <a name="create-blogin-test-user"></a>Skapa blogg test användare

I det här avsnittet skapas en användare som heter B. Simon i bloggen. Blogg inlägg stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i bloggen skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Bloggi på åtkomst panelen, bör du loggas in automatiskt på bloggen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova blogg med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda blogg med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
