---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Confluence SAML SSO från Microsoft | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Confluence SAML SSO från Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 9238a65643c9ff24166e9a9b9a05e8c924abfb37
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544490"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Confluence SAML SSO från Microsoft

I den här självstudien får du lära dig att integrera Confluence SAML SSO från Microsoft med Azure Active Directory (Azure AD). När du integrerar Confluence SAML SSO av Microsoft med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Confluence SAML SSO av Microsoft.
* Gör det möjligt för användarna att logga in automatiskt till Confluence SAML SSO från Microsoft med deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Beskrivning:

Använd ditt Microsoft Azure Active Directory-konto med Atlassian Confluence-servern för att aktivera enkel inloggning. På så sätt kan alla organisations användare använda Azure AD-autentiseringsuppgifterna för att logga in i Confluence-programmet. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Confluence SAML SSO från Microsoft behöver du följande:

- En Azure AD-prenumeration
- Confluence-serverprogrammet installerat på en Windows 64-bitars server (lokalt eller i moln-IaaS-infrastrukturen)
- Confluence-servern är HTTPS-aktiverad
- Observera att de versioner som stöds för Confluence-plugin-programmet anges i avsnittet nedan.
- Confluence-servern kan nås via Internet, särskilt för Azure AD-inloggningssidan för autentisering, och bör kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter konfigureras i Confluence
- WebSudo är inaktiverat i Confluence
- Testanvändare har skapats i Confluence-serverprogrammet

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte att du använder en produktionsmiljö i Confluence. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

För att komma igång behöver du följande objekt:

* Använd inte din produktionsmiljö om det inte behövs.
* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Confluence SAML SSO med Microsoft enkel inloggning (SSO) aktive rad prenumeration.

## <a name="supported-versions-of-confluence"></a>Versioner av Confluence som stöds

För närvarande stöds följande versioner av Confluence:

- Confluence: 5,0 till 5,10
- Confluence: 6.0.1 till 6.15.9
- Confluence: 7.0.1 till 7.6.1

> [!NOTE]
> Observera att vårt Confluence-plugin-program också fungerar på Ubuntu version 16,04

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Confluence SAML SSO från Microsoft stöder **IDP**-initierad enkel inloggning

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Lägga till Confluence SAML SSO från Microsoft från galleriet

För att konfigurera integreringen av Confluence SAML SSO från Microsoft till Azure AD behöver du lägga till Confluence SAML SSO från Microsoft från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Confluence SAML SSO av Microsoft** i sökrutan.
1. Välj **Confluence SAML SSO av Microsoft** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Konfigurera och testa enkel inloggning med Azure AD för Confluence SAML SSO från Microsoft

Konfigurera och testa Azure AD SSO med Confluence SAML SSO från Microsoft med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Confluence SAML SSO från Microsoft.

Om du vill konfigurera och testa Azure AD SSO med Confluence SAML SSO från Microsoft slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Confluence SAML SSO med Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Confluence SAML SSO av Microsoft test User](#create-confluence-saml-sso-by-microsoft-test-user)** -om du vill ha en motsvarighet till B. Simon i Confluence SAML SSO av Microsoft som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **Hantera** och välj **enkel inloggning**på sidan **Confluence SAML SSO av Microsoft** Application Integration.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<domain:port>/`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Porten är valfri ifall den är en namngiven URL. Dessa värden tas emot under konfigurationen av Confluence-pluginprogrammet, som beskrivs senare i självstudien.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Confluence SAML SSO från Microsoft.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Confluence SAML SSO från Microsoft**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Konfigurera Confluence SAML SSO med Microsoft SSO

1. Logga in på din Confluence-instans som administratör i ett annat webbläsarfönster.

1. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon1.png)

1. Ladda ned plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Ladda manuellt upp det plugin-program som tillhandahålls av Microsoft med hjälp av menyn **Upload add-on** (Ladda upp tillägg). Nedladdningen av plugin-programmet täcks av [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon12.png)

