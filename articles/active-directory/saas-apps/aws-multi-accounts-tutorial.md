---
title: 'Självstudie: Azure Active Directory integration med Amazon Web Services för att ansluta flera konton | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure AD och Amazon Web Services (tidigare självstudier).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384120"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Självstudie: Azure Active Directory integration med Amazon Web Services

I den här självstudien får du lära dig att integrera Azure Active Directory (Azure AD) med Amazon Web Services (AWS) (tidigare självstudier).

Den här integrationen ger följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AWS.
- Du kan göra det möjligt för användarna att automatiskt logga in på AWS med hjälp av enkel inloggning (SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

![Diagram över Azure AD-integrering med AWS.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Vi rekommenderar att du *inte* ansluter en AWS-app till alla dina AWS-konton. I stället rekommenderar vi att du använder [Azure AD SSO-integrering med AWS](./amazon-web-service-tutorial.md) för att konfigurera flera instanser av ditt AWS-konto till flera instanser av AWS-appar i Azure AD. 

Vi rekommenderar att du *inte* ansluter en AWS-app till alla dina AWS-konton av följande orsaker:

* Använd bara den här metoden om du har ett litet antal AWS-konton och-roller, eftersom den här modellen inte är skalbar som antalet AWS-konton och rollerna i dem ökar. Metoden använder inte AWS-funktionerna för roll import med Azure AD-etableringen, så du måste manuellt lägga till, uppdatera eller ta bort roller. 

* Du måste använda metoden Microsoft Graph Explorer för att uppdatera alla roller i appen. Vi rekommenderar inte att du använder manifest fil metoden.

* Kunder rapporterar att efter att ha lagt till ~ 1 200-app-roller för en enda AWS-app, börjar alla ytterligare åtgärder i appen att tillämpa felen som rör storleken. Det finns en gräns för hård storlek på programobjektet.

* Du måste uppdatera rollerna manuellt när de läggs till i något av kontona. Detta är tyvärr en *ersättnings* Metod, inte en metod för att *lägga till* . Om ditt konto nummer växer blir detta också en *n* &times; *n* -relation med konton och roller.

* Alla AWS-konton använder samma XML-fil för federationsmetadata. Vid tidpunkten för certifikat förnyelse kan det vara en enorm övning att uppdatera certifikatet på alla AWS-konton samtidigt.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med AWS behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du få en [utvärderings period](https://azure.microsoft.com/pricing/free-trial/)på en månad.
* En AWS SSO-aktiverad prenumeration.

> [!NOTE]
> Vi rekommenderar inte att du testar stegen i den här självstudien i en produktions miljö om det inte är nödvändigt.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

AWS stöder SP-initierad och IDP-initierad SSO.

## <a name="add-aws-from-the-gallery"></a>Lägg till AWS från galleriet

Om du vill konfigurera integreringen av AWS i Azure AD lägger du till AWS från galleriet till din lista över hanterade program som en tjänst (SaaS).

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du den Azure AD-tjänst som du vill arbeta med.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett program.
1. I avsnittet **Lägg till från galleriet** skriver du **Amazon Web Services** i sökrutan.
1. I listan resultat väljer du **Amazon Web Services** och lägger sedan till appen. Om några sekunder läggs appen till i din klient organisation.

1. Gå till rutan **Egenskaper** och kopiera sedan värdet som visas i rutan **objekt-ID** .

    ![Skärm bild av rutan objekt-ID i fönstret Egenskaper.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med AWS baserat på en test användare som kallas "Britta Simon."

För att enkel inloggning ska fungera måste Azure AD veta vad motsvarande användare i AWS är till Azure AD-användaren. Med andra ord måste du upprätta en länk relation mellan Azure AD-användaren och samma användare i AWS.

I AWS tilldelar du värdet för **användar namnet** i Azure AD som värdet för AWS- **användarnamnet** för att upprätta länk relationen.

Gör följande för att konfigurera och testa enkel inloggning med AWS i Azure AD:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
1. **[Konfigurera AWS SSO](#configure-aws-sso)** för att konfigurera SSO-inställningar på program sidan.
1. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du Azure AD SSO i Azure Portal och konfigurerar SSO i AWS-programmet genom att göra följande:

1. I Azure Portal i den vänstra rutan på sidan **Amazon Web Services (AWS)** program integration väljer du **enkel inloggning**.

    ![Skärm bild av kommandot "enkel inloggning".](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Skärm bild av fönstret "Välj en enkel inloggnings metod".](common/select-saml-option.png)

1. Välj knappen **Redigera** (Penn ikonen) i fönstret **Konfigurera enskilda Sign-On med SAML** .

    ![Skärm bild av knappen Redigera i fönstret konfigurera enskilda Sign-On med SAML.](common/edit-urls.png)

1. Fönstret **grundläggande SAML-konfiguration** öppnas. Hoppa över det här avsnittet eftersom appen är Förintegrerad med Azure. Välj **Spara**.

   AWS-programmet förväntar sig SAML-intyg i ett särskilt format. Du kan hantera värdena för de här attributen från avsnittet **användarattribut &-anspråk** på sidan **program integration** . 
   
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du knappen **Redigera** .

    ![Skärm bild av knappen Redigera i fönstret användarattribut.](common/edit-attribute.png)

1. I avsnittet **användar anspråk** **i fönstret användarattribut konfigurerar du attributet** SAML-token med hjälp av värdena i följande tabell:

    | Name  | Källattribut  | Namnområde |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Roll | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "Ange ett värde från 900 sekunder (15 minuter) till 43200 sekunder (12 timmar)" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. Välj **Lägg till nytt anspråk** och gör sedan följande i fönstret **hantera användar anspråk** :

   ![Skärm bild av knapparna "Lägg till nytt anspråk" och "Spara" i fönstret "användar anspråk".](common/new-save-attribute.png)

   ![Skärm bild av fönstret "hantera användar anspråk".](common/new-attribute-details.png)

   b. I rutan **namn** anger du attributnamnet.  

   c. Ange namn områdets värde i rutan **namn område** .

   d. För **källan** väljer du **attribut**.

   e. I list rutan **källattribut** väljer du attributet.

   f. Välj **OK** och välj sedan **Spara**.

   >[!NOTE]
   >Mer information om roller i Azure AD finns i [lägga till app-roller i programmet och ta emot dem i token](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. På sidan **Konfigurera enskilda Sign-On med SAML** , i avsnittet SAML- **signeringscertifikat** , väljer du **Hämta** för att ladda ned XML-filen för federationsmetadata och sparar den sedan på datorn.

   ![Skärm bild av nedladdnings länken för "federationsmetadata" XML "i fönstret" SAML signerings certifikat ".](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Konfigurera AWS SSO

1. Logga in på din AWS företags webbplats som administratör i ett nytt webbläsarfönster.

1. Välj Start ikonen för **AWS** .

   ![Skärm bild av ikonen "AWS Home".][11]

1. I fönstret **AWS Services** , under **säkerhet, identitet & regelefterlevnad**, väljer du **IAM (identitets & åtkomst hantering)**.

    ![Skärm bild av länken "identitets-och åtkomst hantering" i fönstret "AWS Services".][12]

1. Välj **identitets leverantörer** i den vänstra rutan och välj sedan **skapa Provider**.

    ![Skärm bild av knappen "skapa Provider".][13]

1. I fönstret **Konfigurera Provider** gör du följande:

    ![Skärm bild av fönstret "Configure Provider".][14]

    a. I list rutan **providertyp** väljer du **SAML**.

    b. I rutan **Providernamn** anger du ett namn för providern (till exempel. *WAAD*).

    c. Bredvid dokument rutan **metadata** väljer du **Välj fil** för att ladda upp den hämtade XML-filen för federationsmetadata till Azure Portal.

    d. Välj **Nästa steg**.

1. I fönstret **Verifiera leverantörs information** väljer du **skapa**.

    ![Skärm bild av fönstret "kontrol lera leverantörs information".][15]

1. Välj **roller** i den vänstra rutan och välj sedan **skapa roll**.

    ![Skärm bild av knappen "skapa roll" i fönstret roller.][16]

    > [!NOTE]
    > Den kombinerade längden på roll Amazon-resursens namn (ARN) och SAML-providerns ARN för en roll som importeras måste vara 240 eller färre tecken.

1. Gör följande på sidan **skapa roll** :  

    ![Skärm bild av knappen "SAML 2,0 Federation" betrodd entitet på sidan "skapa roll".][19]

    a. Under **Välj typ av betrodd entitet** väljer du **SAML 2,0 Federation**.

    b. Under **Välj en saml 2,0-Provider** väljer du den SAML-provider som du skapade tidigare (till exempel *WAAD*)

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).

    d. Välj **Nästa: Behörigheter**.

1. I rutan Sök anger du **Administratörs åtkomst**, markerar kryss rutan **AdministratorAccess** och väljer sedan **Nästa: Taggar**.

    ![Skärm bild av listan "princip namn" med AdministratorAccess-principen vald.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Gör följande i fönstret **Lägg till taggar (valfritt)** :

    ![Skärm bild av fönstret Lägg till taggar (valfritt).](./media/aws-multi-accounts-tutorial/config2.png)

    a. I rutan **nyckel** anger du nyckel namnet (till exempel *Azureadtest*).

    b. I rutan **värde (valfritt)** anger du nyckel värde i följande format: `<accountname-aws-admin>` . Konto namnet får bara innehålla gemena bokstäver.

    c. Välj **Nästa: Granskning**.

1. Gör följande i **gransknings** fönstret:

    ![Skärm bild av rutan granska med rutorna "roll namn" och "roll Beskrivning" markerade.][34]

    a. I rutan **rollnamn** anger du värdet i följande format: `<accountname-aws-admin>` .

    b. I rutan **roll Beskrivning** anger du det värde som du använde för roll namnet.

    c. Välj **skapa roll**.

    d. Skapa så många roller som du behöver och mappa dem till identitets leverantören.

    > [!NOTE]
    > På samma sätt kan du skapa andra roller, till exempel *AccountName-ekonomi-admin*, *AccountName-Read-Only-User*, *AccountName-DevOps-User* eller *AccountName-TPM-User*, var och en med en annan princip som är kopplad till den. Du kan ändra dessa roll principer senare, enligt kraven för varje AWS-konto. Det är en bra idé att behålla samma principer för varje roll i AWS-konton.

1. Se till att anteckna konto-ID: t för AWS-kontot, antingen från Amazon EC2-egenskaps fönstret eller IAM-instrumentpanelen, som visas på följande skärm bild:

    ![Skärm bild som visar var konto-ID visas i fönstret "identitets-och åtkomst hantering".](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Logga in på Azure Portal och gå sedan till **grupper**.

1. Skapa nya grupper med samma namn som de för IAM-roller som du skapade tidigare och anteckna sedan värdet i rutan **objekt-ID** för var och en av de nya grupperna.

    ![Skärm bild av konto informationen för en ny grupp.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Logga ut från det aktuella AWS-kontot och logga sedan in på ett annat konto där du vill konfigurera SSO med Azure AD.

1. När du har skapat alla roller i kontona visas de i listan **roller** för dessa konton.

    ![Skärm bild av listan roller som visar varje Rolls namn, beskrivning och betrodda entiteter.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Du måste först avbilda alla roll ARNs och betrodda entiteter för alla-roller i alla konton. Du måste mappa dem manuellt med Azure AD-programmet. Så här gör du:

1. Välj varje roll för att kopiera dess roll ARN och betrodda enhets värden. Du behöver dem för alla roller som du skapar i Azure AD.

    ![Skärm bild av fönstret Summary för rollens ARNs och betrodda entiteter.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Upprepa föregående steg för alla roller i alla konton och lagra dem sedan i en textfil i följande format: `<Role ARN>,<Trusted entities>` .

1. Öppna [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)och gör följande:

    a. Logga in på Microsoft Graph Explorer-webbplatsen med autentiseringsuppgifterna för global administratör eller medadministratör för din klient organisation.

    b. Du måste ha tillräcklig behörighet för att skapa roller. Välj **ändra behörigheter**.

      ![Skärm bild av länken "ändra behörigheter" i verifierings fönstret i Microsoft Graph Explorer.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. I listan behörigheter, om du inte redan har de behörigheter som visas i följande skärm bild, markerar du var och en och väljer sedan **ändra behörigheter**. 

      ![Skärm bild av listan över Microsoft Graph Explorer-behörigheter med lämpliga behörigheter markerade.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Logga in i Graph Explorer igen och godkänn användnings villkoren för webbplatsen. 

    e. Längst upp i fönstret väljer du **Hämta** för metoden, Välj **beta** version för versionen och ange sedan något av följande i rutan fråga: 
    
    * Använd om du vill hämta alla tjänst huvud namn från din klient `https://graph.microsoft.com/beta/servicePrincipals` . 
    * Om du använder flera kataloger använder du `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` , som innehåller din primära domän.

    ![Skärm bild av fönstret Microsoft Graph Explorer-fråga om begär ande text.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. I listan över tjänstens huvud namn, hämtar du den som du behöver ändra. 
    
    Du kan också söka i programmet efter alla angivna tjänst huvud namn genom att välja Ctrl + F. Om du vill hämta ett särskilt huvud namn för tjänsten tar du med i frågan det objekt-ID för tjänstens huvud namn som du kopierade tidigare från fönstret Egenskaper för Azure AD, som du ser här:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Skärm bild som visar en tjänst huvud fråga som innehåller objekt-ID.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    ex. Extrahera egenskapen appRoles från objektet tjänstens huvud namn.

    ![Skärm bild av koden för att extrahera egenskapen appRoles från objektet tjänstens huvud namn.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Du måste nu generera nya roller för ditt program. 

    i. Följande JSON-kod är ett exempel på ett appRoles-objekt. Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program.

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
    > Du kan bara lägga till nya roller när du har lagt till *msiam_access* för korrigerings åtgärden. Du kan också lägga till så många roller du vill, beroende på organisationens behov. Azure AD skickar *värdet* för dessa roller som anspråks värde i SAML-svaret.

    j. Ändra metoden från **gå** till **patch** i Microsoft Graph Explorer. Korrigera tjänstens huvud namns objekt med de roller du vill ha genom att uppdatera egenskapen appRoles, som den som visas i föregående exempel. Välj **Kör fråga** för att köra korrigerings åtgärden. Ett meddelande om att rollen för ditt AWS-program har skapats bekräftar att rollen är klar.

      ![Skärm bild av fönstret Microsoft Graph Explorer med metoden ändrades till PATCH.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. När tjänstens huvud namn har uppdaterats med fler roller kan du tilldela användare och grupper till sina respektive roller. Du gör detta i Azure Portal genom att gå till AWS-programmet och sedan välja fliken **användare och grupper** överst.

1. Vi rekommenderar att du skapar en ny grupp för varje AWS-roll så att du kan tilldela en viss roll i gruppen. Den här mappningen innebär att en grupp tilldelas till en roll. Du kan sedan lägga till medlemmar som tillhör den gruppen.

1. När du har skapat grupperna väljer du gruppen och tilldelar den till programmet.

    ![Skärm bild av fönstret "användare och grupper".](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Kapslade grupper stöds inte när du tilldelar grupper.

1. Om du vill tilldela rollen till gruppen väljer du rollen och väljer sedan **tilldela**.

    ![Skärm bild av fönstret Lägg till tilldelning.](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > När du har tilldelat rollerna kan du visa dem genom att uppdatera Azure Portal-sessionen.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av Microsoft Mina appar.

När du väljer panelen **AWS** i Mina appar öppnas sidan AWS program med ett alternativ för att välja rollen.

![Skärm bild av AWS-sidan för att testa SSO.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Du kan också kontrol lera SAML-svaret för att se vilka roller som skickas som anspråk.

![Skärm bild av SAML-svaret.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Mer information om Mina appar finns i [Logga in och starta appar från portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat AWS kan du framtvinga sessionshantering, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

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
