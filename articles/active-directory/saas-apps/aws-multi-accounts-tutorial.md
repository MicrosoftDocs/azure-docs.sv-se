---
title: 'Självstudie: Azure Active Directory integration med Amazon Web Services (AWS) för att ansluta flera konton | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure AD och Amazon Web Services (AWS) (tidigare självstudier).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 20674f5a793267c3a9e2fa078f95cbf96624df13
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550185"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Självstudie: Azure Active Directory integration med Amazon Web Services (AWS) (tidigare självstudier)

I den här självstudien får du lära dig att integrera Azure Active Directory (Azure AD) med Amazon Web Services (AWS) (tidigare självstudier).

Integreringen av Amazon Web Services (AWS) med Azure AD medför följande fördelar:

- Du kan i Azure AD styra vem som har åtkomst till Amazon Web Services (AWS).
- Du kan låta dina användare automatiskt bli inloggade till Amazon Web Services (AWS) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om SaaS-appens integrering med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Tänk på att ansluta en AWS-app till alla dina AWS-konton är inte den rekommenderade metoden. I stället rekommenderar vi att du använder [den här](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) metoden för att konfigurera flera instanser av AWS-kontot till flera instanser av AWS-appar i Azure AD. Du bör bara använda den här metoden om du har några AWS-konton och-roller i den här modellen är den inte skalbar eftersom AWS-kontona och rollerna i dessa konton växer. Den här metoden använder inte import funktionen för AWS-roller med hjälp av Azure AD-etablering, så du måste manuellt lägga till/uppdatera/ta bort roller. Andra begränsningar för den här metoden finns i informationen nedan.

**Observera att vi inte rekommenderar att du använder den här metoden på grund av följande:**

* Du måste använda metoden Microsoft Graph Explorer för att uppdatera alla roller i appen. Vi rekommenderar inte att du använder manifest fil metoden.

* Vi har sett kunder som rapporterar att efter att ha lagt till ~ 1200-AppData för en enda AWS-app, och alla åtgärder i appen har börjat visa felen som är relaterade till storlek. Det finns en hård gräns för programobjektets storlek.

* Du måste uppdatera rollen manuellt när rollerna läggs till i något av kontona, vilket är en ersättnings metod och inte heller tillägg. Även om dina konton växer blir detta n x n-relation med konton och roller.

* Alla AWS-konton kommer att använda samma XML-fil för federationsmetadata och vid tidpunkten för certifikat förnyelse måste du köra den här enorma övningen för att uppdatera certifikatet på alla AWS-konton på samma gång

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Amazon Web Services (AWS)-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Amazon Web Services (AWS) stöder **SP- och IDP**-initierad enkel inloggning
* När du har konfigurerat Amazon Web Services (AWS) kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Amazon Web Services (AWS)** i sökrutan.
1. Välj **Amazon Web Services (AWS)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

