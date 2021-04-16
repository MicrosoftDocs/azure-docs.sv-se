---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med AWS Single-Account Access | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AWS Single-Account Access.
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
ms.openlocfilehash: eb469c757e2898a9925dd7d3358cfe95734cb2e9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Självstudie: Azure Active Directory med enkel inloggning (SSO) med AWS Single-Account Access

I den här självstudien lär du dig att integrera AWS Single-Account Access med Azure Active Directory (Azure AD). När du integrerar AWS Single-Account Access med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till AWS Single-Account Access.
* Gör så att dina användare automatiskt loggas in på AWS Single-Account Access med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Förstå de olika AWS-programmen i Azure AD-programgalleriet
Använd informationen nedan för att fatta ett beslut mellan att använda AWS Single Sign-On och AWS Single-Account Access-program i Azure AD-programgalleriet.

**Enkel inloggning för AWS**

[Enkel inloggning för AWS](./aws-single-sign-on-tutorial.md) lades till i Azure AD-programgalleriet i februari 2021. Det gör det enkelt att hantera åtkomst centralt till flera AWS-konton och AWS-program, med inloggning via Microsoft Azure AD. Federera Microsoft Azure AD med AWS SSO en gång och använd AWS SSO för att hantera behörigheter för alla dina AWS-konton från en och samma plats. AWS SSO tillser behörigheter automatiskt och håller dem aktuella när du uppdaterar principer och åtkomsttilldelningar. Slutanvändare kan autentisera med sina Azure AD-autentiseringsuppgifter för att få åtkomst till AWS-konsolen, kommandoradsgränssnittet och AWS SSO-integrerade program.

**AWS Single-Account Access**

[AWS Single-Account Access]() har använts av kunder under de senaste åren och gör att du kan federera Azure AD till ett enda AWS-konto och använda Azure AD för att hantera åtkomst till AWS IAM-roller. AWS IAM-administratörer definierar roller och principer i varje AWS-konto. För varje AWS-konto federerar Azure AD-administratörer till AWS IAM, tilldelar användare eller grupper till kontot och konfigurerar Azure AD för att skicka försäkran som auktoriserar rollåtkomst.  

| Funktion | Enkel AWS-Sign-On | AWS Single-Account Access |
|:--- |:---:|:---:|
|Villkorlig åtkomst| Stöder en princip för villkorlig åtkomst för alla AWS-konton. | Stöder en princip för villkorlig åtkomst för alla konton eller anpassade principer per konto|
| CLI-åtkomst | Stöds | Stöds|
| Privileged Identity Management | Stöds inte ännu | Stöds inte ännu |
| Centralisera kontohantering | Centralisera kontohantering i AWS. | Centralisera kontohantering i Azure AD (kräver troligen ett Azure AD-företagsprogram per konto). |
| SAML-certifikat| Enskilt certifikat| Separata certifikat per app/konto | 

