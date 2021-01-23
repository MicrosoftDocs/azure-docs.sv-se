---
title: 'Självstudie: Azure Active Directory integration med JIRA SAML SSO från Microsoft (V 5.2) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och JIRA SAML SSO från Microsoft (V5.2).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 7b85cc064babf44b14e80abc02669573b4730da2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736914"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Självstudie: Azure Active Directory integration med JIRA SAML SSO från Microsoft (V 5.2)

I den här självstudien får du lära dig att integrera JIRA SAML SSO från Microsoft (V 5.2) med Azure Active Directory (Azure AD). När du integrerar JIRA SAML SSO från Microsoft (V 5.2) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till JIRA SAML SSO från Microsoft (V 5.2).
* Gör det möjligt för användarna att logga in automatiskt till JIRA SAML SSO från Microsoft (V 5.2) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="description"></a>Beskrivning

Använd ditt Microsoft Azure Active Directory-konto med Atlassian JIRA-servern för att aktivera enkel inloggning. På så sätt kan alla organisations användare använda Azure AD-autentiseringsuppgifterna för att logga in i JIRA-programmet. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med JIRA SAML SSO från Microsoft (V5.2) behöver du följande:

- En Azure AD-prenumeration
- JIRA Core och programvaruversion 5.2 bör vara installerat och konfigurerat för Windows 64-bitarsversionen
- JIRA-servern är HTTPS-aktiverad
- Observera att de versioner som stöds för JIRA-plugin-programmet anges i avsnittet nedan.
- JIRA-servern kan nås via Internet, särskilt för Azure AD-inloggningssidan för autentisering, och bör kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter konfigureras i JIRA
- WebSudo är inaktiverat i JIRA
- Testanvändare har skapats i JIRA-serverprogrammet

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte att du använder en JIRA-produktionsmiljö. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderings miljö för Azure AD kan du få en månads utvärderings version här: [utvärderings](https://azure.microsoft.com/pricing/free-trial/)version.

## <a name="supported-versions-of-jira"></a>Versioner av JIRA som stöds

* JIRA Core och program vara: 5,2
* JIRA stöder också 6.0 till 7.12. Om du vill ha mer information klickar du på [JIRA SAML SSO från Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Observera att vårt JIRA-plugin-program också fungerar på Ubuntu version 16,04.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* JIRA SAML SSO från Microsoft (V5.2) stöder **SP**-initierad enkel inloggning

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Lägga till JIRA SAML SSO från Microsoft (V5.2) från galleriet

För att konfigurera integreringen av JIRA SAML SSO från Microsoft (V5.2) med Azure AD måste du lägga till JIRA SAML SSO från Microsoft (V5.2) från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **JIRA SAML SSO av Microsoft (v 5.2)** i sökrutan.
1. Välj **JIRA SAML SSO med Microsoft (v 5.2)** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Konfigurera och testa Azure AD SSO för JIRA SAML SSO från Microsoft (V 5.2)

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med JIRA SAML SSO från Microsoft (V 5.2) baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och en relaterad användare i JIRA SAML SSO från Microsoft (V 5.2).

Utför följande steg för att konfigurera och testa enkel inloggning för Azure AD med JIRA SAML SSO från Microsoft (V 5.2):

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera JIRA SAML SSO från Microsoft (v 5.2) SSO](#configure-jira-saml-sso-by-microsoft-v52-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa en testanvändare för JIRA SAML SSO från Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)** – för att skapa en motsvarighet till Britta Simon i JIRA SAML SSO från Microsoft (V5.2) som är länkad till en Azure AD-representation av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

1. I Azure Portal går **du till sidan** för program integrering i **JIRA SAML SSO av Microsoft (v 5.2)** och väljer sedan **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<domain:port>/`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Porten är valfri ifall den är en namngiven URL. Dessa värden tas emot under konfigurationen av Jira-pluginprogrammet, som beskrivs senare i självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
    1. I **Namn**-fältet skriver du `B.Simon`.  
    1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
    1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till JIRA SAML SSO från Microsoft (V 5.2).

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **JIRA SAML SSO från Microsoft (V5.2)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>Konfigurera JIRA SAML SSO med Microsoft (V 5.2) SSO

1. Logga in på din JIRA-instans som administratör i ett annat webbläsarfönster.

2. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Skärm bild som visar tillägg som valts från menyn Inställningar.](./media/jira52microsoft-tutorial/addon1.png)

3. Klicka på **Hantera tillägg** under fliken Tillägg.

    ![Skärm bild som visar Hantera tillägg som marker ATS på fliken tillägg.](./media/jira52microsoft-tutorial/addon7.png)

4. Ladda ned plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Ladda manuellt upp det plugin-program som tillhandahålls av Microsoft med hjälp av menyn **Upload add-on** (Ladda upp tillägg). Nedladdningen av plugin-programmet täcks av [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Skärm bild som visar Hantera tillägg med länken överför tillägg som kallas för.](./media/jira52microsoft-tutorial/addon12.png)

5. När plugin-programmet har installerats visas det i tilläggsavsnittet **Användarinstallerade**. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Skärm bild som visar avsnittet Azure A D SAML enkel inloggning för JIRA med konfigurera valt.](./media/jira52microsoft-tutorial/addon13.png)

6. Utför följande steg på konfigurationssidan:

    ![Skärm bild som visar konfigurations sidan för Microsoft JIRA S O-anslutning.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Kontrollera att det bara finns ett certifikat mappat mot appen så att det inte förekommer några fel vid lösningen av metadata. Om det finns flera certifikat får administratören ett fel vid lösningen av metadata.

    a. I textrutan **Metadata-URL** klistrar du in värdet för **URL:en med appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på knappen **Lös**. Den läser metadata-URL för IdP och fyller i information i alla fält.

    b. Kopiera värdena för **identifierare, svars-URL och inloggnings-URL** och klistra in dem respektive textrutor för **identifierare, svars-URL och inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    c. I **Login Button Name** (Namn på inloggningsknapp) skriver du namnet på den knapp som organisationen vill att användarna ser på inloggningsskärmen.

    d. I **SAML-användar-ID platser** väljer **du antingen användar-ID: t i NameIdentifier-elementet för ämnes instruktionen** eller **användar-ID: t finns i ett Attribute-element**.  Detta ID måste vara användar-ID för JIRA. Om användar-ID: t inte matchas kommer systemet inte att tillåta användare att logga in.

    > [!Note]
    > Standardplatsen för SAML-användar-ID är Name Identifier (Namnidentifierare). Du kan ändra den till ett attributalternativ och ange lämpligt attributnamn.

    e. Om du väljer **användar-ID finns i ett attribut element** , anger du namnet på attributet där användar-ID förväntas i text rutan för **attributets namn** . 

    f. Om du använder den federerade domänen (till exempel ADFS osv) med Azure AD klickar du på alternativet **Enable Home Realm Discovery** (Aktivera identifiering av hemsfär) och konfigurerar **Domännamn**.

    ex. I **Domännamn** anger du domännamnet för ADFS-baserad inloggning.

    h. Markera **aktivera enkel utloggning** om du vill logga ut från Azure AD när en användare loggar ut från Jira. 

    i. Klicka på knappen **Spara** för att spara inställningarna.

    > [!NOTE]
    > Mer information om installation och fel sökning finns i [Administratörs guiden för MS JIRA SSO Connector](./ms-confluence-jira-plugin-adminguide.md) och det finns även [vanliga frågor och svar](./ms-confluence-jira-plugin-adminguide.md) om din hjälp.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Skapa en testanvändare för JIRA SAML SSO från Microsoft (V5.2)

Om du vill att Azure AD-användare ska kunna logga in på den lokala JIRA-servern måste de tillhandahållas i den lokala JIRA-servern.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din lokala JIRA-server som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Skärm bild som visar användar hantering som valts på menyn Inställningar.](./media/jira52microsoft-tutorial/user1.png)

3. Du omdirigeras till administratörsåtkomstsidan där du kan ange **lösenord** och klicka på knappen **Bekräfta**.

    ![Skärm bild som visar sidan för administratörs åtkomst där du anger dina autentiseringsuppgifter.](./media/jira52microsoft-tutorial/user2.png)

4. På avsnittsfliken **Användarhantering** klickar du på **skapa användare**.

    ![Skärm bild som visar fliken Användar hantering där du kan skapa användare.](./media/jira52microsoft-tutorial/user3.png) 

5. I dialogrutan **”Skapa ny användare”** utför du följande steg:

    ![Skärm bild som visar dialog rutan skapa ny användare där du kan ange informationen i det här steget.](./media/jira52microsoft-tutorial/user4.png)

    a. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    b. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    c. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Klicka på **skapa användare**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till JIRA SAML SSO från Microsoft (V 5.2) inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till JIRA SAML SSO från Microsoft (V 5.2) inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen JIRA SAML SSO med Microsoft (V 5.2) i Mina appar omdirigeras det till JIRA SAML SSO från Microsoft (V 5.2) inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat JIRA SAML SSO från Microsoft (V 5.2) kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).