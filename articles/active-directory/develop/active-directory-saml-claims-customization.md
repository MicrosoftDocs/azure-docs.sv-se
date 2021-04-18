---
title: Anpassa app-SAML-tokenanspråk
titleSuffix: Microsoft identity platform
description: Lär dig hur du anpassar anspråk som utfärdats av Microsoft Identity Platform i SAML-token för företagsprogram.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 25e737afb524cb8c6f45ac8e99f46a8064ae7855
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598847"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Gör så här: Anpassa anspråk som utfärdats i SAML-token för företagsprogram

Idag stöder Microsoft Identity Platform enkel inloggning (SSO) med de flesta företagsprogram, inklusive både program som är förintegrerade i Azure AD-appgalleriet samt anpassade program. När en användare autentiserar till ett program via Microsoft Identity Platform med hjälp av SAML 2.0-protokollet skickar Microsoft Identity Platform en token till programmet (via en HTTP POST). Sedan validerar och använder programmet token för att logga in användaren i stället för att fråga efter ett användarnamn och lösenord. Dessa SAML-token innehåller information om användaren som kallas *anspråk.*

Ett *anspråk* är information som en identitetsprovider uppger om en användare inuti den token som han eller hon utfärdar för den användaren. I [SAML-token](https://en.wikipedia.org/wiki/SAML_2.0)finns dessa data vanligtvis i SAML-attributsatsen. Användarens unika ID visas vanligtvis i SAML-ämne som också kallas för namnidentifierare.

Som standard utfärdar Microsoft Identity Platform en SAML-token till ditt program som innehåller ett anspråk med ett värde för användarens användarnamn (även kallat användarens huvudnamn) i Azure AD, som kan identifiera `NameIdentifier` användaren unikt. SAML-token innehåller också ytterligare anspråk som innehåller användarens e-postadress, förnamn och efternamn.

Om du vill visa eller redigera anspråk som utfärdats i SAML-token till programmet öppnar du programmet i Azure Portal. Öppna sedan **avsnittet Användarattribut & anspråk.**

![Öppna avsnittet Användarattribut & anspråk i Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråken som utfärdats i SAML-token:

* Programmet kräver att `NameIdentifier` NameID-anspråket är något annat än användarnamnet (eller användarens huvudnamn) som lagras i Azure AD.
* Programmet har skrivits för att kräva en annan uppsättning anspråks-URI:er eller anspråksvärden.

## <a name="editing-nameid"></a>Redigera nameID

Så här redigerar du NameID (namnidentifierarvärde):

1. Öppna sidan **Namnidentifierarvärde.**
1. Välj det attribut eller den transformering som du vill tillämpa på attributet. Du kan också ange det format som du vill att NameID-anspråket ska ha.

   ![Redigera nameID-värdet (namnidentifierare)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-format

Om SAML-begäran innehåller elementet NameIDPolicy med ett visst format kommer Microsoft Identity Platform att respektera formatet i begäran.

Om SAML-begäran inte innehåller något element för NameIDPolicy utfärdar Microsoft Identity Platform NameID med det format som du anger. Om inget format anges använder Microsoft Identity Platform det standardkällformat som är associerat med den valda anspråkskällan.

I **listrutan Välj format för** namnidentifierare kan du välja något av följande alternativ.

| NameID-format | Description |
|---------------|-------------|
| **Standardvärde** | Microsoft Identity Platform använder standardkällformatet. |
| **Permanent** | Microsoft Identity Platform använder Persistent som NameID-format. |
| **EmailAddress** | Microsoft Identity-plattformen använder EmailAddress som NameID-format. |
| **Ospecificerat** | Microsoft Identity Platform använder Unspecified som NameID-format. |

Tillfälligt NameID stöds också, men är inte tillgängligt i listrutan och kan inte konfigureras på Azures sida. Mer information om nameIDPolicy-attributet finns i Enkel Sign-On [SAML-protokoll](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribut

Välj önskad källa för `NameIdentifier` anspråket (eller NameID). Du kan välja bland följande alternativ.

| Name | Beskrivning |
|------|-------------|
| E-post | Användarens e-postadress |
| userprincipalName | Användarens huvudnamn (UPN) |
| onpremisessamaccountname | SAM-kontonamn som har synkroniserats från lokal Azure AD |
| Objectid | Objectid för användaren i Azure AD |
| employeeid | Användarens anställnings-ID |
| Katalogtillägg | Katalogtillägg [som synkroniseras från lokal Active Directory med Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Tilläggsattribut 1–15 | Lokala tilläggsattribut som används för att utöka Azure AD-schemat |

Mer information finns i Tabell [3: Giltiga ID-värden per källa.](reference-claims-mapping-policy-type.md#table-3-valid-id-values-per-source)

Du kan också tilldela alla konstanta (statiska) värden till alla anspråk som du definierar i Azure AD. Följ stegen nedan för att tilldela ett konstant värde:

1. I <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>i avsnittet Användarattribut **& anspråk** klickar du på ikonen  Redigera för att redigera anspråken.
1. Klicka på det anspråk som du vill ändra.
1. Ange konstantvärdet utan citattecken i **attributet Source** enligt din organisation och klicka på **Spara.**

    ![Avsnittet organisationsattribut & anspråk i Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Konstantvärdet visas enligt nedan.

    ![Redigera & anspråk i Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Särskilda anspråk – transformningar

Du kan också använda funktionerna för anspråksomvandling.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffixet från antingen e-postadressen eller användarens huvudnamn. Detta extraherar endast den första delen av användarnamnet som skickas (till exempel "joe_smith" i stället för joe_smith@contoso.com ). |
| **Join()** | Ansluter ett attribut till en verifierad domän. Om det valda användaridentifierarvärdet har en domän extraheras användarnamnet för att lägga till den valda verifierade domänen. Om du till exempel väljer e-postmeddelandet ( ) som användaridentifierarvärde och contoso.onmicrosoft.com som joe_smith@contoso.com verifierad domän, resulterar detta i joe_smith@contoso.onmicrosoft.com . |
| **ToLower()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det valda attributet till versaler. |

## <a name="adding-application-specific-claims"></a>Lägga till programspecifika anspråk

Så här lägger du till programspecifika anspråk:

1. I **Användarattribut & anspråk väljer** du Lägg till nytt **anspråk** för att öppna sidan **Hantera användaranspråk.**
1. Ange **namnet** på anspråken. Värdet behöver inte strikt följa ett URI-mönster enligt SAML-specifikationen. Om du behöver ett URI-mönster kan du placera det i **fältet Namnområde.**
1. Välj **den** källa där anspråket ska hämta dess värde. Du kan välja ett användarattribut från listrutan för källattribut eller tillämpa en transformering på användarattributet innan du skickar det som ett anspråk.

### <a name="claim-transformations"></a>Anspråksomvandlare

Så här tillämpar du en transformering på ett användarattribut:

1. I **Hantera anspråk** väljer du *Transformering* som anspråkskälla för att öppna sidan Hantera **omvandling.**
2. Välj funktionen i listrutan transformering. Beroende på vilken funktion som valts måste du ange parametrar och ett konstant värde som ska utvärderas i transformeringen. Se tabellen nedan för mer information om tillgängliga funktioner.
3. Om du vill tillämpa flera transformationer klickar du på **Lägg till transformering.** Du kan använda högst två transformeringen för ett anspråk. Du kan till exempel först extrahera e-postprefixet för `user.mail` . Gör sedan strängens versaler.

   ![Omvandling av flera anspråk](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Du kan använda följande funktioner för att omvandla anspråk.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffixet från antingen e-postadressen eller användarens huvudnamn. Detta extraherar endast den första delen av användarnamnet som skickas (till exempel "joe_smith" i stället för joe_smith@contoso.com ). |
| **Join()** | Skapar ett nytt värde genom att koppla två attribut. Du kan också använda en avgränsare mellan de två attributen. För NameID-anspråksomvandling är koppling begränsad till en verifierad domän. Om det valda användaridentifierarvärdet har en domän extraheras användarnamnet för att lägga till den valda verifierade domänen. Om du till exempel väljer e-postmeddelandet ( ) som användaridentifierarvärde och contoso.onmicrosoft.com som joe_smith@contoso.com verifierad domän resulterar detta i joe_smith@contoso.onmicrosoft.com . |
| **ToLowercase()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUppercase()** | Konverterar tecknen i det valda attributet till versaler. |
| **Contains()** | Matar ut ett attribut eller en konstant om indata matchar det angivna värdet. Annars kan du ange ytterligare utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens e-postadress om det innehåller domänen " ", annars vill du mata ut användarens @contoso.com huvudnamn. Om du vill göra detta konfigurerar du följande värden:<br/>*Parameter 1 (indata):* user.email<br/>*Värde*: " @contoso.com "<br/>Parameter 2 (utdata): user.email<br/>Parameter 3 (utdata om det inte finns någon matchning): user.userprincipalname |
| **EndWith()** | Matar ut ett attribut eller en konstant om indata slutar med det angivna värdet. Annars kan du ange ytterligare utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens anställnings-ID om medarbetar-ID slutar med "000", annars vill du mata ut ett tilläggsattribut. Om du vill göra detta konfigurerar du följande värden:<br/>*Parameter 1 (indata):* user.employeeid<br/>*Värde:*"000"<br/>Parameter 2 (utdata): user.employeeid<br/>Parameter 3 (utdata om det inte finns någon matchning): user.extensionattribute1 |
| **StartWith()** | Matar ut ett attribut eller en konstant om indata börjar med det angivna värdet. Annars kan du ange ytterligare utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens anställnings-ID om landet/regionen börjar med "USA", annars vill du mata ut ett tilläggsattribut. Om du vill göra detta konfigurerar du följande värden:<br/>*Parameter 1 (indata):* user.country<br/>*Värde:*"US"<br/>Parameter 2 (utdata): user.employeeid<br/>Parameter 3 (utdata om det inte finns någon matchning): user.extensionattribute1 |
| **Extract() – Efter matchning** | Returnerar delsträngen när den matchar det angivna värdet.<br/>Om indatavärdet till exempel är "Finance_BSimon", är det matchande värdet "Finance_", så är anspråksutdata "BSimon". |
| **Extract() – Före matchning** | Returnerar delsträngen tills den matchar det angivna värdet.<br/>Om indatavärdet till exempel är "BSimon_US", är det matchande värdet "_US", så är anspråksutdata "BSimon". |
| **Extract() – Mellan matchning** | Returnerar delsträngen tills den matchar det angivna värdet.<br/>Om indatavärdet till exempel är "Finance_BSimon_US", är det första matchande värdet "Ekonomi", det andra matchande värdet är "US" så är anspråket utdata \_ \_ "BSimon". |
| **ExtractAlpha() – Prefix** | Returnerar prefixet alfabetisk del av strängen.<br/>Om indatavärdet till exempel är "BSimon_123" returneras "BSimon". |
| **ExtractAlpha() – Suffix** | Returnerar suffixet alfabetiskt i strängen.<br/>Om indatavärdet till exempel är "123_Simon" returneras "Simon". |
| **ExtractNumeric() – Prefix** | Returnerar prefixets numeriska del av strängen.<br/>Om indatavärdet till exempel är "123_BSimon" returneras "123". |
| **ExtractNumeric() – Suffix** | Returnerar suffixet numerisk del av strängen.<br/>Om indatavärdet till exempel är "BSimon_123" returneras "123". |
| **IfEmpty()** | Matar ut ett attribut eller en konstant om indata är null eller tom.<br/>Om du till exempel vill mata ut ett attribut som lagras i ett extensionattribute om medarbetar-ID:t för en viss användare är tomt. Om du vill göra detta konfigurerar du följande värden:<br/>Parameter 1(input): user.employeeid<br/>Parameter 2 (utdata): user.extensionattribute1<br/>Parameter 3 (utdata om det inte finns någon matchning): user.employeeid |
| **IfNotEmpty()** | Matar ut ett attribut eller en konstant om indata inte är null eller tom.<br/>Om du till exempel vill mata ut ett attribut som lagras i ett extensionattribute om medarbetar-ID:t för en viss användare inte är tomt. Om du vill göra detta konfigurerar du följande värden:<br/>Parameter 1(input): user.employeeid<br/>Parameter 2 (utdata): user.extensionattribute1 |

Om du behöver ytterligare omvandlingar kan du skicka in din idé i [feedbackforumet i Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) under *Kategorin SaaS-program.*

## <a name="emitting-claims-based-on-conditions"></a>Generera anspråk baserat på villkor

Du kan ange källan för ett anspråk baserat på användartyp och den grupp som användaren tillhör. 

Användartypen kan vara:
- **Alla**: Alla användare har behörighet att komma åt programmet.
- **Medlemmar:** Intern medlem i klientorganisationen
- **Alla gäster:** Användaren tas över från en extern organisation med eller utan Azure AD.
- **AAD-gäster:** Gästanvändaren tillhör en annan organisation som använder Azure AD.
- **Externa gäster:** Gästanvändaren tillhör en extern organisation som inte har Azure AD.


Ett scenario där detta är användbart är när källan för ett anspråk är annorlunda för en gäst och en anställd som har åtkomst till ett program. Du kanske vill ange att NameID kommer från user.email om användaren är en medarbetare, men om användaren är en gäst kommer NameID från user.extensionattribute1.

Så här lägger du till ett anspråksvillkor:

1. I **Hantera anspråk** expanderar du Anspråksvillkor.
2. Välj användartyp.
3. Välj de grupper som användaren ska tillhöra. Du kan välja upp till 50 unika grupper för alla anspråk för ett visst program. 
4. Välj **den** källa där anspråket ska hämta dess värde. Du kan välja ett användarattribut från listrutan för källattribut eller tillämpa en transformering på användarattributet innan du skickar det som ett anspråk.

Det är viktigt i vilken ordning du lägger till villkoren. Azure AD utvärderar villkoren uppifrån och ned för att avgöra vilket värde som ska generera i anspråket. Det sista värdet som matchar uttrycket genereras i anspråket.

Till exempel är Britta Simon en gästanvändare i Contoso-klientorganisationen. Hon tillhör en annan organisation som också använder Azure AD. Enligt konfigurationen nedan för Fabrikam-programmet utvärderar Microsoft Identity Platform villkoren enligt nedan när Britta försöker logga in på Fabrikam.

Först verifierar Microsoft Identity Platform om Brittas användartyp är `All guests` . Eftersom detta är sant tilldelar Microsoft Identity Platform källan för anspråket till `user.extensionattribute1` . För det andra verifierar Microsoft Identity Platform om Brittas användartyp är , eftersom detta också är sant, så tilldelar Microsoft Identity Platform källan för `AAD guests` anspråket till `user.mail` . Slutligen genereras anspråket med värdet `user.mail` för Britta.

![Villkorsstyrd konfiguration av anspråk](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Nästa steg

* [Programhantering i Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurera enkel inloggning för program som inte finns i Azure AD-programgalleriet](../manage-apps/configure-saml-single-sign-on.md)
* [Felsöka SAML-baserad enkel inloggning](../manage-apps/debug-saml-sso-issues.md)
