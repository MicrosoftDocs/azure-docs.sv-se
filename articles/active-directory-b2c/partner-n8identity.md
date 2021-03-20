---
title: Självstudie för att konfigurera N8-identitet med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Själv studie kurs om hur du konfigurerar TheAccessHub administrations verktyg med Azure Active Directory B2C för att adressera migrering av kund konton och kund tjänst begär Anden (CSR).
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96170930"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera TheAccessHub Admin Tool med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar Azure Active Directory (AD) B2C med [TheAccessHub Admin Tool](https://n8id.com/products/theaccesshub-admintool/) från N8 identitet. Den här lösningen behandlar migrering av kund konton och kund tjänst begär Anden (CSR)-administration.  

Den här lösningen passar dig, om du har ett eller flera av följande behov:

- Du har en befintlig plats och vill migrera till Azure AD B2C. Men du är kämpar med migrering av dina kund konton, inklusive lösen ord

- Du behöver ett verktyg som du kan använda för att administrera Azure AD B2C-konton.

- Du måste ha ett krav för att använda delegerad administration för dina kund service representanter.

- Du vill synkronisera och slå samman data från flera databaser till Azure AD B2C.

## <a name="pre-requisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](./tutorial-create-tenant.md). Klienten måste vara länkad till din Azure-prenumeration.