1. När du har lagt till programmet går du till sidan **Egenskaper** och kopierar **objekt-ID: t**.

    ![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Amazon Web Services (AWS) baserat på en test användare som kallas "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD veta vad motsvarande användare i Amazon Web Services (AWS) är till en användare i Azure AD. Med andra ord måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Amazon Web Services (AWS).

I Amazon Web Services (AWS) tilldelar du värdet för **användar namnet** i Azure AD som värdet för **användar** namnet för att upprätta länk relationen.

För att konfigurera och testa enkel inloggning med Azure AD med Amazon Web Services (AWS) behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure Portal och konfigurerar enkel inloggning i ditt Amazon Web Services (AWS)-program.

**Utför följande steg för att konfigurera enkel inloggning i Azure AD med Amazon Web Services (AWS):**

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Amazon Web Services (AWS)**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är Förintegrerad med Azure och klickar på **Spara**.

5. Amazon Web Services (AWS)-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialog rutan **användarattribut & anspråk** .

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Name  | Källattribut  | Namnområde |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Roll            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  `https://aws.amazon.com/SAML/Attributes` |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I text rutan **namnrymd** anger du det namn områdes värde som visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på **Ladda ned** för att ladda ned **XML-metadata för federationen** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurera enkel inloggning för Amazon Web Services (AWS)

1. I ett annat webbläsarfönster loggar du in på din Amazon Web Services (AWS)-företagsplats som administratör.

1. Klicka på **AWS Home**.

    ![Konfigurera start för enkel inloggning][11]

1. Klicka på **Identitets- och åtkomsthantering**.

    ![Konfigurera identitet för enkel inloggning][12]

1. Klicka på **Identitetsprovidrar** och sedan på **Skapa provider**.

    ![Konfigurera provider för enkel inloggning][13]

1. På dialogsidan **Konfigurera provider** utför du följande steg:

    ![Dialogruta för att konfigurera enkel inloggning][14]

    a. För **Providertyp** väljer du **SAML**.

    b. I text rutan **Providernamn** anger du ett providernamn (till exempel: *WAAD*).

    c. För att ladda upp den nedladdade **metadatafilen** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **Nästa steg**.

1. På dialogsidan **Verifiera providerinformation** klickar du på **Skapa**.

    ![Konfigurera verifiering för enkel inloggning][15]

1. Klicka på **Roller** och sedan på **Skapa roll**.

    ![Konfigurera roller för enkel inloggning][16]

1. På sidan **Skapa roll** utför du följande steg:  

    ![Konfigurera förtroende för enkel inloggning][19]

    a. Välj **SAML 2.0-federation** under **Select type of trusted entity** (Välj typ av betrodd entitet).

    b. I **avsnittet Välj en saml 2,0-Provider**väljer du den **SAML-Provider** som du har skapat tidigare (till exempel: *WAAD*)

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Klicka på **Nästa: behörigheter**.

1. Sök efter **Administratörs åtkomst** i Sök fältet och markera kryss rutan **AdministratorAccess** och klicka sedan på **Nästa: Taggar**.

    ![Välj administratörs åtkomst](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Utför följande steg i avsnittet **Lägg till taggar (valfritt)** :

    ![Välj administratörs åtkomst](./media/aws-multi-accounts-tutorial/config2.png)

    a. I text rutan **nyckel** anger du nyckel namnet för t. ex.: Azureadtest.

    b. I text rutan **värde (valfritt)** anger du nyckel värdet i följande format `accountname-aws-admin` . Konto namnet får bara innehålla gemener.

    c. Klicka på **Nästa: granska**.

1. I dialogrutan **Granskning** utför du följande steg:

    ![Konfigurera granskning för enkel inloggning][34]

    a. Ange värdet i följande mönster i text rutan **roll namn** `accountname-aws-admin` .

    b. Ange samma värde som du använde för roll namnet i text rutan **roll Beskrivning** .

    c. Klicka på **Skapa roll**.

    d. Skapa så många roller som behövs och mappa dem till identitetsprovidern.

    > [!NOTE]
    > Skapa återstående andra roller som AccountName-ekonomi-admin, AccountName-Read-Only-User, AccountName-DevOps-User, AccountName-TPM-User med olika principer som ska bifogas. Senare kan dessa roll principer ändras enligt kraven per AWS-konto, men det är alltid bättre att behålla samma principer för varje roll över AWS-kontona.

1. Anteckna konto-ID för det AWS-kontot antingen från EC2 egenskaper eller IAM-instrumentpanelen enligt nedan:

    ![Välj administratörs åtkomst](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Logga nu in på [Azure Portal](https://portal.azure.com/) och gå till **grupper**.

1. Skapa nya grupper med samma namn som IAM-roller som skapats tidigare och anteckna de nya gruppernas **objekt-ID** .

    ![Välj administratörs åtkomst](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Logga ut från det aktuella AWS-kontot och logga in med ett annat konto där du vill konfigurera enkel inloggning med Azure AD.

1. När alla roller har skapats i kontona visas de i listan **roller** för dessa konton.

    ![Roll konfiguration](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Vi måste samla in alla roll ARN och betrodda entiteter för alla roller i alla konton, som vi behöver mappa manuellt med Azure AD-program.

1. Klicka på rollerna för att kopiera **rollens ARN** och **betrodda entitets** värden. Du behöver dessa värden för alla roller som du behöver skapa i Azure AD.

    ![Roll konfiguration](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Utför ovanstående steg för alla roller i alla konton och lagra dem i formatet **roll ARN, betrodda entiteter** i ett anteckningar.

1. Öppna [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på Microsoft Graph Explorer-webbplatsen med hjälp av autentiseringsuppgifterna för global administratör/medadministratör för din klient organisation.

    b. Du måste ha tillräcklig behörighet för att skapa roller. Klicka på **ändra behörigheter** för att hämta de behörigheter som krävs.

    ![Dialog rutan Microsoft Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte redan har dem) och klicka på Ändra behörigheter 

    ![Dialog rutan Microsoft Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Då uppmanas du att logga in igen och godkänna medgivande. När du har accepterat godkännandet loggas du in i Microsoft Graph Explorer igen.

    e. Ändra List rutan version till **beta**. Om du vill hämta alla tjänst huvud namn från klienten använder du följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger kan du använda följande mönster, som har din primära domän  `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialog rutan Microsoft Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Hämta den som du behöver ändra i listan över tjänstens huvud namn. Du kan också använda Ctrl + F för att söka i programmet från alla listade service princip ALS. Du kan använda följande fråga genom att använda det **objekt-ID för tjänstens huvud namn** som du har kopierat från sidan för Azure AD-egenskaper för att komma till respektive tjänst huvud namn.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialog rutan Microsoft Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    ex. Extrahera egenskapen appRoles från objektet tjänstens huvud namn.

    ![Dialog rutan Microsoft Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Du måste nu generera nya roller för ditt program. 

    i. Under-JSON är ett exempel på appRoles-objekt. Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Du kan bara lägga till nya roller efter **msiam_access** för korrigerings åtgärden. Du kan också lägga till så många roller du vill per organisationens behov. Azure AD skickar **värdet** för dessa roller som anspråks värde i SAML-svar.

    j. Gå tillbaka till Microsoft Graph Explorer och ändra-metoden från **Hämta** till **patch**. Uppdatera tjänstens huvud namns objekt till att ha önskade roller genom att uppdatera appRoles-egenskapen som liknar den som visas ovan i exemplet. Kör korrigerings åtgärden genom att klicka på **Kör fråga** . Ett meddelande om att rollen har skapats bekräftar att rollen för ditt Amazon Web Services-program är klar.

    ![Dialog rutan Microsoft Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. När tjänstens huvud namn har uppdaterats med fler roller kan du tilldela användare/grupper till respektive roller. Detta kan göras genom att gå till portalen och navigera till Amazon Web Services programmet. Klicka på fliken **användare och grupper** överst.

1. Vi rekommenderar att du skapar nya grupper för varje AWS-roll så att du kan tilldela den specifika rollen i gruppen. Observera att detta är en till en mappning för en grupp till en roll. Du kan sedan lägga till medlemmar som tillhör den gruppen.

1. När grupperna har skapats väljer du gruppen och tilldelar programmet.

    ![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Kapslade grupper stöds inte när grupper tilldelas grupper.

1. Om du vill tilldela rollen till gruppen väljer du rollen och klickar på knappen **tilldela** längst ned på sidan.

    ![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Observera att du måste uppdatera sessionen i Azure Portal för att se nya roller.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Web Services (AWS) i åtkomst panelen bör du hämta Amazon Web Services (AWS)-program sidan med möjlighet att välja rollen.

![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Du kan också kontrol lera SAML-svaret för att se vilka roller som skickas som anspråk.

![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Konfigurera etablering med MS Graph API: er](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [Skydda Amazon Web Services (AWS) med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-aws)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/