---
title: Konfigurera grupp anspråk för program med Azure Active Directory | Microsoft Docs
description: Information om hur du konfigurerar grupp anspråk för användning med Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: bef5942707c1ded22ba82bdb0d945b9fdb23fffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349358"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Konfigurera grupp anspråk för program med Azure Active Directory

Azure Active Directory kan ge en användare grupp medlemskaps information i tokens för användning i program.  Två huvud mönster stöds:

- Grupper som identifieras med ett OID-attribut (Azure Active Directory Object Identifier)
- Grupper som identifieras av sAMAccountName-eller GroupSID-attribut för Active Directory (AD) synkroniserade grupper och användare

> [!IMPORTANT]
> Det finns ett antal varningar att notera för den här funktionen:
>
>- Stöd för användning av sAMAccountName-och säkerhets identifierare (SID) attribut som synkroniseras från lokalt är utformat för att kunna flytta befintliga program från AD FS och andra identitets leverantörer. Grupper som hanteras i Azure AD innehåller inte de attribut som krävs för att generera dessa anspråk.
>- I större organisationer kan antalet grupper som en användare är medlem i överskrida gränsen som Azure Active Directory kommer att läggas till i en token. 150 grupper för en SAML-token och 200 för ett JWT. Detta kan leda till oväntade resultat. Om användarna har ett stort antal grupp medlemskap rekommenderar vi att du använder alternativet för att begränsa grupper som genereras i anspråk till relevanta grupper för programmet.  
>- För ny program utveckling, eller i de fall där programmet kan konfigureras för det, och där stöd för kapslade grupper inte krävs, rekommenderar vi att autentisering i appar baseras på program roller i stället för grupper.  Detta begränsar den mängd information som måste gå till token, är säkrare och skiljer användar tilldelningen från App-konfigurationen.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Gruppera anspråk för program som migrerar från AD FS och andra identitets leverantörer

Många program som kon figurer ATS för att autentisera med AD FS förlitar sig på grupp medlemskaps information i form av Windows AD-Gruppattribut.   De här attributen är gruppen sAMAccountName, som kan vara kvalificerade efter domän namn eller Windows-gruppens säkerhets identifierare (GroupSID).  När programmet är federerad med AD FS, AD FS använder funktionen TokenGroups för att hämta grupp medlemskap för användaren.

En app som har flyttats från AD FS behöver anspråk i samma format. Grupp-och roll anspråk kan genereras från Azure Active Directory som innehåller domänens kvalificerade sAMAccountName eller GroupSID som synkroniserats från Active Directory snarare än gruppens Azure Active Directory objectID.

De format som stöds för grupp anspråk är:

- **Azure Active Directory grupp ObjectID** (tillgängligt för alla grupper)
- **sAMAccountName** (tillgängligt för grupper som synkroniserats från Active Directory)
- **NetbiosDomain\sAMAccountName** (tillgängligt för grupper som synkroniserats från Active Directory)
- **DNSDomainName\sAMAccountName** (tillgängligt för grupper som synkroniserats från Active Directory)
- **Säkerhets identifierare för lokal grupp** (tillgänglig för grupper som synkroniserats från Active Directory)

> [!NOTE]
> SID-attribut för sAMAccountName och lokala grupper är bara tillgängliga för grupp objekt som har synkroniserats från Active Directory.   De är inte tillgängliga på grupper som skapats i Azure Active Directory eller Office365.   Program som kon figurer ATS i Azure Active Directory för att hämta synkroniserade lokala Gruppattribut får bara till synkroniserade grupper.

## <a name="options-for-applications-to-consume-group-information"></a>Alternativ för program att använda grupp information

Program kan anropa slut punkten MS Graph-grupper för att hämta grupp information för den autentiserade användaren. Det här anropet säkerställer att alla grupper som en användare är medlem i är tillgängliga även om det finns ett stort antal grupper.  Grupp uppräkning är sedan oberoende av storleks begränsningarna för token.

Men om ett befintligt program förväntar sig att använda grupp information via anspråk kan Azure Active Directory konfigureras med ett antal olika anspråks format.  Överväg följande alternativ:

- När du använder grupp medlemskap för auktorisering i program är det bättre att använda gruppen ObjectID. Gruppen ObjectID är oföränderlig och unik i Azure Active Directory och är tillgänglig för alla grupper.
- Om du använder den lokala gruppens sAMAccountName för auktorisering använder du kvalificerade domän namn.  Det finns mindre risk för namn konflikt. sAMAccountName kan vara unikt inom en Active Directory domän, men om mer än en Active Directory domän är synkroniserad med en Azure Active Directory-klient finns det en möjlighet för fler än en grupp att ha samma namn.
- Överväg att använda [program roller](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) för att tillhandahålla ett lager av indirigering mellan grupp medlemskapet och programmet.   Programmet fattar sedan interna auktoriseringsbeslut baserat på roll anspråk i token.
- Om programmet har kon figurer ATS för att hämta Gruppattribut som synkroniseras från Active Directory och en grupp inte innehåller dessa attribut tas de inte med i anspråken.
- Grupp anspråk i token inkluderar kapslade grupper, förutom när du använder alternativet för att begränsa grupp anspråk till grupper som tilldelats programmet.  Om en användare är medlem i GroupB och GroupB är medlem i Groupa, kommer grupp anspråk för användaren att innehålla både Groupa och GroupB. När en organisations användare har ett stort antal grupp medlemskap, kan antalet grupper som listas i token växa till token-storleken.  Azure Active Directory begränsar antalet grupper som det genererar i en token till 150 för SAML-kontroller och 200 för JWT.  Om en användare är medlem i ett större antal grupper utelämnas grupperna och en länk till diagrammets slut punkt för att hämta grupp information ingår i stället.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Krav för att använda Gruppattribut synkroniserade från Active Directory

Anspråk på gruppmedlemskap kan genereras i token för valfri grupp om du använder ObjectId-formatet. Om du vill använda grupp anspråk i andra format än gruppen ObjectId måste grupperna synkroniseras från Active Directory med Azure AD Connect.

Det finns två steg för att konfigurera Azure Active Directory att generera grupp namn för Active Directory grupper.

1. **Synkronisera grupp namn från Active Directory** Innan Azure Active Directory kan generera grupp namn eller lokalt grupp-SID i grupp-eller roll anspråk måste de attribut som krävs synkroniseras från Active Directory.  Du måste köra Azure AD Connect version 1.2.70 eller senare.   Tidigare versioner av Azure AD Connect än 1.2.70 kommer att synkronisera grupp objekt från Active Directory, men kommer inte att innehålla de obligatoriska attributen för grupp namn.  Uppgradera till den aktuella versionen.

2. **Konfigurera program registreringen i Azure Active Directory att inkludera grupp anspråk i token** Grupp anspråk kan konfigureras i avsnittet företags program i portalen eller med hjälp av applikations manifestet i avsnittet program registreringar.  Om du vill konfigurera grupp anspråk i program manifestet, se "Konfigurera Azure Active Directory program registrering för Gruppattribut" nedan.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Lägg till grupp anspråk till token för SAML-program med SSO-konfiguration

Om du vill konfigurera grupp anspråk för ett galleri eller ett icke-Galleri SAML-program öppnar du **företags program**, klickar på programmet i listan, väljer **konfiguration för enkel inloggning** och väljer sedan användarattribut **& anspråk**.

Klicka på **Lägg till ett grupp anspråk**  

