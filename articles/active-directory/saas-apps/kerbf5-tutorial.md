---
title: 'Självstudie: integrering med enkel inloggning i Azure AD med F5 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: b48b8c1282f90788846b9dc3b64a5ea1ee018a3c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88536010"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör det möjligt för användarna att logga in automatiskt på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

* F5-aktiverad prenumeration med enkel inloggning (SSO).

* Distribution av den gemensamma lösningen kräver följande licens:
    * F5 BIG-IP- &reg; bästa paket (eller)

    * F5-fristående licens för BIG-IP Access Policy Manager &trade; (APM)

    * F5-tilläggsprogram för BIG-IP Access Policy Manager &trade; (APM) på en befintlig stor IP F5-LTM (Big-IP- &reg; lokal Traffic Manager &trade; ).

    * Förutom ovanstående licens kan F5-systemet också vara licensierat med:

        * En URL-filtrerings prenumeration som använder URL-kategorins databas

        * En F5-prenumeration för IP-information för att identifiera och blockera kända angripare och skadlig trafik

        * En nätverks-och maskin varu säkerhetsmodul (HSM) som skyddar och hanterar digitala nycklar för stark autentisering

* F5 BIG-IP-system är etablerad med APM-moduler (LTM är valfritt)

* Även om det är valfritt, rekommenderar vi starkt att du distribuerar F5-systemen i en [enhets grupp för synkronisering/redundans](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), som innehåller det aktiva standby-paret med en flytande IP-adress för hög tillgänglighet (ha). Ytterligare gränssnitts redundans kan uppnås med hjälp av LACP (Link Aggregation Control Protocol). LACP hanterar de anslutna fysiska gränssnitten som ett enda virtuellt gränssnitt (samlings grupp) och identifierar eventuella gränssnitts problem i gruppen.