- En TheAccessHub-administratörs verktygs miljö: kontakta [N8 identitet](https://n8id.com/contact/) för att etablera en ny miljö.

- Valfritt Information om anslutningar och autentiseringsuppgifter för alla databaser eller LDAP-protokoll (Lightweight Directory Access Protocol) som du vill migrera kund information från.

- Valfritt Konfigurerat Azure AD B2Cs miljö för att använda [anpassade principer](./custom-policy-get-started.md), om du vill integrera TheAccessHub-administrationsverktyget i ditt registrerings princip flöde.

## <a name="scenario-description"></a>Scenariobeskrivning

Administrations verktyget TheAccessHub körs som alla andra program i Azure. Den kan köras i antingen N8-identitetens Azure-prenumeration eller kundens prenumeration. I följande arkitektur diagram visas implementeringen.

![Bild som visar n8identity-arkitektur diagram](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användare väljer registrering för att skapa ett nytt konto och ange information på sidan. Azure AD B2C samlar in användarattribut.
| 2. | Azure AD B2C anropar administrations verktyget för TheAccessHub och skickar på användarattribut
| 3. | TheAccessHub admin-verktyget kontrollerar den befintliga databasen efter aktuell användar information.  
| 4. | Användar posten synkroniseras från databasen till TheAccessHub admin-verktyget.
| 5. | TheAccessHub admin-verktyget delar data med den delegerade CSR/supportavdelningen-administratören.
| 6. | TheAccessHub admin-verktyget synkroniserar användar posterna med Azure AD B2C.
| 7. |Baserat på det lyckade/misslyckade svaret från TheAccessHub admin-verktyget, skickar Azure AD B2C ett anpassat välkomst meddelande till användaren.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Skapa en global administratör i din Azure AD B2C-klient

Administrations verktyget TheAccessHub kräver behörighet att agera för en global administratörs räkning för att läsa användar information och genomföra ändringar i Azure AD B2C-klienten. Ändringar i dina ordinarie administratörer vann; Det kan påverka TheAccessHub admin-verktygets förmåga att interagera med klienten.

Följ dessa steg om du vill skapa en global administratör:

1. Logga in på din Azure AD B2C-klient som administratör i Azure Portal. Navigera till **Azure Active Directory**  >  **användare**
2. Välj **ny användare**
3. Välj **skapa användare** för att skapa en vanlig katalog användare och inte en kund
4. Fyll i formuläret identitets information

   a. Ange användar namnet, till exempel "theaccesshub"

   b. Ange namnet, till exempel "TheAccessHub Service Account"

5. Välj **Visa lösen ord** och kopiera det ursprungliga lösen ordet för senare användning
6. Tilldela rollen global administratör

   a. Välj användarens aktuella roll **användare** för att ändra den

   b. Kontrol lera posten för global administratör

   c. **Välj**  >  **Skapa**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Anslut TheAccessHub admin-verktyget med din Azure AD B2C klient

TheAccessHub Admin Tool använder Microsofts Graph API för att läsa och göra ändringar i din katalog. Den fungerar som global administratör i din klient organisation. Ytterligare behörighet krävs av TheAccessHub Admin Tool, som du kan bevilja inifrån verktyget.

Följ dessa steg om du vill auktorisera TheAccessHub Admin Tool för att få åtkomst till din katalog:

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Gå till **system administratör**  >  **Azure AD B2C konfiguration**

3. Välj **auktorisera anslutning**

4. I det nya fönstret loggar du in med ditt globala administratörs konto. Du kan bli ombedd att återställa ditt lösen ord om du loggar in för första gången med det nya tjänst kontot.

5. Följ anvisningarna och välj **Godkänn** för att bevilja TheAccessHub admin verktyget de begärda behörigheterna.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Konfigurera en ny CSR-användare med din företags identitet

Skapa en CSR/supportavdelningen-användare som har åtkomst till TheAccessHub admin-verktyget med sina befintliga företags Azure Active Directory autentiseringsuppgifter.

Om du vill konfigurera CSR/helpdesk-användare med enkel inloggning (SSO) rekommenderar vi följande steg:

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som tillhandahålls av N8-identiteten.

2. Gå till **hanterings verktyg**  >  **Hantera kollegor**

3. Välj **Lägg till kollega**

4. Välj **kollega typ Azure-administratör**

5. Ange information om kollegans profil

   a. Att välja en hem organisation kontrollerar vem som har behörighet att hantera den här användaren.

   b. För inloggnings-ID/Azure AD-användarnamn anger du användarens huvud namn från användarens Azure Active Directory konto.

   c. På fliken TheAccessHub roller kontrollerar du den hanterade roll supportavdelningen. Det gör att användaren får åtkomst till vyn hantera kolleger. Användaren måste fortfarande placeras i en grupp eller bli ägare av organisationen för att kunna agera på kunderna.

6. Välj **Skicka**.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Konfigurera en ny CSR-användare med en ny identitet

Skapa en CSR/supportavdelningen-användare som kommer åt TheAccessHub admin-verktyget med en ny lokal autentiseringsuppgift som är unik för TheAccessHub admin-verktyget. Detta används huvudsakligen av organisationer som inte använder en Azure AD för sitt företag.

Följ dessa steg om du vill [Konfigurera en CSR/supportavdelningen](https://youtu.be/iOpOI2OpnLI) -användare utan SSO:

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som tillhandahålls av N8-identiteten

2. Gå till **hanterings verktyg**  >  **Hantera kollegor**

3. Välj **Lägg till kollega**

4. Välj **lokal administratör för kollega**

5. Ange information om kollegans profil

   a. Att välja en hem organisation kontrollerar vem som har behörighet att hantera den här användaren.

   b. På fliken **TheAccessHub roller** väljer du den hanterade roll **supportavdelningen**. Det gör att användaren får åtkomst till vyn hantera kolleger. Användaren måste fortfarande placeras i en grupp eller bli ägare av organisationen för att kunna agera på kunderna.

6. Kopiera **inloggnings-ID/e-post** och **lösen ord för en tid** . Ange den nya användaren. De använder dessa autentiseringsuppgifter för att logga in på TheAccessHub admin-verktyget. Användaren uppmanas att ange ett nytt lösen ord vid första inloggningen.

7. Välj **Skicka**

## <a name="configure-partitioned-csr-administration"></a>Konfigurera partitionerad CSR-administration

Behörigheter för att hantera kund-och CSR/helpdesk-användare i TheAccessHub admin-verktyget hanteras med hjälp av en organisationshierarki. Alla kollegor och kunder har en hem organisation där de finns. Vissa kollegor eller grupper av kollegor kan tilldelas som ägare av organisationer.  Organisations ägare kan hantera (göra ändringar i) kollegor och kunder i organisationer eller under organisationer som de äger. För att flera kollegor ska kunna hantera en uppsättning användare kan en grupp skapas med många medlemmar. Gruppen kan sedan tilldelas som organisations ägare och alla medlemmar i gruppen kan hantera kollegor och kunder i organisationen.

### <a name="create-a-new-group"></a>Skapa en ny grupp

1. Logga in på TheAccessHub admin-verktyget med **autentiseringsuppgifter** som du fått av N8 identitet

2. Navigera till **organisationens > hantera grupper**

3. Välj > **Lägg till grupp**

4. Ange ett **grupp namn**, en **grupp Beskrivning** och en **grupp ägare**

5. Sök efter och markera rutorna i de kollegor som du vill ska vara medlemmar i gruppen och välj sedan >**Lägg till**

6. Längst ned på sidan kan du se alla medlemmar i gruppen.

7. Om det behövs medlemmar kan tas bort genom att markera **x** i slutet av raden

8. Välj **Skicka**

### <a name="create-a-new-organization"></a>Skapa en ny organisation

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Navigera till organisationens > **Hantera organisationer**

3. Välj > **Lägg till organisation**

4. Ange ett **organisations namn**, en **organisations ägare** och en **överordnad organisation**.

    a. Organisationens namn är det bästa värdet som motsvarar dina kund uppgifter. Om du anger namnet på organisationen i belastningen när du läser in kollega och kunddata, kan kollegan automatiskt placeras i organisationen.

    b. Ägaren representerar den person eller grupp som ska hantera kunder och kollegor i den här organisationen och eventuell under organisation i.

    c. Den överordnade organisationen visar vilken annan organisation som är integrerad, även ansvarig för den här organisationen.

5. Välj **Skicka**.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Ändra hierarkin via trädvyn

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Navigera till trädvyn för **hanterings verktyg**  >  

3. I den här representationen kan du visualisera vilka kollegor och grupper som kan hantera vilka organisationer.

4. Hierarkierna kan ändras genom att dra organisationer overtop-organisationer som du vill att de ska bli överordnade.

5. Välj **Spara** när du är klar med att ändra hierarkin.

## <a name="customize-welcome-notification"></a>Anpassa välkomst meddelande

När du använder TheAccessHub för att migrera användare från en tidigare autentiseringsnyckel till Azure AD B2C kanske du vill anpassa användar välkomst meddelandet, som skickas till användaren med TheAccessHub under migreringen. Det här meddelandet innehåller vanligt vis länken för kunden att ange ett nytt lösen ord i Azure AD B2C-katalogen.

Så här anpassar du meddelandet:

1. Logga in på TheAccessHub med autentiseringsuppgifter som du fått av N8 identitet

2. Gå till **system administratörs**  >  **meddelanden**

3. Välj **mallen skapa kollega**

4. Välj **Redigera**

5. Ändra fälten för meddelande och mall efter behov. Fältet mall är HTML-medvetet och kan skicka HTML-formaterade meddelanden till kund-e-post.

6. Välj **Spara** när du är färdig.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Migrera data från externa data källor till Azure AD B2C

Med hjälp av TheAccessHub Admin Tool kan du importera data från olika databaser, LDAPs och CSV-filer och sedan skicka dessa data till din Azure AD B2C-klient. Det gör du genom att läsa in data i Azure AD B2C användarens typ av kollega i administrations verktyget för TheAccessHub.  Om data källan inte är Azure själva placeras data i både TheAccessHub admin-verktyget och Azure AD B2C. Om källan till dina externa data inte är en enkel CSV-fil på datorn konfigurerar du en data källa innan du gör data inläsningen. Stegen nedan beskriver hur du skapar en data källa och gör data inläsningen.

### <a name="configure-a-new-data-source"></a>Konfigurera en ny data Källa

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Gå till **system administratörs**  >  **data källor**

3. Välj **Lägg till data källa**

4. Ange ett **namn** och en **typ** för den här data källan

5. Fyll i formulär data, beroende på typ av data Källa:

   **För databaser**

   a. **Typ** – databas

   b. **Databas typ** – Välj en databas från en av de databas typer som stöds.

   c. **Anslutnings-URL** – ange en korrekt formaterad JDBC-anslutningssträng. Till exempel: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Användar namn** – Ange användar namnet för åtkomst till databasen

   e. **Lösen ord** – ange lösen ordet för att komma åt databasen

   f. **Fråga** – ange SQL-frågan för att extrahera kund information. Till exempel: ``SELECT * FROM mytable;``

   ex. Väljer **Testa anslutning** visas ett exempel på dina data för att säkerställa att anslutningen fungerar.

   **För LDAPs**

   a. **Typ** – LDAP

   b. **Värd** – ange värd namnet eller IP-adressen för den dator där LDAP-servern körs. Till exempel: ``mysite.com``

   c. **Port** – ange det port nummer som LDAP-servern lyssnar på.

   d. **SSL** – Markera kryss rutan om TheAccessHub admin-verktyget ska kommunicera med LDAP på ett säkert sätt med hjälp av SSL. Användning av SSL rekommenderas starkt.

   e. **Inloggnings namn** – ange DN för det användar konto som ska logga in och gör LDAP-sökningen

   f. **Lösen ord** – ange användarens lösen ord

   ex. **Bas-DN** – ange DN överst i hierarkin där du vill göra sökningen

   h. **Filtrera** – Ange LDAP-filterlistan som ska hämta dina kund poster

   i. **Attribut** – ange en kommaavgränsad lista med attribut från dina kund poster som ska skickas till TheAccessHub Admin Tool

   j. Välj **test anslutningen** så ser du ett exempel på dina data för att säkerställa att anslutningen fungerar.

   **För OneDrive**

   a. **Typ** – OneDrive för företag

   b. Välj **auktorisera anslutning**

   c. I ett nytt fönster uppmanas du att logga in på **OneDrive**, logga in med en användare med Läs behörighet till ditt OneDrive-konto. TheAccessHub admin-verktyget kommer att fungera för att den här användaren ska kunna läsa CSV-läsa filer.

   d. Följ anvisningarna och välj **Godkänn** för att bevilja TheAccessHub admin verktyget de begärda behörigheterna.

6. Välj **Spara** när du är färdig.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Synkronisera data från data källan i Azure AD B2C

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Navigera till synkronisering av **system administratörs**  >  **data**

3. Välj **ny belastning**

4. Välj **typ av kollega** Azure AD B2C användare

5. Välj **källa** i popup-dialogrutan och välj din data källa. Om du har skapat en OneDrive-datakälla väljer du också filen.

6. Om du inte vill skapa nya kund konton med den här belastningen ändrar du den första principen: **om det inte gick att hitta kollegan i TheAccessHub** **gör du ingenting**

7. Om du inte vill uppdatera befintliga kund konton med den här belastningen ändrar du den andra principen **om käll-och TheAccessHub data inte matchar** och **gör ingenting**

8. Välj **Nästa**

9. I **Sök mappnings konfigurationen** identifierar vi hur man korrelerar inläsnings poster med kunder som redan har lästs in i administrations verktyget för TheAccessHub. Välj ett eller flera identifierande attribut i källan. Matcha attributen med ett attribut i TheAccessHub admin-verktyget som innehåller samma värden. Om en matchning hittas åsidosätts den befintliga posten. Annars skapas en ny kund. Du kan sekvensera ett antal kontroller. Du kan till exempel kontrol lera e-postmeddelandet först och sedan för-och efter namn.

10. På den vänstra menyn väljer du **data mappning**.

11. I Data-Mapping-konfigurationen tilldelar du det TheAccessHub administrations verktygets attribut ska fyllas i från dina källattribut. Du behöver inte mappa alla attribut. Omappade attribut är oförändrade för befintliga kunder.

12. Om du mappar till attributet org_name med ett värde som är namnet på en befintlig organisation placeras nya kunder som skapats i organisationen.

13. Välj **Nästa**

14. Ett dagligt/veckovis eller månatligt schema kan anges om den här inläsningen ska göras om. Behåll annars standardinställningen **nu**.

15. Välj **Skicka**

16. Om **schemat nu** har marker ATS läggs en ny post till på skärmen data synkroniseringar direkt. När validerings fasen har nått 100% väljer du den **nya posten** för att se förväntat resultat för belastningen. För schemalagda inläsningar visas dessa poster endast efter den schemalagda tiden.

17. Om det inte finns några fel väljer du **Kör** för att genomföra ändringarna. Annars väljer du **ta bort** på menyn **mer** för att ta bort belastningen. Du kan sedan korrigera käll informationen eller läsa in mappningar och försöka igen. I stället kan du uppdatera posterna manuellt och välja **Uppdatera** på varje post för att göra ändringar om antalet fel är litet. Slutligen kan du fortsätta med eventuella fel och lösa dem senare som **stöd för ingrepp** i TheAccessHub administrations verktyg.

18. När **datasynkroniseringen** blir 100% i inläsnings fasen, kommer alla ändringar som uppstår från belastningen att initieras. Kunderna bör börja visa eller ta emot ändringar i Azure AD B2C.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Synkronisera Azure AD B2C kund information 

Som en engångs-eller pågående åtgärd kan TheAccessHub admin-verktyget synkronisera all kund information från Azure AD B2C till TheAccessHub admin-verktyget. Detta garanterar att CSR/helpdesk-administratörer ser uppdaterad kund information.

Så här synkroniserar du data från Azure AD B2C till administrations verktyget för TheAccessHub:

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Navigera till synkronisering av **system administratörs**  >  **data**

3. Välj **ny belastning**

4. Välj **typ av kollega** Azure AD B2C användare

5. Lämna standardvärdena för steget **alternativ** .

6. Välj **Nästa**

7. Lämna standardvärdena för **data mappningen & search** -steget. Förutom om du mappar till attributet **org_name** med ett värde som är namnet på en befintlig organisation placeras nya kunder som skapats i organisationen.

8. Välj **Nästa**

9. Ett dagligt/veckovis eller månatligt schema kan anges om den här inläsningen ska göras om. Behåll annars standardinställningen: **nu**. Vi rekommenderar att du synkroniserar från Azure AD B2C regelbundet.

10. Välj **Skicka**

11. Om schemat **nu** har marker ATS läggs en ny post till på skärmen data synkroniseringar direkt. När validerings fasen har nått 100% väljer du den nya posten för att se förväntat resultat för belastningen. För schemalagda inläsningar visas dessa poster endast efter den schemalagda tiden.

12. Om det inte finns några fel väljer du **Kör** för att genomföra ändringarna. Annars väljer du **ta bort** på menyn mer för att ta bort belastningen. Du kan sedan korrigera käll informationen eller läsa in mappningar och försöka igen. I stället kan du uppdatera posterna manuellt och välja **Uppdatera** på varje post för att göra ändringar om antalet fel är litet. Slutligen kan du fortsätta med eventuella fel och lösa dem senare som stöd för ingrepp i TheAccessHub administrations verktyg.

13. När **datasynkroniseringen** blir 100% i inläsnings fasen, kommer alla ändringar som uppstår från belastningen att initieras.

## <a name="configure-azure-ad-b2c-policies"></a>Konfigurera Azure AD B2C principer

Ibland kan synkronisering av TheAccessHub-administrationsverktyget vara begränsad i sin förmåga att hålla sitt tillstånd uppdaterat med Azure AD B2C. Vi kan dra nytta av TheAccessHub Admin Tools API och Azure AD B2C principer för att informera TheAccessHub admin-verktyget om ändringar när de sker. Den här lösningen kräver tekniska kunskaper om [Azure AD B2C anpassade principer](./custom-policy-get-started.md). I nästa avsnitt får du ett exempel på princip steg och ett säkert certifikat för att meddela TheAccessHub administrations verktyg för nya konton i dina Sign-Up anpassade principer.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Skapa en säker autentiseringsuppgift för att anropa TheAccessHub Admin Tools API

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Gå till **System Admin**  >  **admin tools**  >  **API Security**

3. Välj **generera**

4. Kopiera **certifikat lösen ordet**

5. Välj **Hämta** för att hämta klient certifikatet.

6. Följ den här [självstudien](./secure-rest-api.md#https-client-certificate-authentication ) för att lägga till klient certifikatet i Azure AD B2C.

### <a name="retrieve-your-custom-policy-examples"></a>Hämta dina anpassade princip exempel

1. Logga in på TheAccessHub admin-verktyget med autentiseringsuppgifter som du fått av N8 identitet

2. Gå till   >  **administrations verktyg** för system administratör  >  **Azure B2C-principer**

3. Ange din Azure AD B2C klient domän och de två ID: na för identitets erfarenhet från din konfiguration av Identity Experience Framework

4. Välj **Spara**

5. Välj **Hämta** för att få en zip-fil med grundläggande principer som lägger till kunder i TheAccessHub admin-verktyget när kunderna registrerar sig.

6. Följ den här [självstudien](./custom-policy-get-started.md) för att komma igång med att utforma anpassade principer i Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)