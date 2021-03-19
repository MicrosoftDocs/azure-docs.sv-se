---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med AWS Single-Account åtkomst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AWS Single-Account åtkomst.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: jeedes
ms.openlocfilehash: e390eb62f64c9ec55605525828790f15633e5f14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med AWS Single-Account åtkomst

I den här självstudien får du lära dig hur du integrerar AWS-Single-Account åtkomst med Azure Active Directory (Azure AD). När du integrerar AWS Single-Account åtkomst med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AWS Single-Account åtkomst.
* Gör det möjligt för användarna att logga in automatiskt för att AWS Single-Account åtkomst med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Förstå de olika AWS-programmen i Azure AD-programgalleriet
Använd informationen nedan för att fatta ett beslut mellan att använda AWS-Sign-On och AWS-Single-Account åtkomst program i Azure AD-programgalleriet.

**AWS enkel inloggning**

[AWS för enkel inloggning](./aws-single-sign-on-tutorial.md) lades till i Azure AD-programgalleriet i februari 2021. Det gör det enkelt att hantera åtkomst centralt till flera AWS-konton och AWS-program, med inloggning via Microsoft Azure AD. Federera Microsoft Azure AD med AWS SSO en gång och Använd AWS SSO för att hantera behörigheter för alla dina AWS-konton från en och samma plats. AWS SSO etablerar behörigheter automatiskt och behåller dem aktuella när du uppdaterar principer och åtkomst tilldelningar. Slutanvändare kan autentisera med sina autentiseringsuppgifter för Azure AD för att få åtkomst till AWS-konsolen, kommando rads gränssnittet och AWS SSO-integrerade program.

**AWS Single-Account åtkomst**

[AWS Single-Account åtkomst]() har använts av kunder under de senaste åren och gör det möjligt att federera Azure AD till ett enda AWS-konto och använda Azure AD för att hantera åtkomst till AWS IAM-roller. AWS IAM-administratörer definierar roller och principer i varje AWS-konto. För varje AWS-konto federerar Azure AD-administratörerna till AWS IAM, tilldelar användare eller grupper till kontot och konfigurerar Azure AD för att skicka kontroller som godkänner roll åtkomst.  

| Funktion | AWS enkla Sign-On | AWS Single-Account åtkomst |
|:--- |:---:|:---:|
|Villkorlig åtkomst| Har stöd för en enda princip för villkorlig åtkomst för alla AWS-konton. | Har stöd för en enda princip för villkorlig åtkomst för alla konton eller anpassade principer per konto|
| CLI-åtkomst | Stöds | Stöds|
| Privileged Identity Management | Stöds inte ännu | Stöds inte ännu |
| Centralisera konto hantering | Centralisera konto hanteringen i AWS. | Centralisera konto hantering i Azure AD (kräver förmodligen ett Azure AD Enterprise-program per konto). |
| SAML-certifikat| Enskilt certifikat| Separata certifikat per app/konto | 

## <a name="aws-single-account-access-architecture"></a>AWS Single-Account åtkomst arkitektur
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

* AWS Single-Account åtkomst stöder **SP-och IDP** -initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>Lägga till AWS Single-Account åtkomst från galleriet

Om du vill konfigurera integreringen av AWS Single-Account åtkomst till Azure AD måste du lägga till AWS Single-Account åtkomst från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets konto, skol konto eller personliga Microsoft-konto.
1. I Azure Portal söker du efter och väljer **Azure Active Directory**.
1. I menyn Azure Active Directory översikt väljer du **företags program**  >  **alla program**.
1. Välj **nytt program** för att lägga till ett program.
1. I avsnittet **Lägg till från galleriet** , skriv **AWS Single-Account åtkomst** i sökrutan.
1. Välj **AWS Single-Account åtkomst** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Konfigurera och testa Azure AD SSO för AWS Single-Account åtkomst

Konfigurera och testa Azure AD SSO med AWS Single-Account åtkomst med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i AWS Single-Account åtkomst.