## <a name="aws-single-account-access-architecture"></a>AWS Single-Account Access-arkitektur
![Diagram över Azure AD- och AWS-relation](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Du kan konfigurera flera identifierare för flera instanser. Exempel:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Med dessa värden tar Azure AD bort värdet för och skickar rätt värde som **#** `https://signin.aws.amazon.com/saml` målgrupps-URL i SAML-token.

Vi rekommenderar den här metoden av följande skäl:

- Varje program ger dig ett unikt X509-certifikat. Varje instans av en AWS-appinstans kan sedan ha olika förfallodatum för certifikat, som kan hanteras på ett enskilt AWS-konto. I det här fallet är det enklare att distribuera övergripande certifikat.

- Du kan aktivera användareablering med en AWS-app i Azure AD, och sedan hämtar vår tjänst alla roller från det AWS-kontot. Du behöver inte lägga till eller uppdatera AWS-rollerna i appen manuellt.

- Du kan tilldela appens ägare individuellt för appen. Den här personen kan hantera appen direkt i Azure AD.

> [!Note]
> Se till att endast använda ett galleriprogram.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* En AWS-prenumeration med enkel inloggning (SSO).

> [!Note]
> Roller bör inte redigeras manuellt i Azure AD när du utför rollimporter.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* AWS Single-Account Access stöder **SP- och IDP-initierad** enkel inloggning.

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>Lägga till AWS Single-Account Access från galleriet

För att konfigurera integreringen av AWS Single-Account Access i Azure AD måste du lägga till AWS Single-Account Access från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbetskonto, skolkonto eller personligt Microsoft-konto.
1. I Azure Portal du efter och väljer **Azure Active Directory**.
1. I den Azure Active Directory översiktsmenyn väljer **du Företagsprogram**  >  **Alla program**.
1. Välj **Nytt program för** att lägga till ett program.
1. I avsnittet **Lägg till från** galleriet skriver du **AWS Single-Account Access** i sökrutan.
1. Välj **AWS Single-Account Access** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Konfigurera och testa enkel inloggning i Azure AD för AWS Single-Account Access

Konfigurera och testa enkel inloggning i Azure AD med AWS Single-Account Access med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i AWS Single-Account Access.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD Single-Account AWS Single-Account Access:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för AWS Single-Account Access –](#configure-aws-single-account-access-sso)** för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa AWS Single-Account Access-testanvändare](#create-aws-single-account-access-test-user)** – för att ha en motsvarighet för B.Simon i AWS Single-Account Access som är länkad till En Azure AD-representation av användaren.
    1. **[Konfigurera rolletablering i AWS Single-Account Access](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal går du till **sidan för AWS Single-Account Access-programintegrering** och går till avsnittet Hantera och **väljer enkel inloggning.** 
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** uppdaterar du både **Identifierare (entitets-ID)** och **Svars-URL** med samma standardvärde: `https://signin.aws.amazon.com/saml` . Du måste välja **Spara för** att spara konfigurationsändringarna.

1. När du konfigurerar fler än en instans anger du ett identifierarvärde. Från och med andra instansen använder du följande format, inklusive ett **#** -tecken för att ange ett unikt SPN-värde.

    `https://signin.aws.amazon.com/saml#2`

1. AWS-programmet förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig AWS-programmet att några fler attribut skickas tillbaka i SAML-svaret som visas nedan. Dessa attribut är också ifyllda i förväg, men du kan granska dem enligt dina behov.
    
    | Name  | Källattribut  | Namnområde |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Roll | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS förväntar sig roller för användare som har tilldelats till programmet. Konfigurera dessa roller i Azure AD så att användarna kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. På sidan **Konfigurera enkel inloggning med SAML** går du till dialogrutan **SAML-signeringscertifikat** (steg 3) och väljer Lägg till **ett certifikat.**

    ![Skapa nytt SAML-certifikat](common/add-saml-certificate.png)

1. Generera ett nytt SAML-signeringscertifikat och välj sedan **Nytt certifikat.** Ange en e-postadress för certifikatmeddelanden.
   
    ![Nytt SAML-certifikat](common/new-saml-certificate.png) 

1. I avsnittet **SAML-signeringscertifikat** hittar  du **XML för federationsmetadata** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](./media/amazon-web-service-tutorial/certificate.png)

1. I **avsnittet Konfigurera AWS Single-Account Access** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure Portal med namnet B.Simon.

1. I Azure Portal du efter och väljer **Azure Active Directory**.
1. I den Azure Active Directory översiktsmenyn väljer **du Användare**  >  **Alla användare**.
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till AWS Single-Account Access.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **AWS Single-Account Access**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-aws-single-account-access-sso"></a>Konfigurera enkel inloggning för AWS Single-Account Access

1. Logga in på AWS-företagswebbplatsen som administratör i ett annat webbläsarfönster.

2. Välj **AWS Home**.

    ![Skärmbild av AWS-företagswebbplatsen med AWS-startikonen markerad][11]

3. Välj **Identitets- och åtkomsthantering.**

    ![Skärmbild av sidan AWS-tjänster med IAM markerat][12]

4. Välj **Identitetsproviders**  >  **Skapa provider.**

    ![Skärmbild av sidan IAM med Identitetsproviders och Skapa provider markerade][13]

5. På sidan **Konfigurera provider** utför du följande steg:

    ![Skärmbild av konfigurera provider][14]

    a. För **Providertyp** väljer du **SAML**.

    b. I **Providernamn** anger du ett providernamn (till exempel *WAAD*).

    c. Om du vill ladda upp **den nedladdade metadatafilen** från Azure Portal väljer du **Välj fil**.

    d. Välj **Nästa steg.**

6. På sidan **Verifiera providerinformation** väljer du **Skapa**.

    ![Skärmbild av Verifiera providerinformation med Skapa markerat][15]

7. Välj **Roller**  >  **Skapa roll.**

    ![Skärmbild av sidan Roller][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Skärmbild av sidan Skapa roll][19]

    a. Under **Välj typ av betrodd entitet** väljer du **SAML 2.0-federation**.

    b. Under **Choose a SAML 2.0 Provider (Välj en SAML 2.0-provider)** väljer du **den SAML-provider** som du skapade tidigare (till exempel *WAAD*).

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Välj **Nästa: Behörigheter**.

9. I dialogrutan **Attach permissions policies (Koppla** behörighetsprinciper) bifogar du lämplig princip enligt din organisation. Välj sedan **Nästa: Granska**.  

    ![Skärmbild av dialogrutan Anslut behörighetsprincip][33]

10. I **dialogrutan** Granska utför du följande steg:

    ![Skärmbild av dialogrutan Granska][34]

    a. I **Rollnamn** anger du rollnamnet.

    b. I **Rollbeskrivning** anger du beskrivningen.

    c. Välj **Skapa roll.**

    d. Skapa så många roller som behövs och mappa dem till identitetsprovidern.

11. Använd autentiseringsuppgifter för AWS-tjänstkontot för att hämta rollerna från AWS-kontot i Azure AD-användareablering. För detta öppnar du AWS-konsolstarten.

12. Välj **Tjänster.** Under **Säkerhet, Identitetsefterlevnad &** väljer du **IAM**.

    ![Skärmbild av startsidan för AWS-konsolen med Tjänster och IAM markerade](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. I avsnittet IAM väljer du **Principer.**

    ![Skärmbild av IAM-avsnittet med Principer markerat](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Skapa en ny princip genom att **välja Skapa princip** för att hämta rollerna från AWS-kontot i Azure AD-användareablering.

    ![Skärmbild av sidan Skapa roll med Skapa princip markerat](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Skapa en egen princip för att hämta alla roller från AWS-konton.

    ![Skärmbild av sidan Skapa princip med JSON markerat](./media/amazon-web-service-tutorial/policy1.png)

    a. I **Skapa princip** väljer du fliken **JSON.**

    b. Lägg till följande JSON i principdokumentet:

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

    c. Välj **Granska princip** för att verifiera principen.

    ![Skärmbild av sidan Skapa princip](./media/amazon-web-service-tutorial/policy5.png)

16. Definiera den nya principen.

    ![Skärmbild av sidan Skapa princip med fälten Namn och Beskrivning markerade](./media/amazon-web-service-tutorial/policy2.png)

    a. I **Namn** anger du **AzureAD_SSOUserRole_Policy**.

    b. I **Beskrivning** anger du **Den här principen tillåter att rollerna hämtas från AWS-konton.**

    c. Välj **Skapa princip**.

17. Skapa ett nytt användarkonto i AWS IAM-tjänsten.

    a. I AWS IAM-konsolen väljer du **Användare**.

    ![Skärmbild av AWS IAM-konsolen med Användare markerat](./media/amazon-web-service-tutorial/policy3.png)

    b. Om du vill skapa en ny användare väljer du **Lägg till användare**.

    ![Skärmbild av knappen Lägg till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I avsnittet **Lägg till** användare:

    ![Skärmbild av sidan Lägg till användare med Användarnamn och Åtkomsttyp markerat](./media/amazon-web-service-tutorial/adduser1.png)

    * Ange användarnamnet som **AzureADRoleManager**.

    * Som åtkomsttyp väljer du **Programmatisk åtkomst.** På så sätt kan användaren anropa API:erna och hämta rollerna från AWS-kontot.

    * Välj **Nästa behörigheter.**

18. Skapa en ny princip för den här användaren.

    ![Skärmbild som visar sidan Lägg till användare där du kan skapa en princip för användaren.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Välj **Bifoga befintliga principer direkt.**

    b. Sök efter den nyligen skapade principen i filteravsnittet **AzureAD_SSOUserRole_Policy**.

    c. Välj principen och välj sedan **Nästa: Granska**.

19. Granska principen till den anslutna användaren.

    ![Skärmbild av sidan Lägg till användare med Skapa användare markerat](./media/amazon-web-service-tutorial/adduser3.png)

    a. Granska användarnamnet, åtkomsttypen och den princip som är mappad till användaren.

    b. Välj **Skapa användare.**

20. Ladda ned användarautentiseringsuppgifterna för en användare.

    ![Skärmbild som visar sidan Lägg till användare med knappen Ladda ned c s v för att hämta autentiseringsuppgifter för användaren.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopiera användarens **åtkomstnyckel-ID** och **hemliga åtkomstnyckel**.

    b. Ange dessa autentiseringsuppgifter i avsnittet om azure AD-användareablering för att hämta rollerna från AWS-konsolen.

    c. Välj **Stäng**.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Konfigurera rolletablering i AWS Single-Account Access

1. I Azure AD-hanteringsportalen går du till Etablera i **AWS-appen.**

    ![Skärmbild av AWS-appen med Etablering markerat](./media/amazon-web-service-tutorial/provisioning.png)

2. Ange åtkomstnyckeln och hemligheten **i fälten clientsecret** **respektive Secret Token.**

    ![Skärmbild av dialogrutan Administratörsautentiseringsuppgifter](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Ange AWS-användaråtkomstnyckeln i fältet **clientsecret**.

    b. Ange AWS-användarhemligheten i fältet **Hemlig token**.

    c. Välj **Testanslutning**.

    d. Spara inställningen genom att välja **Spara.**

3. I avsnittet **Inställningar** för **Etableringsstatus** väljer du **På**. Välj sedan **Spara**.

    ![Skärmbild av avsnittet Inställningar med På markerat](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Etableringstjänsten importerar endast roller från AWS till Azure AD. Tjänsten etablerar inte användare och grupper från Azure AD till AWS.

> [!NOTE]
> När du har sparat etableringsautentiseringsuppgifterna måste du vänta tills den första synkroniseringscykeln har körts. Synkroniseringen tar vanligtvis cirka 40 minuter att slutföra. Du kan se statusen längst ned på **sidan Etablering** under **Aktuell status**.

### <a name="create-aws-single-account-access-test-user"></a>Skapa AWS Single-Account Access-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet B.Simon i AWS Single-Account Access. AWS Single-Account Access behöver inte skapa någon användare i sitt system för enkel inloggning, så du behöver inte utföra några åtgärder här.

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till AWS Single-Account för inloggning till åtkomst där du kan initiera inloggningsflödet.  

* Gå till AWS Single-Account åtkomst inloggnings-URL direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** Azure Portal så bör du automatiskt loggas in på AWS Single-Account Access som du har ställt in enkel inloggning för 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på AWS Single-Account Access-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på AWS Single-Account Access som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="known-issues"></a>Kända problem

* AWS Single-Account Access-etableringsintegrering kan endast användas för att ansluta till slutpunkter för offentliga AWS-moln. AWS Single-Account Access-etableringsintegrering kan inte användas för åtkomst till AWS Government-miljöer.
 
* I **avsnittet Etablering** visar **underavsnittet Mappningar** en "Läser in..." och visar aldrig attributmappningarna. Det enda etableringsarbetsflöde som stöds i dag är import av roller från AWS till Azure AD för val under en användar- eller grupptilldelning. Attributmappningarna för detta är förinställda och kan inte konfigureras.

* Avsnittet **Etablering** stöder endast inmatning av en uppsättning autentiseringsuppgifter för en AWS-klientorganisation i taget. Alla importerade roller skrivs till egenskapen `appRoles` för Azure AD-objektet [ `servicePrincipal` för](/graph/api/resources/serviceprincipal) AWS-klientorganisationen.

  Flera AWS-klienter (som representeras `servicePrincipals` av ) kan läggas till i Azure AD från galleriet för etablering. Det finns dock ett känt problem där det inte går att automatiskt skriva alla importerade roller från flera AWS som används för etablering till den enda som används för `servicePrincipals` `servicePrincipal` enkel inloggning.

  Som en tillfällig lösning kan du använda [Microsoft Graph API](/graph/api/resources/serviceprincipal) för att extrahera alla importerade till varje `appRoles` AWS `servicePrincipal` där etablering har konfigurerats. Därefter kan du lägga till rollsträngarna i den AWS `servicePrincipal` där enkel inloggning har konfigurerats.

* Roller måste uppfylla följande krav för att kunna importeras från AWS till Azure AD:

  * Roller måste ha exakt en saml-provider definierad i AWS
  * Den kombinerade längden på ARN (Amazon Resource Name) för rollen och ARN för den associerade saml-providern måste vara mindre än 240 tecken.

## <a name="change-log"></a>Ändringslogg

* 2020-01-12 – Höjd längd på roll från 119 tecken till 239 tecken. 

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat AWS Single-Account Access kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


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