![Skärm bild som visar sidan "användarattribut &-anspråk" med alternativet "Lägg till ett grupp anspråk" markerat.](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Använd alternativ knapparna för att välja vilka grupper som ska ingå i token

![Skärm bild som visar fönstret "grupp anspråk" med "säkerhets grupper" valt.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Urval | Beskrivning |
|----------|-------------|
| **Alla grupper** | Skapar säkerhets grupper och distributions listor och roller.  |
| **Säkerhetsgrupper** | Genererar säkerhets grupper som användaren är medlem i i gruppen grupp anspråk |
| **Katalogroller** | Om användaren har tilldelats katalog roller genereras de som ett ' wids '-anspråk (grupp anspråk genereras inte) |
| **Grupper som har tilldelats programmet** | Genererar bara de grupper som uttryckligen tilldelas till programmet och användaren är medlem i |

Om du till exempel vill generera alla säkerhets grupper som användaren är medlem i väljer du säkerhets grupper

![Skärm bild som visar fönstret "grupp anspråk" med "säkerhets grupper" valt och den nedrullningsbara menyn källattribut är öppen.](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Om du vill generera grupper med hjälp av Active Directory attribut som synkroniserats från Active Directory i stället för Azure AD objectIDs väljer du det format som krävs från List rutan. Endast grupper som synkroniseras från Active Directory tas med i anspråken.

![Skärm bild som visar den nedrullningsbara menyn "källattribut" öppen.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Om du bara vill generera grupper som är tilldelade till programmet väljer du **grupper tilldelade till programmet**

![Skärm bild som visar fönstret "grupp anspråk" med "grupper tilldelade till programmet" markerat.](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Grupper som har tilldelats till programmet tas med i token.  Andra grupper som användaren är medlem i kommer att utelämnas.  Med det här alternativet inkluderas inte kapslade grupper och användaren måste vara en direkt medlem i gruppen som tilldelats programmet.

Om du vill ändra de grupper som har tilldelats programmet väljer du programmet i listan **företags program** och klickar sedan på **användare och grupper** från programmets vänstra navigerings meny.

Se dokumentet [tilldela en användare eller grupp till en företags app](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) för information om hur du hanterar grupp tilldelningar till program.

### <a name="advanced-options"></a>Avancerade alternativ

Hur grupp anspråk genereras kan ändras av inställningarna under avancerade alternativ

Anpassa grupp anspråkets namn: om det här alternativet väljs kan en annan anspråks typ anges för grupp anspråk.   Ange anspråks typ i fältet namn och det valfria namn området för anspråket i fältet namn område.

![Skärm bild som visar avsnittet "avancerade alternativ" med värdet "anpassa namnet på grupp anspråket" valda och "namn" och "namespace" som anges.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Vissa program kräver att grupp medlemskaps informationen visas i "roll"-anspråket. Du kan välja att generera användarens grupper som roller genom att markera rutan generera grupper av en roll anspråk.

![Skärm bild som visar avsnittet "avancerade alternativ" med "anpassa namnet på grupp anspråket" och "generera grupper som roll anspråk" valda.](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Om alternativet att generera grupp data som roller används, visas endast grupper i roll anspråket.  Alla program roller som användaren är tilldelad visas inte i roll anspråket.

### <a name="edit-the-group-claims-configuration"></a>Redigera grupp anspråks konfigurationen

När en grupp anspråks konfiguration har lagts till i användarattribut & anspråks konfigurationen, blir alternativet för att lägga till ett grupp anspråk nedtonat.  Ändra grupp anspråks konfigurationen genom att klicka på grupp anspråket i listan **Ytterligare anspråk** .

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurera Azure AD Application registration för Gruppattribut

Grupp anspråk kan också konfigureras i avsnittet [valfria anspråk](../../active-directory/develop/active-directory-optional-claims.md) i [applikations manifestet](../../active-directory/develop/reference-app-manifest.md).

1. I portalen->Azure Active Directory-> program registreringar – >Välj program->manifest

2. Aktivera grupp medlemskaps anspråk genom att ändra groupMembershipClaim

Giltiga värden är:

| Urval | Beskrivning |
|----------|-------------|
| **Vissa** | Skapar säkerhets grupper, distributions listor och roller |
| **"SecurityGroup"** | Genererar säkerhets grupper som användaren är medlem i i gruppen grupp anspråk |
| **"DirectoryRole"** | Om användaren har tilldelats katalog roller genereras de som ett ' wids '-anspråk (grupp anspråk genereras inte) |
| **Variabeln applicationgroup** | Genererar bara de grupper som uttryckligen tilldelas till programmet och användaren är medlem i |
| **Alternativet** | Inga grupper returneras. (Det är inte sensetive att ingen fungerar som den ska, och det kan ställas in direkt i applikations manifestet.) |

   Exempel:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Som standard genereras grupp ObjectIDs i grupp anspråks värdet.  Om du vill ändra anspråks värdet så att det innehåller lokala Gruppattribut, eller ändra anspråks typen till roll, använder du OptionalClaims-konfiguration enligt följande:

3. Ange alternativ för grupp namns konfiguration valfria anspråk.

   Om du vill att grupperna i token ska innehålla attributen för lokal AD-grupp anger du vilken typ av tokentyp som tillvals kravet ska tillämpas på i avsnittet valfria anspråk.  Flera typer av token kan visas:

   - idToken för OIDC-ID-token
   - accessToken för OAuth/OIDC-åtkomsttoken
   - Saml2Token för SAML-token.

   > [!NOTE]
   > Saml2Token-typen gäller för både SAML 1.1 och SAML 2.0-format-token

   För varje relevant tokentyp ändrar du grupp anspråk till att använda OptionalClaims-avsnittet i manifestet. OptionalClaims-schemat ser ut så här:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Valfritt anspråks schema | Värde |
   |----------|-------------|
   | **Namn:** | Måste vara "grupper" |
   | **källicensservern** | Används inte. Utelämna eller ange null |
   | **största** | Används inte. Utelämna eller ange falskt |
   | **additionalProperties:** | Lista över ytterligare egenskaper.  Giltiga alternativ är "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   I additionalProperties krävs bara en av "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Om det finns fler än en används den första och andra ignoreras.

   Vissa program kräver grupp information om användaren i roll anspråket.  Om du vill ändra anspråks typen till från ett grupp anspråk till ett roll anspråk lägger du till "emit_as_roles" i ytterligare egenskaper.  Grupp värden genereras i roll anspråket.

   > [!NOTE]
   > Om emit_as_roles används alla program roller som kon figurer ATS som användaren är tilldelad visas inte i roll anspråket

### <a name="examples"></a>Exempel

Generera grupper som grupp namn i OAuth-åtkomsttoken i dnsDomainName\SAMAccountName-format

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Så här genererar du grupp namn som ska returneras i netbiosDomain\samAccountName-format som roll anspråk i SAML-och OIDC-ID-token:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Nästa steg

- [Lägg till auktorisering med grupper & grupp anspråk till en ASP.NET Core webbapp (kod exempel)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Tilldela en användare eller grupp till en företags app](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Konfigurera rollanspråk](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
