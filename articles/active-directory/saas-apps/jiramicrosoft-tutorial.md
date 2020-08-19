---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med JIRA SAML SSO från Microsoft | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och JIRA SAML SSO från Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.openlocfilehash: 08f5829eb624f13be44bba633f3188d8d9876058
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547468"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med JIRA SAML SSO från Microsoft

I den här självstudien får du lära dig att integrera JIRA SAML SSO från Microsoft med Azure Active Directory (Azure AD). När du integrerar JIRA SAML SSO av Microsoft med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till JIRA SAML SSO av Microsoft.
* Gör det möjligt för användarna att logga in automatiskt till JIRA SAML SSO från Microsoft med deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Description

Använd ditt Microsoft Azure Active Directory-konto med Atlassian JIRA-servern för att aktivera enkel inloggning. På så sätt kan alla organisations användare använda Azure AD-autentiseringsuppgifterna för att logga in i JIRA-programmet. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med JIRA SAML SSO från Microsoft behöver du följande:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
- JIRA Core och program vara 6,4 till 8.11.0 eller JIRA Service Desk 3,0 till 4.8.0 bör installeras och konfigureras på Windows 64-bitars version
- JIRA-servern är HTTPS-aktiverad
- Observera att de versioner som stöds för JIRA-plugin-programmet anges i avsnittet nedan.
- JIRA-servern kan nås via Internet, särskilt för Azure AD-inloggningssidan för autentisering, och bör kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter konfigureras i JIRA
- WebSudo är inaktiverat i JIRA
- Testanvändare har skapats i JIRA-serverprogrammet

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte att du använder en JIRA-produktionsmiljö. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

För att komma igång behöver du följande objekt:

* Använd inte din produktionsmiljö om det inte behövs.
* JIRA SAML SSO med Microsoft enkel inloggning (SSO) aktive rad prenumeration.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="supported-versions-of-jira"></a>Versioner av JIRA som stöds

* JIRA Core och program vara: 6,4 till 8.11.0
* JIRA Service Desk 3.0.0 till 4.8.0
* JIRA stöder också 5.2. Om du vill ha mer information klickar du på [Microsoft Azure Active Directory single sign-on for JIRA 5.2](jira52microsoft-tutorial.md) (Microsoft Azure Active Directory enkel inloggning för JIRA 5.2)

> [!NOTE]
> Observera att vårt JIRA-plugin-program också fungerar på Ubuntu version 16,04 och Linux.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Jira SAML SSO från Microsoft stöder **SP**-initierad enkel inloggning

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Lägga till Jira SAML SSO från Microsoft från galleriet

För att konfigurera integreringen av Jira SAML SSO från Microsoft med Azure AD måste du lägga till JIRA SAML SSO från Microsoft från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **JIRA SAML SSO av Microsoft** i sökrutan.
1. Välj **JIRA SAML SSO av Microsoft** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jira-saml-sso-by-microsoft"></a>Konfigurera och testa enkel inloggning med Azure AD för JIRA SAML SSO från Microsoft

Konfigurera och testa Azure AD SSO med JIRA SAML SSO från Microsoft med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i JIRA SAML SSO från Microsoft.

Om du vill konfigurera och testa Azure AD SSO med JIRA SAML SSO från Microsoft slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera JIRA SAML SSO med Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa JIRA SAML SSO av Microsoft test User](#create-jira-saml-sso-by-microsoft-test-user)** -om du vill ha en motsvarighet till B. Simon i JIRA SAML SSO av Microsoft som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **Hantera** och välj **enkel inloggning**på sidan **JIRA SAML SSO av Microsoft** Application Integration.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<domain:port>/`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Porten är valfri ifall den är en namngiven URL. Dessa värden tas emot under konfigurationen av Jira-pluginprogrammet, som beskrivs senare i självstudien.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till JIRA SAML SSO från Microsoft.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **JIRA SAML SSO från Microsoft**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Konfigurera JIRA SAML SSO med Microsoft SSO

1. Logga in på din JIRA-instans som administratör i ett annat webbläsarfönster.

2. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon1.png)

3. Ladda ned plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). Ladda manuellt upp det plugin-program som tillhandahålls av Microsoft med hjälp av menyn **Upload add-on** (Ladda upp tillägg). Nedladdningen av plugin-programmet täcks av [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon12.png)