* För Kerberos-program, ett lokalt AD-tjänstkontot för begränsad delegering.  Se [F5-dokumentationen](https://support.f5.com/csp/article/K43063049) för att skapa ett AD Delegerings konto.

## <a name="access-guided-configuration"></a>Åtkomst till guidad konfiguration

* Åtkomst till guidad konfiguration stöds på F5 TMOS version 13.1.0.8 och senare. Om ditt BIG-IP-system kör en version nedan 13.1.0.8, se avsnittet **Avancerad konfiguration** .

* Åtkomst till interaktiv konfiguration visar en helt ny och strömlinjeformad användar upplevelse. Den här arbets flödes-baserade arkitekturen ger intuitiva konfigurations steg som är anpassade till den valda topologin.

* Innan du fortsätter med konfigurationen ska du uppgradera den guidade konfigurationen genom att ladda ned det senaste användnings fall paketet från [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Följ proceduren nedan om du vill uppgradera.

    >[!NOTE]
    >Skärm bilderna nedan är för den senaste utgivna versionen (BIG-IP 15,0 med AGC version 5,0). Konfigurations stegen nedan är giltiga för det här användnings fallet över från 13.1.0.8 till den senaste versionen av BIG-IP.

1. I webb gränssnittet F5 BIG-IP klickar du på **åtkomst >> guide konfiguration**.

2. På sidan **guidad konfiguration** klickar du på **Uppgradera guidad konfiguration** i det övre vänstra hörnet.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. På popup-skärmen uppgraderings guide konfiguration väljer du **Välj fil** för att ladda upp det nedladdade användnings fallet och klickar på knappen **överför och installera** .

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. När uppgraderingen är klar klickar du på knappen **Fortsätt** .

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* F5 stöder **SP-och IDP** -INITIERAd SSO
* F5 SSO kan konfigureras på tre olika sätt.

- [Konfigurera enkel inloggning med F5 för Kerberos-program](#configure-f5-single-sign-on-for-kerberos-application)

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenarier för nyckel autentisering

Förutom Azure Active Directory inbyggt integrerings stöd för moderna autentiseringsprotokoll som öppen ID Connect, SAML och WS-utfodras, F5 utökar säker åtkomst för äldre-baserade autentiseringscertifikat för både intern och extern åtkomst med Azure AD, vilket möjliggör moderna scenarier (t. ex. lösen ords mindre åtkomst) till dessa program. Detta inkluderar:

* Huvudbaserade autentiseringsmekanismer

* Kerberos-autentisering av appar

* Anonym autentisering eller inga inbyggta autentiseringscertifikat

* NTLM-autentisering av appar (skydd med dubbla prompter för användaren)

* Formulär baserat program (skydd med dubbla prompter för användaren)

## <a name="adding-f5-from-the-gallery"></a>Lägga till F5 från galleriet

Om du vill konfigurera integrering av F5 i Azure AD måste du lägga till F5 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **F5** i sökrutan.
1. Välj **F5** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurera och testa enkel inloggning med Azure AD för F5

Konfigurera och testa Azure AD SSO med F5 med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i F5.

Om du vill konfigurera och testa Azure AD SSO med F5 slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera F5 SSO](#configure-f5-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa F5 test användare](#create-f5-test-user)** – om du vill ha en motsvarighet till B. Simon i F5 som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **F5** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<YourCustomFQDN>.f5.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<YourCustomFQDN>.f5.com/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [F5-klientens support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och **certifikat (base64)** och väljer sedan **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera F5** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till F5.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **F5**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .
1. Klicka på **villkorlig åtkomst** .
1. Klicka på **ny princip**.
1. Nu kan du se din F5-app som en resurs för CA-principen och tillämpa villkorlig åtkomst inklusive multifaktorautentisering, enhet baserad åtkomst kontroll eller identitets skydds princip.

## <a name="configure-f5-sso"></a>Konfigurera F5 SSO

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Konfigurera enkel inloggning med F5 för Kerberos-program

### <a name="guided-configuration"></a>Guidad konfiguration

1. Öppna ett nytt webbläsarfönster och logga in på din "F5 (Kerberos) företags webbplats som administratör och utför följande steg:

1. Du måste importera certifikatet för metadata till F5 som kommer att användas senare i installations processen.

1. Gå till **System > certifikat hantering > hantering av trafik certifikat > SSL-certifikat lista**. Välj **Importera** från det högra hörnet. Ange ett **certifikat namn** (kommer att refereras till senare i konfigurationen). I **certifikat källan**väljer du överför fil ange det certifikat som hämtats från Azure när du konfigurerar SAML enkel inloggning. Klicka på **Importera**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Dessutom krävs **SSL-certifikat för program värd namnet. Gå till system > certifikat hantering > hantering av trafik certifikat > SSL-certifikat lista**. Välj **Importera** från det högra hörnet. **Import typen** är **PKCS 12 (IIS)**. Ange ett **nyckel namn** (kommer att refereras till senare i konfigurationen) och ange PFX-filen. Ange **lösen ordet** för PFX-filen. Klicka på **Importera**.

    >[!NOTE]
    >I exempel namnet på appen `Kerbapp.superdemo.live` använder vi ett jokertecken med vårt namn `WildCard-SuperDemo.live`

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. Vi kommer att använda den guidade upplevelsen för att konfigurera Azure AD Federation och program åtkomst. Gå till – F5 BIG-IP- **huvud** och välj **åtkomst > guidad konfiguration > Federation > SAML-tjänstprovider**. Klicka på **Nästa** och påbörja konfigurationen genom att klicka på **Nästa** .

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Ange ett **konfigurations namn**. Ange **entitets-ID: t** (samma som det som du konfigurerade i Azure AD-programkonfigurationen). Ange **värd namnet**. Lägg till en **Beskrivning** av referensen. Godkänn återstående standard poster och välj och klicka sedan på **spara & nästa**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. I det här exemplet skapar vi en ny virtuell server som 192.168.30.200 med port 443. Ange IP-adressen för den virtuella servern i **mål adressen**. Välj klienten **SSL-profil**, Välj Skapa ny. Ange tidigare överförda program certifikat (jokertecken i det här exemplet) och tillhör ande nyckel och klicka sedan på **spara & nästa**.

    >[!NOTE]
    >i det här exemplet körs vår interna webserver på port 80 och vi vill publicera den med 443.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. Under **Välj metod för att konfigurera din IDP-anslutning**anger du metadata, klickar på Välj fil och överför metadata-XML-filen som hämtades tidigare från Azure AD. Ange ett unikt **namn** för SAML IDP Connector. Välj det **signerings certifikat för metadata** som överfördes tidigare. Klicka på **spara & nästa**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. Under **Välj en pool**anger du **Skapa ny** (du kan också välja en pool som den redan finns). Låt andra värde vara standard.    Under pooler anger du IP-adressen under **IP-adress/nodnamn**. Ange **porten**. Klicka på **spara & nästa**.
 
    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. På skärmen Inställningar för enkel inloggning väljer du **aktivera enkel inloggning**. Välj **Kerberos**under **vald typ av enkel inloggning** . Ersätt **session. SAML. senaste. identitet**  med **session. SAML. Last. attr. Name. identitet** under **användar namn källa** (den här variabeln anges med anspråks mappning i Azure AD). Välj **Visa avancerad inställning**. Under **Kerberos-sfär** skriver du domän namnet. Ange kontot och lösen ordet för APM-delegering under **konto namn/konto lösen ord** . Ange domänkontrollantens IP-adress i **KDC** -fältet. Klicka på **spara & nästa**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. I den här vägledningen kommer vi att hoppa över slut punkts kontroller.  Mer information finns i F5-dokumentationen.  På skärmen väljer du **spara & nästa**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Acceptera standardinställningarna och klicka på **spara & nästa**. Se F5-dokumentationen för information om inställningar för hantering av SAML-sessioner.


    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Granska sammanfattnings skärmen och välj **distribuera** för att konfigurera Big-IP.
 
    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. När programmet har kon figurer ATS klickar du på **Slutför**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Avancerad konfiguration

>[!NOTE]
>För referens klickar du [här](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Konfigurera en Active Directory AAA-server

Du konfigurerar en Active Directory AAA-server i Access Policy Manager (APM) för att ange domänkontrollanter och autentiseringsuppgifter för APM som ska användas för att autentisera användare.

1. På huvud fliken klickar du på **åtkomst princip > AAA-servrar > Active Directory**. Skärmen Active Directory servrar visas.

2. Klicka på **Skapa**. Skärmen nya server Egenskaper öppnas.

3. I fältet **namn** anger du ett unikt namn för autentiseringsservern.

4. I fältet **domän namn** skriver du namnet på Windows-domänen.

5. Välj något av följande alternativ för inställningen **Server anslutning** :

   * Välj **Använd pool** för att konfigurera hög tillgänglighet för AAA-servern.

   * Välj **Direct** för att konfigurera AAA-servern för fristående funktioner.

6. Om du har valt **direkt**, anger du ett namn **i fältet domänkontrollant** .

7. Om du har valt Använd **pool**konfigurerar du poolen:

   * Skriv ett namn i fältet **namn på domänkontrollantens pool** .

   * Ange **domän kontrol Lanterna** i poolen genom att skriva in IP-adressen och värd namnet för var och en av dem och klicka på knappen **Lägg till** .

   * För att övervaka hälso tillståndet för AAA-servern har du möjlighet att välja en hälso Övervakare: endast **gateway_icmps** övervakaren är lämplig i det här fallet. Du kan välja den från övervaknings listan för **serverpoolen** .

8. I fältet **admin-namn** skriver du ett skift läges känsligt namn för en administratör som har Active Directory administratörs behörighet. APM använder informationen i fälten **admin-namn** och **Administratörs lösen ord** för AD-fråga. Om Active Directory har kon figurer ATS för anonyma frågor behöver du inte ange ett administratörs namn. Annars behöver APM ett konto med tillräcklig behörighet för att binda till en Active Directory-Server, Hämta användar grupps information och hämta Active Directory lösen ords principer som stöder lösenordsbaserade funktioner. (APM måste hämta lösen ords principer, till exempel om du väljer alternativet uppmana användaren att ändra lösen ordet innan förfallo datum i en AD-fråga.) Om du inte anger information om administratörs kontot i den här konfigurationen använder APM användar kontot för att hämta information. Detta fungerar om användar kontot har tillräcklig behörighet.

9. I fältet **Administratörs lösen ord** anger du det administratörs lösen ord som är associerat med domän namnet.

10. I fältet **Verifiera administratörs lösen ord** anger du det administratörs lösen ord som är associerat med **domän namns** inställningen.

11. I fältet **gruppens cache livstid** anger du antalet dagar. Standard livs längden är 30 dagar.

12. Ange antalet dagar i fältet **livs längd för lösen ords säkerhets objektets cacheminne** . Standard livs längden är 30 dagar.

13. Från listan **Kerberos-typ för Kerberos-förautentisering** väljer du en krypterings typ. Standardvärdet är **none**. Om du anger en krypterings typ inkluderar BIG-IP-systemet Kerberos-Förautentiserings data i det första begäran om Autentiseringstjänsten (AS-REQ).

14. I fältet **tids gräns** anger du ett timeout-intervall (i sekunder) för AAA-servern. (Den här inställningen är valfri.)

15. Klicka på **klart**. Den nya servern visas på listan. Detta lägger till den nya Active Directory servern i listan Active Directory servrar.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML-konfiguration

1. Du måste importera certifikatet för metadata till F5 som kommer att användas senare i installations processen. Gå till **System > certifikat hantering > hantering av trafik certifikat > SSL-certifikat lista**. Välj **Importera** från det högra hörnet.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. För att konfigurera SAML-IDP, **navigera till åtkomst > Federation > SAML: Service Provider > externa IDP-kopplingar**och klicka på **skapa > från metadata**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. Om du vill konfigurera SAML SP navigerar du till **åtkomst > Federation > SAML Service Provider > lokala SP-tjänster** och klickar på **skapa**. Slutför följande information och klicka på **OK**.

    * Typnamn: KerbApp200SAML
    * Entitets-ID *: https://kerb-app.com.cutestat.com
    * Inställningar för SP-namn
    * Schema: https
    * Värd: kerbapp200. superdemo. Live
    * Beskrivning: kerbapp200. superdemo. Live

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Välj SP-konfigurationen, KerbApp200SAML och klicka på **BIND/UnBind IDP-kopplingar**.

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     c. Klicka på **Lägg till ny rad** och välj den **externa IDP-anslutning** som skapades i föregående steg, klicka på **Uppdatera**och klicka sedan på **OK**.

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. För att konfigurera Kerberos SSO navigerar du till **åtkomst > enkel inloggning > Kerberos**, fullständig information och klickar på **slutfört**.

    >[!Note]
    > Du behöver kontot för Kerberos-delegering för att kunna skapas och anges. Se avsnittet KCD (se tillägg för variabel referenser)

    * **Användar namn källa**: session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/WS/2005/05/Identity/Claims/givenName

    * **Användar sfär källa**: session. logon. senaste. domän

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. För att konfigurera åtkomst profilen, navigera till **åtkomst > profil/principer > åtkomst profil (per sessionstillstånd)**, klicka på **skapa**, Slutför följande information och klicka på **Slutför**.

    * Namn: KerbApp200
    * Profil typ: alla
    * Profil omfattning: profil
    * Språk: engelska

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Klicka på namnet KerbApp200, Slutför följande information och klicka på **Uppdatera**.

    * Domän-cookie: superdemo. Live
    * SSO-konfiguration: KerAppSSO_sso

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Klicka på **åtkomst princip** och klicka sedan på **Redigera åtkomst princip** för profilen "KerbApp200".

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **session. logon. senaste. usernameUPN uttryck {[mcget {session. SAML. Last. Identity}]}**

    * **session. AD. lastactualdomain TEXT superdemo. Live**

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName =% {session. logon. senaste. usernameUPN})**

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **session. logon. senaste. username-uttryck {"[mcget {session. AD. Last. attr. sAMAccountName}]"}**

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session. logo. senaste. username}**
    * **mcget {session. logo. senaste. password**

1. För att lägga till en ny nod, navigera till **> noder i lokal trafik noder > Node, klicka på Skapa**, Slutför följande information och klicka sedan på **slutfört**.

    * Namn: KerbApp200
    * Beskrivning: KerbApp200
    * Adress: 192.168.20.200

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. För att skapa en ny pool, navigera till **lokal trafik > pooler > pool, klicka på Skapa**, Slutför följande information och klicka på **Slutför**.

    * Namn: KerbApp200-pool
    * Beskrivning: KerbApp200-pool
    * Hälso Övervakare: http
    * Adress: 192.168.20.200
    * Tjänst port: 81

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. För att skapa en virtuell server går du till **lokal trafik > virtuella servrar > Virtual Server List > +**, Slutför följande information och klickar på **slutförd**.

    * Namn: KerbApp200
    * Mål adress/mask: värd 192.168.30.200
    * Tjänst port: port 443 HTTPS
    * Åtkomst profil: KerbApp200
    * Ange åtkomst profilen som skapades i föregående steg

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Konfigurera Kerberos-delegering 

>[!NOTE]
>För referens klickar du [här](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Steg 1:** Skapa ett Delegerings konto

    **Exempel:**
    * Domän namn: **superdemo. live**

    * Sam-konto namn: **Big-ipuser**

    * New-ADUser-Name "APM Delegerings konto"-UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -sAMAccountName "Big-ipuser"-PasswordNeverExpires $True-enabled $True-AccountPassword (Read-Host-AsSecureString "Password! 1234")

* **Steg 2:** Ange SPN (på kontot för APM-delegering)

    **Exempel:**
    * Setspn – en **värd/Big-ipuser. superdemo. live** stor – ipuser

* **Steg 3:** SPN-delegering (för App Service konto) Konfigurera lämplig delegering för F5-Delegerings kontot.
    I exemplet nedan konfigureras APM Delegerings kontot för KCD för FRP-app1. superdemo. Live-app.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Ange de uppgifter som anges i referens dokumentet ovan under [detta](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

### <a name="create-f5-test-user"></a>Skapa F5-test användare

I det här avsnittet skapar du en användare som heter B. Simon i F5. Arbeta med [F5-klient support teamet](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att lägga till användarna på F5-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på F5-panelen i åtkomst panelen, bör du loggas in automatiskt på F5 som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova F5 med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

