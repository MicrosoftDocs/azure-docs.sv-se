---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Amazon Web Services (AWS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Web Services (AWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/14/2020
ms.author: jeedes
ms.openlocfilehash: 7377c6ea92ea53ca14525938e7522448afac541c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548365"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Amazon Web Services (AWS)

I den här självstudien får du lära dig hur du integrerar Amazon Web Services (AWS) med Azure Active Directory (Azure AD). När du integrerar Amazon Web Services (AWS) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Amazon Web Services (AWS).
* Gör det möjligt för användarna att logga in automatiskt till Amazon Web Services (AWS) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagram över Azure AD-och AWS-relationer](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Du kan konfigurera flera identifierare för flera instanser. Exempel:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Med dessa värden tar Azure AD bort värdet **#** och skickar rätt värde `https://signin.aws.amazon.com/saml` som mål grupps-URL i SAML-token.

Vi rekommenderar den här metoden av följande orsaker:

- Varje program ger dig ett unikt X509-certifikat. Varje instans av en AWS-serverinstans kan sedan ha ett annat förfallo datum för certifikatet, som kan hanteras på ett enskilt AWS-konto. Övergripande certifikat förnyelse är enklare i det här fallet.

- Du kan aktivera användar etablering med en AWS-app i Azure AD och sedan hämtar tjänsten alla roller från det AWS-kontot. Du behöver inte lägga till eller uppdatera AWS-roller manuellt i appen.

- Du kan tilldela program ägaren individuellt för appen. Den här personen kan hantera appen direkt i Azure AD.

> [!Note]
> Se till att du endast använder ett galleri program.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En AWS-aktiverad (SSO)-prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Amazon Web Services (AWS) stöder **SP- och IDP**-initierad enkel inloggning
* När du har konfigurerat Amazon Web Services (AWS) kan du framtvinga kontroll av sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets konto, skol konto eller personliga Microsoft-konto.
1. I Azure Portal söker du efter och väljer **Azure Active Directory**.
1. I menyn Azure Active Directory översikt väljer du **företags program**  >  **alla program**.
1. Välj **nytt program** för att lägga till ett program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Amazon Web Services (AWS)** i sökrutan.
1. Välj **Amazon Web Services (AWS)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-amazon-web-services-aws"></a>Konfigurera och testa Azure AD SSO för Amazon Web Services (AWS)

Konfigurera och testa Azure AD SSO med Amazon Web Services (AWS) med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Amazon Web Services (AWS).

Om du vill konfigurera och testa Azure AD SSO med Amazon Web Services (AWS) slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Amazon Web Services (AWS) SSO](#configure-amazon-web-services-aws-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Amazon Web Services (AWS)-test användare](#create-amazon-web-services-aws-test-user)** – om du vill ha en motsvarighet till B. Simon i Amazon Web Services (AWS) som är länkad till Azure AD-representation av användare.
    1. **[Konfigurera roll etablering i Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **Amazon Web Services (AWS)** program integration i [Azure Portal](https://portal.azure.com/), letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** uppdaterar du båda **identifieraren (entitets-ID)** och **svars-URL** med samma standardvärde: `https://signin.aws.amazon.com/saml` . Du måste välja **Spara** för att spara konfigurations ändringarna.

1. Ange ett ID-värde när du konfigurerar fler än en instans. Använd följande format från den andra instansen, inklusive ett **#** tecken för att ange ett unikt SPN-värde.

    `https://signin.aws.amazon.com/saml#2`

1. AWS-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig AWS-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name  | Källattribut  | Namnområde |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Roll            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration             | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  `https://aws.amazon.com/SAML/Attributes` |

1. På sidan **Konfigurera enkel inloggning med SAML** , i dialog rutan **SAML-signeringscertifikat** (steg 3), väljer du **Lägg till ett certifikat**.

    ![Skapa nytt SAML-certifikat](common/add-saml-certificate.png)

1. Generera ett nytt certifikat för SAML-signering och välj sedan **nytt certifikat**. Ange en e-postadress för certifikat aviseringar.
   
    ![Nytt SAML-certifikat](common/new-saml-certificate.png) 

1. I avsnittet **SAML-signeringscertifikat** kan du hitta **XML för federationsmetadata** och välja **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](./media/amazon-web-service-tutorial/certificate.png)

1. I avsnittet **konfigurera Amazon Web Services (AWS)** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I Azure Portal söker du efter och väljer **Azure Active Directory**.
1. I menyn Azure Active Directory översikt väljer **du användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Amazon Web Services (AWS).

1. I Azure Portal söker du efter och väljer **Azure Active Directory**.
1. I menyn Azure Active Directory översikt väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **Amazon Web Services (AWS)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-amazon-web-services-aws-sso"></a>Konfigurera Amazon Web Services (AWS) SSO

1. Logga in på din AWS företags webbplats som administratör i ett annat webbläsarfönster.

2. Välj **AWS Home**.

    ![Skärm bild av AWS företags webbplats med AWS start ikon markerad][11]

3. Välj **identitets-och åtkomst hantering**.

    ![Skärm bild av sidan AWS Services med IAM markerat][12]

4. Välj **identitets leverantörer**  >  **skapa Provider**.

    ![Skärm bild av IAM-sidan med identitets leverantörer och skapa Provider markerad][13]

5. Utför följande steg på sidan **Konfigurera Provider** :

    ![Skärm bild av konfigurera Provider][14]

    a. För **typ av Provider**väljer du **SAML**.

    b. Ange ett providernamn (till exempel: *WAAD*) för **leverantörs namn**.

    c. Om du vill ladda upp den hämtade **metadatafilen** från Azure Portal väljer du **Välj fil**.

    d. Välj **Nästa steg**.

6. På sidan **Verifiera leverantörs information** väljer du **skapa**.

    ![Skärm bild av verifiera leverantörs information med skapa markerat][15]

7. Välj **roller**  >  **skapa roll**.

    ![Skärm bild av roll Sidan][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Skärm bild av sidan Skapa roll][19]

    a. Under **Välj typ av betrodd entitet**väljer du **SAML 2,0 Federation**.

    b. Under **Välj en saml 2,0-Provider**väljer du den **SAML-Provider** som du skapade tidigare (till exempel: *WAAD*).

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Välj **Nästa: Behörigheter**.

9. I dialog rutan **bifoga behörighets principer** bifogar du lämplig princip per organisation. Välj sedan **Nästa: granska**.  

    ![Skärm bild av dialog rutan bifoga behörighets princip][33]

10. I dialog rutan **Granska** utför du följande steg:

    ![Skärm bild av dialog rutan granska][34]

    a. I **roll namn**anger du ditt roll namn.

    b. Ange beskrivningen i **roll Beskrivning**.

    c. Välj **skapa roll**.

    d. Skapa så många roller som du behöver och mappa dem till identitets leverantören.

11. Använd AWS tjänst konto uppgifter för att hämta rollerna från AWS-kontot i användar etableringen för Azure AD. För detta öppnar du AWS-konsolstarten.

12. Välj **tjänster**. Under **säkerhet, identitet & efterlevnad**väljer du **IAM**.

    ![Skärm bild av AWS-konsolens start sida med tjänster och IAM markerat](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. I avsnittet IAM väljer du **principer**.

    ![Skärm bild av IAM-avsnittet med principer markerade](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Skapa en ny princip genom att välja **Skapa princip** för att hämta rollerna från AWS-kontot i användar etableringen för Azure AD.

    ![Skärm bild av sidan Skapa roll med skapa princip markerad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Skapa en egen princip för att hämta alla roller från AWS-konton.

    ![Skärm bild av sidan Skapa princip med JSON markerat](./media/amazon-web-service-tutorial/policy1.png)

    a. I **Skapa princip**väljer du fliken **JSON** .

    b. I princip dokumentet lägger du till följande JSON:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Välj **Granska princip** för att validera principen.

    ![Skärm bild av sidan Skapa princip](./media/amazon-web-service-tutorial/policy5.png)

16. Definiera den nya principen.

    ![Skärm bild av sidan Skapa princip med namn-och beskrivnings fälten markerade](./media/amazon-web-service-tutorial/policy2.png)

    a. I **namn**anger du **AzureAD_SSOUserRole_Policy**.

    b. I **Beskrivning**anger **du den här principen för att hämta ROLLerna från AWS-konton**.

    c. Välj **Skapa princip**.

17. Skapa ett nytt användar konto i AWS IAM-tjänsten.

    a. I AWS IAM-konsolen väljer **du användare**.

    ![Skärm bild av AWS IAM-konsolen med användare markerade](./media/amazon-web-service-tutorial/policy3.png)

    b. Om du vill skapa en ny användare väljer du **Lägg till användare**.

    ![Skärm bild av knappen Lägg till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I avsnittet **Lägg till användare** :

    ![Skärm bild av sidan Lägg till användare med användar namn och åtkomst typ markerad](./media/amazon-web-service-tutorial/adduser1.png)

    * Ange användarnamnet som **AzureADRoleManager**.

    * För åtkomst typen väljer du **programmerings åtkomst**. På så sätt kan användaren anropa API: erna och hämta rollerna från AWS-kontot.

    * Välj **Nästa behörigheter**.

18. Skapa en ny princip för den här användaren.

    ![Skärm bild av Lägg till användare](./media/amazon-web-service-tutorial/adduser2.png)

    a. Välj **koppla befintliga principer direkt**.

    b. Sök efter den nyligen skapade principen i filteravsnittet **AzureAD_SSOUserRole_Policy**.

    c. Välj principen och välj sedan **Nästa: granska**.

19. Granska principen för den anslutna användaren.

    ![Skärm bild av sidan Lägg till användare med skapa användare markerad](./media/amazon-web-service-tutorial/adduser3.png)

    a. Granska användarnamnet, åtkomsttypen och den princip som är mappad till användaren.

    b. Välj **skapa användare**.

20. Hämta användarens autentiseringsuppgifter.

    ![Skärm bild av Lägg till användare](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopiera användarens **åtkomstnyckel-ID** och **hemliga åtkomstnyckel**.

    b. Ange autentiseringsuppgifterna i avsnittet användar etablering i Azure AD för att hämta rollerna från AWS-konsolen.

    c. Välj **Stäng**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Konfigurera roll etablering i Amazon Web Services (AWS)

1. I AWS-appen i Azure AD-hanteringsportalen går du till **etablering**.

    ![Skärm bild av AWS-appen med etablering markerad](./media/amazon-web-service-tutorial/provisioning.png)

2. Ange åtkomst nyckeln och hemligheten i fälten **clientsecret** och **hemlig token** .

    ![Skärm bild av dialog rutan autentiseringsuppgifter för administratörer](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Ange AWS-användaråtkomstnyckeln i fältet **clientsecret**.

    b. Ange AWS-användarhemligheten i fältet **Hemlig token**.

    c. Välj **test anslutning**.

    d. Spara inställningen genom att välja **Spara**.

3. I avsnittet **Inställningar** , för **etablerings status**, väljer du **på**. Välj sedan **Spara**.

    ![Skärm bild av avsnittet inställningar med på markerad](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Etablerings tjänsten importerar roller enbart från AWS till Azure AD. Tjänsten etablerar inte användare och grupper från Azure AD till AWS.

> [!NOTE]
> När du har sparat autentiseringsuppgifterna för etablering måste du vänta tills den inledande synkroniseringen har körts. Synkronisering tar vanligt vis cirka 40 minuter att slutföra. Du kan se statusen längst ned på **etablerings** sidan under **aktuell status**.

### <a name="create-amazon-web-services-aws-test-user"></a>Skapa Amazon Web Services (AWS)-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i Amazon Web Services (AWS). För Amazon Web Services (AWS) krävs det inte att en användare skapas i deras system för enkel inloggning, så du behöver inte göra någonting här.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Amazon Web Services (AWS)-panelen i åtkomstpanelen bör du automatiskt loggas in på Amazon Web Services (AWS) som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Kända problem

 * I avsnittet om **etablering** visas en "inläsning..." i underavsnittet **mappningar** . meddelande, och visar aldrig mappningar för attribut. Det enda etablerings arbets flödet som stöds idag är att importera roller från AWS till Azure AD för val under en användar-eller grupp tilldelning. Attributens mappningar för detta är för bestämda och kan inte konfigureras.

 * Avsnittet **Etablering** stöder endast inmatning av en uppsättning autentiseringsuppgifter för en AWS-klientorganisation i taget. Alla importerade roller skrivs till `appRoles` egenskapen för Azure AD- [ `servicePrincipal` objektet](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) för AWS-klienten.

   Flera AWS-klienter (som representeras av `servicePrincipals` ) kan läggas till i Azure AD från galleriet för etablering. Det finns dock ett känt problem, men det går inte att automatiskt skriva alla importerade roller från de flera AWS `servicePrincipals` som används för etablering i den enda `servicePrincipal` som används för SSO.

   Som en lösning kan du använda [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) för att extrahera alla `appRoles` importerade till varje AWS `servicePrincipal` där etableringen har kon figurer ATS. Du kan senare lägga till de här roll strängarna i AWS `servicePrincipal` där SSO är konfigurerat.

* Roller måste uppfylla följande krav för att kunna importeras från AWS till Azure AD:

  * Roller måste ha exakt en SAML-Provider definierad i AWS

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Amazon Web Services (AWS) med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda Amazon Web Services (AWS) med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-aws)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
