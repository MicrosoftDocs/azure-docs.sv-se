---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Oracle, som skyddas av F5 BIG-IP APM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Oracle, som skyddas av F5 av BIG-IP APM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 3af149f0c1db7f354be6bd968bbd0cf858493d4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219305"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Oracle lib-Protected av F5 BIG-IP APM

I den här självstudien får du lära dig hur du integrerar Oracle, som skyddas av F5 av stor IP APM med Azure Active Directory (Azure AD). När du integrerar Oracle, som skyddas av F5 BIG-IP APM med Azure AD, kan du:

* Kontroll i Azure AD som har åtkomst till Oracle i Oracle, som skyddas av F5 BIG-IP APM.
* Gör det möjligt för användarna att logga in automatiskt till Oracle, som skyddas av F5 av stor IP APM med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.
* Den här självstudien beskriver instruktioner för Oracle i

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

1. En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
1. Oracle, som skyddas av F5, enkel inloggning (SSO) med stora IP APM-prenumeranter.

1. Distribution av den gemensamma lösningen kräver följande licens:

    1. F5 BIG-IP® bästa paket (eller) 
    2. F5-fristående licens för BIG-IP Access Policy Manager™ (APM) 
    3. F5-tilläggsprogram för BIG-IP Access Policy Manager™ (APM) för en befintlig BIG IP F5 BIG-IP® lokala Traffic Manager™ (LTM).
    4. Förutom ovanstående licens kan F5-systemet också vara licensierat med: 
        * En URL-filtrerande prenumeration för att använda URL-kategorins databas. 
        * En F5-prenumeration för IP-information för att identifiera och blockera kända angripare och skadlig trafik. 
        * En nätverks-och maskin varu säkerhetsmodul (HSM) som skyddar och hanterar digitala nycklar för stark autentisering.
1. F5 BIG-IP-system är etablerad med APM-moduler (LTM är valfritt).
1. Även om det är valfritt, rekommenderar vi starkt att du distribuerar F5-systemen i en [enhets grupp för synkronisering/redundans](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), som innehåller det aktiva standby-paret med en flytande IP-adress för hög tillgänglighet (ha). Ytterligare gränssnitts redundans kan uppnås med hjälp av LACP (Link Aggregation Control Protocol). LACP hanterar de anslutna fysiska gränssnitten som ett enda virtuellt gränssnitt (samlings grupp) och identifierar eventuella gränssnitts problem i gruppen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Oraclead-skyddad av F5 BIG-IP APM stöder **SP-och IDP** -initierad SSO.

## <a name="add-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Lägg till Oracle, som skyddas av F5 BIG-IP APM från galleriet

Om du vill konfigurera integreringen av Oracle-volymer med hög IP-adress i F5 i Azure AD, måste du lägga till Oracle-skyddade av F5 BIG-IP APM från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Oracle, som skyddas av F5 Big-IP APM** i sökrutan.
1. Välj **Oracle, som skyddas av F5 Big-IP APM** från resultat panelen, och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Konfigurera och testa Azure AD SSO för Oracle lib-Protected av F5 BIG-IP APM

Konfigurera och testa Azure AD SSO med Oracle lib-Protected av F5 BIG-IP APM med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Oracle, som skyddas av F5 BIG-IP APM.