1. Utför följande steg för att köra scenariot Confluence reverse proxy eller Load Balancer:

    > [!NOTE]
    > Du bör konfigurera servern först med instruktionerna nedan och därefter installera plugin-programmet.

    a. Lägg till attributen nedan i **anslutningsappens** port i filen **server.xml** för JIRA-serverprogram.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Ändra **grundläggande URL** i **Systeminställningar** enligt proxy/lastbalanserare.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. När plugin-programmet har installerats visas det i avsnittet för **användarinstallerade** tillägg i avsnittet **Manage Add-on** (Hantera tillägg). Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon15.png)

1. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Kontrollera att det bara finns ett certifikat mappat mot appen så att det inte förekommer några fel vid lösningen av metadata. Om det finns flera certifikat får administratören ett fel vid lösningen av metadata.

    1. I textrutan **Metadata URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på knappen **Lös**. Den läser metadata-URL för IdP och fyller i information i alla fält.

    1. Kopiera värdena för **identifierare, svars-URL och inloggnings-URL** och klistra in dem respektive textrutor för **identifierare, svars-URL och inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    1. I **Login Button Name** (Namn på inloggningsknapp) skriver du namnet på den knapp som organisationen vill att användarna ser på inloggningsskärmen.
    
    1. I **Beskrivning av inloggnings knapp** anger du en beskrivning av knappen som din organisation vill att användarna ska kunna se på inloggnings skärmen.

    1. I **SAML User ID Locations** (Platser för SAML-användar-ID) väljer du antingen **User ID is in the NameIdentifier element of the Subject statement** (Användar-ID finns i elementet NameIdentifieri instruktionen Ämne) eller **User ID is in an Attribute element** (Användar-ID finns i ett Attribut-element).  Detta ID måste vara användar-ID för Confluence. Om användar-ID: t inte matchas kommer systemet inte att tillåta användare att logga in. 

       > [!Note]
       > Standardplatsen för SAML-användar-ID är Name Identifier (Namnidentifierare). Du kan ändra den till ett attributalternativ och ange lämpligt attributnamn.

    1. Om du väljer **användar-ID finns i ett attribut element** , anger du namnet på attributet där användar-ID förväntas i text rutan för **attributets namn** . 

    1. Om du använder den federerade domänen (till exempel ADFS osv) med Azure AD klickar du på alternativet **Enable Home Realm Discovery** (Aktivera identifiering av hemsfär) och konfigurerar **Domännamn**.

    1. I **Domännamn** anger du domännamnet för ADFS-baserad inloggning.

    1. Markera **aktivera enkel utloggning** om du vill logga ut från Azure AD när en användare loggar ut från Confluence. 

    1. Kryss rutan Aktivera **tvinga Azure-inloggning** om du bara vill logga in via autentiseringsuppgifter för Azure AD.

       > [!Note]
       > Om du vill aktivera standard inloggnings formuläret för Administratörs inloggning på inloggnings sidan när tvinga Azure-inloggning är aktiverat lägger du till frågeparametern i URL: en för webbläsaren.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Klicka på knappen **Spara** för att spara inställningarna.

       > [!NOTE]
       > Mer information om installation och fel sökning finns i [Administratörs guiden för MS Confluence SSO Connector](../ms-confluence-jira-plugin-adminguide.md). Det finns även [vanliga frågor och svar](../ms-confluence-jira-plugin-faq.md) om din hjälp.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Skapa Confluence SAML SSO från Microsoft-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på en lokal server måste de vara etablerade i Confluence SAML SSO av Microsoft. När det gäller Confluence SAML SSO från Microsoft är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din lokala Confluence-server som administratör.

1. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/confluencemicrosoft-tutorial/user1.png)

1. Under avsnittet användare klickar du på fliken **Lägg till användare** . Utför följande steg på dialog sidan **Lägg till en användare** :

    ![Lägga till medarbetare](./media/confluencemicrosoft-tutorial/user2.png)

    a. I text rutan **användar namn** skriver du e-postmeddelandet som B. Simon.

    b. I text rutan **fullständigt namn** skriver du det fullständiga namnet på användaren som B. Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: B.Simon@contoso.com.

    d. Skriv lösen ordet för B. Simon i text rutan **lösen ord** .

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.

    f. Klicka på knappen **Lägg till**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Confluence SAML SSO från Microsoft-panelen i åtkomstpanelen bör du automatiskt loggas in på Confluence SAML SSO från Microsoft som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Confluence SAML SSO av Microsoft med Azure AD](https://aad.portal.azure.com/)
