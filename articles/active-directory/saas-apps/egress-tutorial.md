---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med egress-| Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och utgående.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 392697978041cdab50f45e22a73acbadb9122546
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519446"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egress"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med egress

I den här självstudien lär du dig att integrera Egress med Azure Active Directory (Azure AD). När du integrerar Egress med Azure AD kan du:

* Kontrollera vem som har åtkomst till Egress i Azure AD.
* Gör så att dina användare automatiskt loggas in på Egress med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* En SSO-aktiverad prenumeration (Egress single sign-on).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Egress stöder **SP- och IDP-initierad** enkel inloggning.
* Utgående stöder **just-in-time-användareablering.**

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="add-egress-from-the-gallery"></a>Lägga till utgående från galleriet

För att konfigurera integreringen av Egress i Azure AD behöver du lägga till Egress från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Egress** i sökrutan.
1. Välj **Utgående** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-egress"></a>Konfigurera och testa Azure AD SSO för utgående

Konfigurera och testa Azure AD SSO med Egress med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Egress.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med egress:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera utgående enkel inloggning](#configure-egress-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa testanvändare för utgående](#create-egress-test-user)** – för att ha en motsvarighet för B.Simon i Egress som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal på sidan **för egress-programintegrering** går **du till avsnittet** Hantera och väljer enkel **inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://switch.egress.com/ui/`

1. Klicka på **Spara**.

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet **SAML-signeringscertifikat,** klickar på kopieringsknappen för att kopiera URL:en för **appfederationsmetadata** och sparar den på datorn. 

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure Portal namnet B.Simon.

1. I den vänstra rutan i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Egress.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Utgående**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-egress-sso"></a>Konfigurera enkel inloggning för utgående

1. I ett annat webbläsarfönster loggar du in på din utgående företagswebbplats som administratör.

1. Utför följande steg på följande sida.

    ![Konfiguration av utgående](./media/egress-tutorial/configure-1.PNG)

    a. Klicka på SSO Configuration **(Konfiguration av enkel inloggning) på menyn till vänster.**

    b. Välj **alternativknappen Använd enkel inloggning** för att använda enkel inloggning.

    c. Ge en giltig beskrivning i **textrutan Providerbeskrivning.**

    d. I **textrutan Metadata-URL** klistrar du in värdet för den URL för **appfederationsmetadata** som du har kopierat.

    e. Klicka på **Läs in metadata.**

    f. Klicka på **knappen Spara** för att uppdatera SSO-konfigurationen.

### <a name="create-egress-test-user"></a>Skapa utgående testanvändare

1. Logga in på din **utgående** företagsplats.

1. Klicka på **Bjud** in användare på menyn till vänster och klicka på **Bjud in enskild användare för** att lägga till användaren.

    ![Skärmbild som visar sidan Bjud in användare med knappen Bjud in enskild användare markerad.](./media/egress-tutorial/create-user-1.PNG)

1. Fyll i de obligatoriska fälten och klicka på **Bjud in**.

    ![Skapa testanvändare för utgående](./media/egress-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till utgående inloggnings-URL där du kan initiera inloggningsflödet.  

* Gå till utgående inloggnings-URL direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** Azure Portal så bör du automatiskt loggas in på den egress som du har ställt in enkel inloggning för. 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på panelen Utgående i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på den egress som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Utgående kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