Utför följande steg för att konfigurera och testa Azure AD SSO med Oracle 1 – skyddad av F5 BIG-IP APM:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Oracle PeopleSoft-Protected av F5 Big-IP APM SSO](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa oracle PeopleSoft-Protected av F5 test användare av Big-IP APM](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** – om du vill ha en motsvarighet till B. Simon i Oracle, som skyddas av F5 Big-IP APM som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan Oracle, som **skyddas av F5 av F5-** program integration i Azure Portal, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<FQDN>.peoplesoft.f5.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. I text rutan **utloggnings-URL** skriver du en URL med följande mönster: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Dessa värden är inte verkliga. Uppdatera värdena med faktisk Sign-On-URL, identifierare, svars-URL och utloggnings-URL. Kontakta [Oracle, som skyddas av F5-support teamet för Big-IP APM-klienten](https://support.f5.com) , för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Oracle, som skyddas av F5 BIG-IP APM-program, förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom över, förväntas fler attribut som ska skickas tillbaka i SAML-svar som visas nedan, i Oracle, som skyddas av F5 BIG-IP APM-program. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , laddar du ned **XML för federationsmetadata** och **certifikatet (base64)** och sparar det på din dator.

    ![Länk för nedladdning av certifikatet](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Oracle, som skyddas av F5 BIG-IP APM.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Oracle, som skyddas av F5 Big-IP APM**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Konfigurera Oracle PeopleSoft-Protected av F5 BIG-IP APM SSO

### <a name="f5-saml-sp-configuration"></a>F5 SAML SP-konfiguration

Importera metadata-certifikatet till F5 som kommer att användas senare i installations processen. Gå till **System > certifikat hantering > hantering av trafik certifikat > SSL-certifikat lista**. Välj **Importera** från det högra hörnet.

![F5 SAML SP-konfiguration](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Konfigurera SAML IDP-anslutningen 

1. Gå till **åtkomst > Federation > SAML: Service Provider > externa IDP-kopplingar** och klicka på **skapa > från metadata**.

    ![F5 SAML IDP-koppling](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. På följande sida klickar du på **Bläddra** för att ladda upp XML-filen.

1. Ange ett giltigt namn i text rutan för **identitets leverantörens namn** och klicka sedan på **OK**.

    ![ny SAML IDP-anslutning](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Utför de steg som krävs på fliken **säkerhets inställningar** och klicka sedan på **OK**.

    ![Redigera SAML IDP-koppling](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Konfigurera SAML SP

1. Gå till **åtkomst > Federation > SAML Service Provider > lokala SP-tjänster** och klicka på **skapa**. Slutför följande information och klicka på **OK**.

    * Namn: `<Name>`
    * Enhets-ID: `https://<FQDN>.peoplesoft.f5.com`
    * Inställningar för SP-namn
        * Schema `https`
        * Värd `<FQDN>.peoplesoft.f5.com`
        * Beteckning `<Description>`

    ![nya SAML SP-tjänster](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Välj SP-konfigurationen, PeopleSoftAppSSO och **Klicka på Bind/UnBind IDP-kopplingar**.
Klicka på **Lägg till ny rad** och välj den **externa IDP-anslutning** som skapades i föregående steg, klicka på **Uppdatera** och klicka sedan på **OK**.

    ![Skapa SAML SP-tjänster](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Konfigurerar program

### <a name="create-a-new-pool"></a>Skapa en ny pool
1. Gå till **lokal trafik > pooler > pool**, klicka på **skapa**, Slutför följande information och klicka på **slutförd**.

    * Namn: `<Name>`
    * Beteckning `<Description>`
    * Hälso Övervakare: `http`
    * Adresspool `<Address>`
    * Tjänst port: `<Service Port>`

    ![Skapa ny pool](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Skapa en ny klient-SSL-profil

Gå till **lokal trafik > profiler > SSL > klient > +**, Slutför följande information och klicka på **slutförd**.

* Namn: `<Name>`
* Certifikatmallens `<Certificate>`
* Nyckel: `<Key>`

![ny klient-SSL-profil](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Skapa en ny virtuell server

1. Gå till **lokal trafik > virtuella servrar > Virtual Server List > +**, Slutför följande information och klicka på **slutförd**.
    * Namn: `<Name>`
    * Mål adress/mask: `<Address>`
    * Tjänst port: port 443 HTTPS
    * HTTP-profil (klient): http

    ![Skapa en ny virtuell server](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Fyll i följande värden på sidan nedan:

    * SSL-profil (klient): `<SSL Profile>`
    * Käll adress översättning: automatisk mappning
    * Åtkomst profil: `<Access Profile>`
    * Standardpool: `<Pool>`


    ![Skapa en ny virtuell server-pool med virtuella servrar](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Konfigurera en webbapp-applikation som stöder F5 Big-IP APM som enkel inloggnings lösning

>[!Note]
> Förhållande https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Logga in på den på den andra konsolen `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` med administratörs behörighet (exempel: PS/PS).

    ![Self-tjänster för chefen](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. Skapa **OAMPSFT** som en ny användar profil i The lib-appen och koppla en säkerhets roll med låg säkerhet, till exempel en användare av en **användare**.
Gå till **Peopletools > säkerhets > användar profiler > användar profiler** för att skapa en ny användar profil, till exempel: **OAMPSFT** och Lägg till **användare** av en användare.

    ![Användare, användare](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Öppna webb profilen och ange **OAMPSFT** som **användar-ID** för offentlig åtkomst.

    ![Användar profiler](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. Öppna **FUNCLIB_LDAP** -posten från **Programdesignern för PeopleTools**.

    ![konfiguration av webb profil](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Uppdatera användar huvudet med **PS_SSO_UID** för **OAMSSO_AUTHENTICATION** -funktionen.
I funktionen **getWWWAuthConfig ()** ersätter du värdet som har tilldelats **&defaultUserId** med **OAMPSFT** som vi definierade i webb profilen. Spara post definitionen.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Få åtkomst till **inloggning PeopleCode** -sidan (PeopleTools, säkerhet, säkerhets objekt, inloggning PeopleCode) och aktivera funktionen **OAMSSO_AUTHENTICATION** – inloggning PeopleCode för Oracle Access Manager single inloggning.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Konfigurera F5 stor IP APM för att fylla i "PS_SSO_UID" HTTP-huvud med användar-ID: t

### <a name="configuring-per-request-policy"></a>Konfigurera Per-Request princip
1. Gå till **åtkomst > profil/principer > Per-Request principer**, klicka på **skapa**, Slutför följande information och klicka på **Slutför**.

    * Namn: `<Name>`
    * Profil typ: alla
    * Användning `<Language>`

    ![Konfigurera Per-Request princip ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Klicka på **redigera** Per-Request princip `<Name>` ![ Redigera Per-Request princip PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Tilldela den virtuella servern Per-Request principen

Navigera till **lokal trafik > virtuella servrar > Virtual Server List > PeopleSoftApp** ange `<Name>` som Per-Request princip

![PeopleSoftSSO som Per-Request princip ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Konfigurera F5 Big-IP APM för att stödja enkel utloggning från en/ett-program

Följ dessa steg om du vill lägga till ett enda utloggnings stöd för alla användare av användare:

1. Fastställ korrekt utloggnings-URL för den andra sidan
    * Du måste öppna portalen med valfri webbläsare och aktivera fel söknings verktyg för webbläsare, som du ser i exemplet nedan, för att avgöra vilken adress som används för att avsluta en användarsession:

        ![utloggnings-URL för den andra. ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Hitta elementet med `PT_LOGOUT_MENU` ID och spara URL-sökvägen med frågeparametrar. I vårt exempel fick vi följande värde: `/psp/ps/?cmd=logout`

1. Skapa LTM iRule som omdirigerar användaren till APM-utloggnings-URL: `/my.logout.php3`

    * Navigera till **lokal trafik > iRule**, klicka på **skapa**, Slutför följande information och klicka på **Slutför**.

        Namn: `<Name>`  
        Definition:  
                    _När HTTP_REQUEST {switch-BLOB--[HTTP:: URI] { `/psp/ps/?cmd=logout` {http:: Redirect `/my.logout.php3` }}}_

1. Tilldela den skapade iRule till den virtuella servern

    * Navigera till **lokal trafik > virtuella servrar > Virtual Server List > PeopleSoftApp >-resurser**. Klicka på **hantera...** Slutför   

    * Ange `<Name>` som aktive rad iRule och klicka på **färdig**.

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Ge **namnet** text Rute värde som `<Name>` 

        ![_iRule_PeopleSoftApp avslutad](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Skapa Oracle PeopleSoft-Protected av F5 test användare för BIG-IP APM

I det här avsnittet skapar du en användare som heter B. Simon i Oracle PeopleSoft-Protected av F5 BIG-IP APM. Arbeta med [oracle PeopleSoft-Protected av F5 Big-IP APM support team](https://support.f5.com) för att lägga till användare i Oracle-PeopleSoft-Protected av F5-plattform för stor IP-adress. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Oracle PeopleSoft-Protected av F5 BIG-IP APM-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Oracle PeopleSoft-Protected av F5 stor IP APM-inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal och du bör logga in automatiskt till Oracle PeopleSoft-Protected av F5 Big-IP APM för vilken du konfigurerar SSO. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på Oracle-PeopleSoft-Protected av en stor IP APM-panel i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till Oracle PeopleSoft-Protected av F5 BIG-IP APM som du konfigurerar SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Oracle PeopleSoft-Protected av F5 BIG-IP APM kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