4. Utför följande steg för att köra scenariot med omvänd JIRA-proxy eller lastbalanseringsscenariot:

    > [!NOTE]
    > Du bör konfigurera servern först med instruktionerna nedan och därefter installera plugin-programmet.

    a. Lägg till attributen nedan i **anslutningsappens** port i filen **server.xml** för JIRA-serverprogram.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Ändra **grundläggande URL** i **Systeminställningar** enligt proxy/lastbalanserare.

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. När plugin-programmet har installerats visas det i avsnittet för **användarinstallerade** tillägg i avsnittet **Manage Add-on** (Hantera tillägg). Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon14.png)

6. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Kontrollera att det bara finns ett certifikat mappat mot appen så att det inte förekommer några fel vid lösningen av metadata. Om det finns flera certifikat får administratören ett fel vid lösningen av metadata.

    1. I textrutan **Metadata URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på knappen **Lös**. Den läser metadata-URL för IdP och fyller i information i alla fält.

    1. Kopiera värdena för **identifierare, svars-URL och inloggnings-URL** och klistra in dem respektive textrutor för **identifierare, svars-URL och inloggnings-URL** i avsnittet **JIRA SAML SSO by Microsoft Domain and URL** (JIRA SAML SSO från Microsoft-domän och information om URL:er) i Azure-portalen.

    1. I **Login Button Name** (Namn på inloggningsknapp) skriver du namnet på den knapp som organisationen vill att användarna ser på inloggningsskärmen.
    
    1. I **Beskrivning av inloggnings knapp** anger du en beskrivning av knappen som din organisation vill att användarna ska kunna se på inloggnings skärmen.

    1. I **SAML-användar-ID platser** väljer **du antingen användar-ID: t i NameIdentifier-elementet för ämnes instruktionen** eller **användar-ID: t finns i ett Attribute-element**.  Detta ID måste vara användar-ID för JIRA. Om användar-ID: t inte matchas kommer systemet inte att tillåta användare att logga in.

       > [!Note]
       > Standardplatsen för SAML-användar-ID är Name Identifier (Namnidentifierare). Du kan ändra den till ett attributalternativ och ange lämpligt attributnamn.

    1. Om du väljer **användar-ID finns i ett attribut element** , anger du namnet på attributet där användar-ID förväntas i text rutan för **attributets namn** .

    1. Om du använder den federerade domänen (till exempel ADFS osv) med Azure AD klickar du på alternativet **Enable Home Realm Discovery** (Aktivera identifiering av hemsfär) och konfigurerar **Domännamn**.

    1. I **Domännamn** anger du domännamnet för ADFS-baserad inloggning.

    1. Markera **aktivera enkel utloggning** om du vill logga ut från Azure AD när en användare loggar ut från Jira.
    
    1. Kryss rutan Aktivera **tvinga Azure-inloggning** om du bara vill logga in via autentiseringsuppgifter för Azure AD.
    
       > [!Note]
       > Om du vill aktivera standard inloggnings formuläret för inloggning på inloggnings sidan när tvinga Azure-inloggning är aktiverat lägger du till frågeparametern i URL: en för webbläsaren.
       > `https://<domain:port>/login.jsp?force_azure_login=false`

    1. Klicka på knappen **Spara** för att spara inställningarna.

       > [!NOTE]
       > Mer information om installation och fel sökning finns i [Administratörs guiden för MS JIRA SSO Connector](../ms-confluence-jira-plugin-adminguide.md). Det finns även [vanliga frågor och svar](../ms-confluence-jira-plugin-faq.md) om din hjälp.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Skapa JIRA SAML SSO genom Microsoft-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på en lokal server måste de vara etablerade i JIRA SAML SSO av Microsoft. När det gäller JIRA SAML SSO från Microsoft är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din lokala JIRA-server som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user1.png)

3. Du omdirigeras till administratörsåtkomstsidan där du kan ange **lösenord** och klicka på knappen **Bekräfta**.

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user2.png)

4. På avsnittsfliken **Användarhantering** klickar du på **skapa användare**.

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user3.png) 

5. I dialogrutan **”Skapa ny användare”** utför du följande steg:

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user4.png) 

    a. I textrutan för **e-postadress** skriver du användarens e-postadress som B.simon@contoso.com.

    b. I text rutan **fullständigt namn** skriver du det fullständiga namnet på användaren som B. Simon.

    c. Skriv e-postadressen för användaren i textrutan **Användarnamn** som B.simon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Klicka på **skapa användare**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på JIRA SAML SSO från Microsoft-panelen i åtkomstpanelen bör du automatiskt loggas in på JIRA SAML SSO från Microsoft som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova JIRA SAML SSO av Microsoft med Azure AD](https://aad.portal.azure.com/)