Utför följande steg för att konfigurera och testa Azure AD SSO med AWS Single-Account åtkomst:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera AWS Single-Account åtkomst till SSO](#configure-aws-single-account-access-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa AWS Single-Account Access test User](#create-aws-single-account-access-test-user)** – om du vill ha en motsvarighet till B. Simon i AWS Single-Account åtkomst som är länkad till Azure AD-representation av användare.
    1. **[Konfigurera roll etablering i AWS Single-Account åtkomst](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **AWS Single-Account åtkomst till** program integration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
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
    | Roll | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS förväntar sig roller för användare som tilldelats programmet. Konfigurera de här rollerna i Azure AD så att användarna kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. På sidan **Konfigurera enkel inloggning med SAML** , i dialog rutan **SAML-signeringscertifikat** (steg 3), väljer du **Lägg till ett certifikat**.

    ![Skapa nytt SAML-certifikat](common/add-saml-certificate.png)

1. Generera ett nytt certifikat för SAML-signering och välj sedan **nytt certifikat**. Ange en e-postadress för certifikat aviseringar.
   
    ![Nytt SAML-certifikat](common/new-saml-certificate.png) 

1. I avsnittet **SAML-signeringscertifikat** kan du hitta **XML för federationsmetadata** och välja **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](./media/amazon-web-service-tutorial/certificate.png)

1. I avsnittet **Konfigurera AWS Single-Account åtkomst** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till AWS Single-Account åtkomst.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **AWS Single-Account Access**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-aws-single-account-access-sso"></a>Konfigurera AWS Single-Account åtkomst till SSO

1. Logga in på din AWS företags webbplats som administratör i ett annat webbläsarfönster.

2. Välj **AWS Home**.

    ![Skärm bild av AWS företags webbplats med AWS start ikon markerad][11]

3. Välj **identitets-och åtkomst hantering**.

    ![Skärm bild av sidan AWS Services med IAM markerat][12]

4. Välj **identitets leverantörer**  >  **skapa Provider**.

    ![Skärm bild av IAM-sidan med identitets leverantörer och skapa Provider markerad][13]

5. Utför följande steg på sidan **Konfigurera Provider** :

    ![Skärm bild av konfigurera Provider][14]

    a. För **typ av Provider** väljer du **SAML**.

    b. Ange ett providernamn (till exempel: *WAAD*) för **leverantörs namn**.

    c. Om du vill ladda upp den hämtade **metadatafilen** från Azure Portal väljer du **Välj fil**.

    d. Välj **Nästa steg**.

6. På sidan **Verifiera leverantörs information** väljer du **skapa**.

    ![Skärm bild av verifiera leverantörs information med skapa markerat][15]

7. Välj **roller**  >  **skapa roll**.

    ![Skärm bild av roll Sidan][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Skärm bild av sidan Skapa roll][19]

    a. Under **Välj typ av betrodd entitet** väljer du **SAML 2,0 Federation**.

    b. Under **Välj en saml 2,0-Provider** väljer du den **SAML-Provider** som du skapade tidigare (till exempel: *WAAD*).

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Välj **Nästa: Behörigheter**.

9. I dialog rutan **bifoga behörighets principer** bifogar du lämplig princip per organisation. Välj sedan **Nästa: granska**.  

    ![Skärm bild av dialog rutan bifoga behörighets princip][33]

10. I dialog rutan **Granska** utför du följande steg:

    ![Skärm bild av dialog rutan granska][34]

    a. I **roll namn** anger du ditt roll namn.

    b. Ange beskrivningen i **roll Beskrivning**.

    c. Välj **skapa roll**.

    d. Skapa så många roller som du behöver och mappa dem till identitets leverantören.

11. Använd AWS tjänst konto uppgifter för att hämta rollerna från AWS-kontot i användar etableringen för Azure AD. För detta öppnar du AWS-konsolstarten.

12. Välj **tjänster**. Under **säkerhet, identitet & efterlevnad** väljer du **IAM**.

    ![Skärm bild av AWS-konsolens start sida med tjänster och IAM markerat](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. I avsnittet IAM väljer du **principer**.

    ![Skärm bild av IAM-avsnittet med principer markerade](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Skapa en ny princip genom att välja **Skapa princip** för att hämta rollerna från AWS-kontot i användar etableringen för Azure AD.

    ![Skärm bild av sidan Skapa roll med skapa princip markerad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Skapa en egen princip för att hämta alla roller från AWS-konton.

    ![Skärm bild av sidan Skapa princip med JSON markerat](./media/amazon-web-service-tutorial/policy1.png)

    a. I **Skapa princip** väljer du fliken **JSON** .

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

    a. I **namn** anger du **AzureAD_SSOUserRole_Policy**.

    b. I **Beskrivning** anger **du den här principen för att hämta ROLLerna från AWS-konton**.

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

    ![Skärm bild som visar sidan Lägg till användare där du kan skapa en princip för användaren.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Välj **koppla befintliga principer direkt**.

    b. Sök efter den nyligen skapade principen i filteravsnittet **AzureAD_SSOUserRole_Policy**.

    c. Välj principen och välj sedan **Nästa: granska**.

19. Granska principen för den anslutna användaren.

    ![Skärm bild av sidan Lägg till användare med skapa användare markerad](./media/amazon-web-service-tutorial/adduser3.png)

    a. Granska användarnamnet, åtkomsttypen och den princip som är mappad till användaren.

    b. Välj **skapa användare**.

20. Hämta användarens autentiseringsuppgifter.

    ![Skärm bild som visar sidan Lägg till användare med en Hämta c s v-knapp för att hämta användarautentiseringsuppgifter.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopiera användarens **åtkomstnyckel-ID** och **hemliga åtkomstnyckel**.

    b. Ange autentiseringsuppgifterna i avsnittet användar etablering i Azure AD för att hämta rollerna från AWS-konsolen.

    c. Välj **Stäng**.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Konfigurera roll etablering i AWS Single-Account åtkomst

1. I AWS-appen i Azure AD-hanteringsportalen går du till **etablering**.

    ![Skärm bild av AWS-appen med etablering markerad](./media/amazon-web-service-tutorial/provisioning.png)

2. Ange åtkomst nyckeln och hemligheten i fälten **clientsecret** och **hemlig token** .

    ![Skärm bild av dialog rutan autentiseringsuppgifter för administratörer](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Ange AWS-användaråtkomstnyckeln i fältet **clientsecret**.

    b. Ange AWS-användarhemligheten i fältet **Hemlig token**.

    c. Välj **Testanslutning**.

    d. Spara inställningen genom att välja **Spara**.

3. I avsnittet **Inställningar** , för **etablerings status**, väljer du **på**. Välj sedan **Spara**.

    ![Skärm bild av avsnittet inställningar med på markerad](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Etablerings tjänsten importerar roller enbart från AWS till Azure AD. Tjänsten etablerar inte användare och grupper från Azure AD till AWS.

> [!NOTE]
> När du har sparat autentiseringsuppgifterna för etablering måste du vänta tills den inledande synkroniseringen har körts. Synkronisering tar vanligt vis cirka 40 minuter att slutföra. Du kan se statusen längst ned på **etablerings** sidan under **aktuell status**.

### <a name="create-aws-single-account-access-test-user"></a>Skapa AWS Single-Account åtkomst test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i AWS Single-Account åtkomst. AWS Single-Account Access behöver inte ha någon användare som kan skapas i systemet för SSO, så du behöver inte utföra några åtgärder här.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till AWS Single-Account åtkomst inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till AWS Single-Account åtkomst inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så ska du vara inloggad automatiskt på den AWS Single-Account åtkomst som du ställer in i SSO 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på AWS Single-Account åtkomst panelen i Mina appar, om den har kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på AWS Single-Account åtkomst som du konfigurerar SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="known-issues"></a>Kända problem

* AWS Single-Account åtkomst etablerings integrering kan endast användas för att ansluta till AWS offentliga moln slut punkter. AWS Single-Account åtkomst etablerings integrering kan inte användas för att få åtkomst till AWS myndighets miljöer.
 
* I avsnittet om **etablering** visas en "inläsning..." i underavsnittet **mappningar** . meddelande, och visar aldrig mappningar för attribut. Det enda etablerings arbets flödet som stöds idag är att importera roller från AWS till Azure AD för val under en användar-eller grupp tilldelning. Attributens mappningar för detta är för bestämda och kan inte konfigureras.

* Avsnittet **Etablering** stöder endast inmatning av en uppsättning autentiseringsuppgifter för en AWS-klientorganisation i taget. Alla importerade roller skrivs till `appRoles` egenskapen för Azure AD- [ `servicePrincipal` objektet](/graph/api/resources/serviceprincipal?view=graph-rest-beta) för AWS-klienten.

  Flera AWS-klienter (som representeras av `servicePrincipals` ) kan läggas till i Azure AD från galleriet för etablering. Det finns dock ett känt problem, men det går inte att automatiskt skriva alla importerade roller från de flera AWS `servicePrincipals` som används för etablering i den enda `servicePrincipal` som används för SSO.

  Som en lösning kan du använda [Microsoft Graph API](/graph/api/resources/serviceprincipal?view=graph-rest-beta) för att extrahera alla `appRoles` importerade till varje AWS `servicePrincipal` där etableringen har kon figurer ATS. Du kan senare lägga till de här roll strängarna i AWS `servicePrincipal` där SSO är konfigurerat.

* Roller måste uppfylla följande krav för att kunna importeras från AWS till Azure AD:

  * Roller måste ha exakt en SAML-Provider definierad i AWS
  * Den kombinerade längden på ARN (Amazon Resource Name) för rollen och ARN för den associerade SAML-providern måste vara kortare än 240 tecken.

## <a name="change-log"></a>Ändringslogg

* 01/12/2020 – ökad roll längds gräns från 119 tecken till 239 tecken. 

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat AWS Single-Account åtkomst kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


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