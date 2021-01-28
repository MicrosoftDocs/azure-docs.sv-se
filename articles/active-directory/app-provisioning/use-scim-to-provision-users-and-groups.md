---
title: Självstudie – utveckla en SCIM-slutpunkt för användar etablering till appar från Azure AD
description: System för SCIM (Cross-Domain Identity Management) standardiserar automatisk användar etablering. I den här självstudien lär du dig att utveckla en SCIM-slutpunkt, integrera ditt SCIM-API med Azure Active Directory och börja automatisera etableringen av användare och grupper i dina moln program.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e14bed3158fac1f8bed15f913dc8b7eddbbb96
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985962"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Självstudie: utveckla och planera etablering för en SCIM-slutpunkt

Som programutvecklare kan du använda systemet för användar hanterings-API: et för SCIM (Cross-Domain Identity Management) för att aktivera automatisk etablering av användare och grupper mellan ditt program och Azure AD. Den här artikeln beskriver hur du skapar en SCIM-slutpunkt och integrerar med Azure AD Provisioning-tjänsten. SCIM-specifikationen innehåller ett gemensamt användar schema för etablering. När det används tillsammans med Federations standarder som SAML eller OpenID Connect ger SCIM administratörer en heltäckande lösning för åtkomst hantering från slut punkt till slut punkt.

SCIM är en standardiserad definition av två slut punkter: en `/Users` slut punkt och en `/Groups` slut punkt. Den använder vanliga REST-verb för att skapa, uppdatera och ta bort objekt och ett fördefinierat schema för vanliga attribut, t. ex. grupp namn, användar namn, förnamn, efter namn och e-post. Appar som erbjuder en SCIM 2,0-REST API kan minska eller eliminera den smärta som fungerar med ett eget användar hanterings-API. Till exempel vet alla kompatibla SCIM-klienter hur du gör en HTTP POST av ett JSON-objekt till `/Users` slut punkten för att skapa en ny användar post. I stället för att behöva ett något annorlunda API för samma grundläggande åtgärder kan appar som uppfyller SCIM-standarden omedelbart dra nytta av befintliga klienter, verktyg och kod. 

![Etablering från Azure AD till en app med SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standardschemat för användar objekt och REST API: er för hantering som definieras i SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) gör att identitets leverantörer och appar enkelt kan integreras med varandra. Programutvecklare som skapar en SCIM-slutpunkt kan integreras med alla SCIM-kompatibla klienter utan att behöva göra anpassade arbeten.

Att automatisera etableringen av ett program kräver att du skapar och integrerar en SCIM-slutpunkt med Azure AD SCIM-klienten. Utför följande steg för att starta etableringen av användare och grupper i ditt program. 
    
  * **[Steg 1: utforma användar-och grupp schemat.](#step-1-design-your-user-and-group-schema)** Identifiera de objekt och attribut som programmet behöver och Bestäm hur de mappar till användar-och grupp schema som stöds av Azure AD SCIM-implementeringen.

  * **[Steg 2: förstå Azure AD SCIM-implementeringen.](#step-2-understand-the-azure-ad-scim-implementation)** Förstå hur Azure AD SCIM-klienten implementeras och utforma SCIM-protokollets hantering och svar.

  * **[Steg 3: Bygg en SCIM-slutpunkt.](#step-3-build-a-scim-endpoint)** En slut punkt måste vara SCIM 2,0-kompatibel för integrering med Azure AD Provisioning-tjänsten. Som ett alternativ kan du använda CLI-bibliotek (Common Language Infrastructure) och kod exempel för att bygga din slut punkt. Dessa exempel är endast för referens och testning. Vi rekommenderar att du kodar din produktions-app för att ta ett beroende på dem.

  * **[Steg 4: integrera din SCIM-slutpunkt med Azure AD SCIM-klienten.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Om din organisation använder ett program från tredje part som implementerar profilen för SCIM 2,0 som Azure AD stöder, kan du börja automatisera både etablering och avetablering av användare och grupper direkt.

  * **[Steg 5: publicera ditt program i Azure AD-programgalleriet.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Gör det enkelt för kunderna att upptäcka ditt program och enkelt konfigurera etablering. 

![Steg för att integrera en SCIM-slutpunkt med Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Steg 1: utforma användar-och grupp schema

Varje program kräver olika attribut för att skapa en användare eller grupp. Starta din integrering genom att identifiera de objekt (användare, grupper) och attribut (namn, chef, befattning osv.) som programmet kräver. SCIM-standarden definierar ett schema för hantering av användare och grupper. Kärn användar schemat kräver bara tre attribut: **ID** (Service Provider Defined Identifier), **externalId** (identifierare för den klient som definieras) och **meta** (skrivskyddade metadata som underhålls av tjänst leverantören). Alla andra attribut är valfria. Förutom kärn användar schemat definierar SCIM-standarden ett företags användar tillägg och en modell för utökning av användar schemat för att uppfylla ditt programs behov. Om ditt program till exempel kräver en användares chef kan du använda användar schemat för företag för att samla in användarens chef och kärn schemat för att samla in användarens e-post. Skapa schemat genom att följa stegen nedan:
  1. Lista de attribut som programmet kräver. Det kan vara bra att dela upp dina krav i de attribut som behövs för autentisering (t. ex. loginName och e-post), attribut som behövs för att hantera användarens livs cykel (t. ex. status/aktiv) och andra attribut som behövs för att ditt program ska fungera (t. ex. chef, tagg).
  2. Kontrol lera om dessa attribut redan har definierats i kärn användar schema eller användar schema för företag. Om du har attribut som du behöver och inte omfattas av kärn-eller företags användar scheman, måste du definiera ett tillägg till användar schemat som täcker de attribut som du behöver. I exemplet nedan har vi lagt till ett tillägg till användaren för att tillåta att en "tagg" skapas för en användare. Det är bäst att börja med bara kärn-och företags användar scheman och utöka till ytterligare anpassade scheman senare.  
  3. Mappa SCIM-attributen till användarattribut i Azure AD. Om något av de attribut som du har definierat i SCIM-slutpunkten inte har en tydlig motsvarighet i användar schemat för Azure AD, så är det en stor chans att data inte lagras på användarobjektet på de flesta klienter. Överväg om det här attributet kan vara valfritt för att skapa en användare. Om attributet är kritiskt för att ditt program ska fungera, vägleder klient organisationen att du utökar sitt schema eller använder ett attribut för tillägg som visas nedan för egenskapen "Taggar".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabell 1: disponera de attribut som du behöver 
| Steg 1: Bestäm vilka attribut appen kräver| Steg 2: mappa appens krav till SCIM standard| Steg 3: mappa SCIM-attribut till Azure AD-attributen|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|förnamn|
|lastName|namn. lastName|lastName|
|workMail|e-postmeddelanden [typ EQ "Work"]. värde|E-post|
|manager|manager|manager|
|tagg|urn: IETF: params: scim: schemas: tillägg: 2.0: CustomExtension: tagg|extensionAttribute1|
|status|aktiv|isSoftDeleted (beräknat värde lagras inte på användare)|

Det schema som definierats ovan ska representeras med JSON-nyttolasten nedan. Observera att förutom de attribut som krävs för programmet innehåller JSON-representationen obligatoriska `id` , `externalId` -och- `meta` attribut.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```

### <a name="table-2-default-user-attribute-mapping"></a>Tabell 2: mappning av standardattribut för användare
Du kan sedan använda tabellen nedan för att förstå hur attributen som programmet kräver kan mappas till ett attribut i Azure AD och SCIM RFC. Du kan [Anpassa](customize-application-attributes.md) hur attribut mappas mellan Azure AD och din scim-slutpunkt. Observera att du inte behöver stödja både användare och grupper eller alla attribut som visas nedan. De är en referens för hur attribut i Azure AD ofta mappas till egenskaper i SCIM-protokollet. 

| Azure Active Directory användare | "urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: User" |
| --- | --- |
| IsSoftDeleted |aktiv |
|avdelning|urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|
| displayName |displayName |
|Anställnings|urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers [Type EQ "fax"]. värde |
| förnamn |name.givenName |
| jobTitle |title |
| e-post |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager |
| mobil |phoneNumbers[type eq "mobile"].value |
| postalCode |adresser [Type EQ "Work"]. Postnr |
| Proxy-adresser |e-postmeddelanden [Type EQ "other"]. Värde |
| fysisk leverans – OfficeName |adresser [Type EQ "other"]. Formatera |
| streetAddress |adresser [Type EQ "Work"]. streetAddress |
| surname |name.familyName |
| telefonnummer |phoneNumbers[type eq "work"].value |
| User-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabell 3: mappning av standardmapp-attribut

| Azure Active Directory grupp | urn: IETF: params: scim: schemas: Core: 2.0: grupp |
| --- | --- |
| displayName |displayName |
| e-post |emails[type eq "work"].value |
| mailNickname |displayName |
| medlemmar |medlemmar |
| objectId |externalId |
| proxyAddresses |e-postmeddelanden [Type EQ "other"]. Värde |

Det finns flera slut punkter definierade i SCIM RFC. Du kan komma igång med/User-slutpunkten och sedan expandera därifrån. /Schemas-slutpunkten är användbar när du använder anpassade attribut eller om schemat ändras ofta. Det gör det möjligt för en klient att hämta det mest uppdaterade schemat automatiskt. /Bulk-slutpunkten är särskilt användbar när du har stöd för grupper. Tabellen nedan beskriver de olika slut punkter som definieras i SCIM-standarden.
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabell 4: Bestäm de slut punkter som du vill utveckla
|ENDPOINT|BESKRIVNING|
|--|--|
|/User|Utföra CRUD-åtgärder på ett användar objekt.|
|/Group|Utföra CRUD-åtgärder på ett grupp objekt.|
|/ServiceProviderConfig|Innehåller information om funktionerna i SCIM-standarden som stöds, till exempel de resurser som stöds och autentiseringsmetoden.|
|/ResourceTypes|Anger metadata för varje resurs|
|/Schemas|De attribut som stöds av varje klient och tjänst leverantör kan variera. En tjänst leverantör kan inkludera `name` , `title` och `emails` en annan tjänst leverantör använder `name` , `title` och `phoneNumbers` . Schema slut punkten gör det möjligt att identifiera de attribut som stöds.|
|/Bulk|Med Mass åtgärder kan du utföra åtgärder på en stor mängd resurs objekt i en enda åtgärd (t. ex. Uppdatera medlemskap för en stor grupp).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Steg 2: förstå Azure AD SCIM-implementeringen
> [!IMPORTANT]
> Beteendet för Azure AD SCIM-implementeringen uppdaterades senast den 18 december 2018. Information om vad som har ändrats finns i [SCIM 2,0 Protocol Compliance of the Azure AD User Provisioning-tjänsten](application-provisioning-config-problem-scim-compatibility.md).

Om du skapar ett program som stöder ett SCIM 2,0-API för användar hantering beskrivs i det här avsnittet hur du implementerar Azure AD SCIM-klienten. Det visar också hur du kan modellera hantering och svar för SCIM-protokoll begär Anden. När du har implementerat din SCIM-slutpunkt kan du testa den genom att följa anvisningarna som beskrivs i föregående avsnitt.

I [SCIM 2,0-protokoll specifikationen](http://www.simplecloud.info/#Specification)måste ditt program uppfylla följande krav:

* Har stöd för att skapa användare och eventuellt även grupper, enligt avsnitt [3,3 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Stöder ändring av användare eller grupper med PATCH-begäranden enligt [avsnittet 3.5.2 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.5.2). Stöd säkerställer att grupper och användare är etablerade på ett effektivt sätt. 
* Stöder hämtning av en känd resurs för en användare eller grupp som skapats tidigare, enligt [avsnittet 3.4.1 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Har stöd för att skicka frågor till användare eller grupper enligt avsnittet [3.4.2 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Som standard hämtas användare av och efter `id` frågas av deras `username` och `externalId` , och grupper efter frågas av `displayName` .  
* Har stöd för att skicka frågor till användare efter ID och chef, enligt avsnittet 3.4.2 i SCIM-protokollet.  
* Har stöd för att skicka frågor till grupper efter ID och per medlem, enligt avsnittet 3.4.2 i SCIM-protokollet.  
* Stöder filtret [excludedAttributes = members](#get-group) när du frågar grupp resursen, enligt avsnitt 3.4.2.5 i scim-protokollet.
* Accepterar en enda Bearer-token för autentisering och auktorisering av Azure AD till ditt program.
* Stöder mjuk borttagning av en användare `active=false` och återställning av användaren `active=true` (användarobjektet ska returneras i en begäran oavsett om användaren är aktiv) eller inte. Den enda gång som användaren inte ska returneras är när den är hårt borttagen från programmet. 

Följ dessa allmänna rikt linjer när du implementerar en SCIM-slutpunkt för att säkerställa kompatibilitet med Azure AD:

* `id` är en obligatorisk egenskap för alla resurser. Varje svar som returnerar en resurs måste se till att varje resurs har den här egenskapen, förutom `ListResponse` med noll medlemmar.
* Svar på en fråga/filter-begäran ska alltid vara en `ListResponse` .
* Grupper är valfria, men stöds endast om SCIM-implementeringen stöder PATCH-begäranden.
* Det är inte nödvändigt att inkludera hela resursen i KORRIGERINGs svaret.
* Microsoft Azure AD använder endast följande operatorer:  
    - `eq`
    - `and`
* Kräv inte Skift läges känslig matchning på strukturella element i SCIM, i synnerhet KORRIGERINGs `op` Åtgärds värden, enligt definitionen i https://tools.ietf.org/html/rfc7644#section-3.5.2 . Azure AD avger värdena för "OP" som `Add` , `Replace` och `Remove` .
* Microsoft Azure AD gör begär Anden att hämta en slumpmässig användare och grupp för att säkerställa att slut punkten och autentiseringsuppgifterna är giltiga. Det sker också som en del av **test anslutnings** flödet i [Azure Portal](https://portal.azure.com). 
* Attributet som resurserna kan frågas om på ska anges som ett matchande attribut i programmet i [Azure Portal](https://portal.azure.com). Mer information finns i [Anpassa mappningar för användar etablerings attribut](customize-application-attributes.md)
* Stöd för HTTPS på din SCIM-slutpunkt

### <a name="user-provisioning-and-deprovisioning"></a>Användar etablering och avetablering

Följande bild visar de meddelanden som Azure Active Directory skickar till en SCIM-tjänst för att hantera livs cykeln för en användare i ditt programs identitets lager.  

![Visar användar etablering och avetablerings ordning](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Användar etablering och avetablerings ordning*

### <a name="group-provisioning-and-deprovisioning"></a>Grupp etablering och avetablering

Grupp etablering och avetablering är valfria. När det implementeras och aktive ras visar följande bild de meddelanden som Azure AD skickar till en SCIM-tjänst för att hantera livs cykeln för en grupp i ditt programs identitets lager.  Dessa meddelanden skiljer sig från meddelanden om användare på två sätt:

* Begär Anden om att hämta grupper anger att attributet members ska uteslutas från alla resurser som tillhandahålls som svar på begäran.  
* Begär Anden för att avgöra om ett referensvärde har ett visst värde, begär Anden om medlemmarnas attribut.  

![Visar grupp etablering och avetablerings ordning](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Grupp etablering och avetablerings ordning*

### <a name="scim-protocol-requests-and-responses"></a>Begär Anden och svar för SCIM-protokoll
Det här avsnittet innehåller exempel på SCIM-begäranden som har genererats av Azure AD SCIM-klienten och exempel på förväntade svar. För bästa resultat bör du koda appen för att hantera dessa begär anden i det här formatet och generera förväntade svar.

> [!IMPORTANT]
> Information om hur och när Azure AD-tjänsten för användar etablering avger de åtgärder som beskrivs nedan finns i avsnittet [etablerings cykler: initial och stegvisa](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [anvisningar för hur etablering fungerar](how-provisioning-works.md).

[Användar åtgärder](#user-operations)
  - [Skapa användare](#create-user) ([](#request)  /  [svar](#response)på begäran)
  - [Hämta användare](#get-user) ([](#request-1)  /  [svar](#response-1)på begäran)
  - [Hämta användare efter fråga](#get-user-by-query) ([begär](#request-2)  /  [svar](#response-2))
  - [Hämta användare efter fråga – noll resultat](#get-user-by-query---zero-results) ([begär](#request-3)  /  [svar](#response-3))
  - [Uppdatera användare [Egenskaper för flera värden]](#update-user-multi-valued-properties) ([begär](#request-4)  /  [svar](#response-4))
  - [Uppdatera användare [Egenskaper för enstaka värde]](#update-user-single-valued-properties) ([begär](#request-5)  /  [svar](#response-5)) 
  - [Inaktivera användare](#disable-user) ([](#request-14)  /  [svar](#response-14)på begäran)
  - [Ta bort användare](#delete-user) (svar på[begäran](#request-6)  /  [](#response-6))


[Grupp åtgärder](#group-operations)
  - [Skapa grupp](#create-group) ([begär](#request-7)  /  [svar](#response-7))
  - [Hämta grupp](#get-group) ([Request](#request-8)  /  [Response](#response-8))
  - [Hämta grupp efter DisplayName](#get-group-by-displayname) ([](#request-9)  /  [svar](#response-9)på begäran)
  - [Uppdaterings grupp [attribut för icke-medlem]](#update-group-non-member-attributes) ([begär](#request-10)  /  [svar](#response-10))
  - [Uppdatera grupp [Lägg till medlemmar]](#update-group-add-members) ([begär](#request-11)  /  [svar](#response-11))
  - [Uppdaterings grupp [ta bort medlemmar]](#update-group-remove-members) (svar på[begäran](#request-12)  /  [](#response-12))
  - [Ta bort grupp](#delete-group) ([begär](#request-13)  /  [svar](#response-13))

### <a name="user-operations"></a>Användar åtgärder

* Användare kan frågas efter `userName` eller `email[type eq "work"]` attribut.  

#### <a name="create-user"></a>Skapa användare

###### <a name="request"></a>Förfrågan

*POST-/users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Svarsåtgärder

*HTTP/1.1 201 har skapats*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Hämta användare

###### <a name="request"></a><a name="request-1"></a>Förfrågan
*Hämta/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Svar (användaren hittades)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Förfrågan
*Hämta/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Svar (användaren hittades inte. Observera att detalj nivån inte är obligatorisk, endast status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Hämta användare efter fråga

##### <a name="request"></a><a name="request-2"></a>Förfrågan

*Hämta/Users? filter = userName EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Svarsåtgärder

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Hämta användare från fråga – noll resultat

##### <a name="request"></a><a name="request-3"></a>Förfrågan

*Hämta/Users? filter = userName EQ "obefintlig användare"*

##### <a name="response"></a><a name="response-3"></a>Svarsåtgärder

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Uppdatera användare [Egenskaper för flera värden]

##### <a name="request"></a><a name="request-4"></a>Förfrågan

*KORRIGERING/Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Svarsåtgärder

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Uppdatera användare [Egenskaper för enstaka värde]

##### <a name="request"></a><a name="request-5"></a>Förfrågan

*KORRIGERING/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Svarsåtgärder

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Inaktivera användare

##### <a name="request"></a><a name="request-14"></a>Förfrågan

*KORRIGERING/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Svarsåtgärder

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Ta bort användare

##### <a name="request"></a><a name="request-6"></a>Förfrågan

*TA bort/users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Svarsåtgärder

*HTTP/1.1 204 inget innehåll*

### <a name="group-operations"></a>Grupp åtgärder

* Grupper ska alltid skapas med en lista med tomma medlemmar.
* Det går att fråga efter attribut med grupper `displayName` .
* Uppdateringen av grupp PATCH-begäran ska ge ett *HTTP 204-innehåll* i svaret. Att returnera en brödtext med en lista över alla medlemmar är inte lämpligt.
* Det är inte nödvändigt att stödja att returnera alla medlemmar i gruppen.

#### <a name="create-group"></a>Skapa grupp

##### <a name="request"></a><a name="request-7"></a>Förfrågan

*PUBLICERA/Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Svarsåtgärder

*HTTP/1.1 201 har skapats*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Hämta grupp

##### <a name="request"></a><a name="request-8"></a>Förfrågan

*Hämta/Groups/40734ae655284ad3abcc? excludedAttributes = medlemmar HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Svarsåtgärder
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Hämta grupp efter displayName

##### <a name="request"></a><a name="request-9"></a>Förfrågan
*Hämta/Groups? excludedAttributes = members&filter = displayName EQ "HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Svarsåtgärder

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Uppdaterings grupp [attribut för icke-medlem]

##### <a name="request"></a><a name="request-10"></a>Förfrågan

*KORRIGERING/Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Svarsåtgärder

*HTTP/1.1 204 inget innehåll*

### <a name="update-group-add-members"></a>Uppdatera grupp [Lägg till medlemmar]

##### <a name="request"></a><a name="request-11"></a>Förfrågan

*KORRIGERING/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Svarsåtgärder

*HTTP/1.1 204 inget innehåll*

#### <a name="update-group-remove-members"></a>Uppdaterings grupp [ta bort medlemmar]

##### <a name="request"></a><a name="request-12"></a>Förfrågan

*KORRIGERING/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Svarsåtgärder

*HTTP/1.1 204 inget innehåll*

#### <a name="delete-group"></a>Ta bort grupp

##### <a name="request"></a><a name="request-13"></a>Förfrågan

*TA bort/Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Svarsåtgärder

*HTTP/1.1 204 inget innehåll*

### <a name="security-requirements"></a>Säkerhetskrav
**TLS-protokoll versioner**

De enda acceptabla TLS-protokollen är TLS 1,2 och TLS 1,3. Inga andra versioner av TLS tillåts. Ingen version av SSL tillåts. 
- RSA-nycklar måste vara minst 2 048 bitar.
- ECC-nycklar måste vara minst 256 bitar, genererade med en godkänd Elliptic-kurva


**Nyckel längder**

Alla tjänster måste använda X. 509-certifikat som genererats med krypterings nycklar av tillräcklig längd, vilket innebär:

**Chiffersviter**

Alla tjänster måste konfigureras för att använda följande chiffersviter, i den ordning som anges nedan. Observera att om du bara har ett RSA-certifikat, har ECDSA-chiffersviter inte någon påverkan. </br>

Minsta fält för TLS 1,2 cipher-paket:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP-intervall
Azure AD Provisioning-tjänsten fungerar för närvarande under IP-intervallen för AzureActiveDirectory som visas [här](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all). Du kan lägga till IP-intervallen som visas i AzureActiveDirectory-taggen för att tillåta trafik från Azure AD Provisioning-tjänsten till ditt program. Observera att du måste granska listan över IP-adressintervall noggrant för beräknade adresser. En adress som "40.126.25.32" kunde representeras i listan över IP-adressintervall som "40.126.0.0/18". Du kan också hämta listan över IP-adressintervall via programmering med följande [API](/rest/api/virtualnetwork/servicetags/list).

## <a name="step-3-build-a-scim-endpoint"></a>Steg 3: Bygg en SCIM-slutpunkt

Nu när du har utformat schemat och förstått Azure AD SCIM-implementeringen kan du komma igång med att utveckla din SCIM-slutpunkt. I stället för att börja från början och skapa implementeringen helt och hållet, kan du lita på ett antal SCIM-bibliotek med öppen källkod som publicerats av SCIM-communityn.

Information om hur du skapar en SCIM-slutpunkt inklusive exempel finns i [utveckla ett exempel på en scim-slutpunkt](use-scim-to-build-users-and-groups-endpoints.md).

Exempel på .NET Core- [referensen](https://aka.ms/SCIMReferenceCode) med öppen källkod som publicerats av Azure AD Provisioning-teamet är en sådan resurs som kan leda till att du börjar utveckla. När du har skapat din SCIM-slutpunkt ska du testa den. Du kan använda samlingen med [Postman-tester](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) som ingår i referens koden eller köra genom exempel begär Anden/svar som anges [ovan](#user-operations).  

   > [!Note]
   > Referens koden är avsedd att hjälpa dig att komma igång med att skapa din SCIM-slutpunkt och tillhandahålls "i befintligt skick". Bidrag från communityn är välkommen att hjälpa till att bygga och underhålla koden.

Lösningen består av två projekt, _Microsoft. scim_ och _Microsoft. scim. WebHostSample_.

_Microsoft. scim_ -projektet är det bibliotek som definierar komponenterna i webb tjänsten som följer scim-specifikationen. Den deklarerar gränssnittet _Microsoft. scim. IProvider_ och begär Anden översätts till anrop till providerns metoder, som skulle vara programmerade att köras i ett identitets lager.

![Detalj nivå: en begäran har översatts till anrop till providerns metoder](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft. scim. WebHostSample_ -projektet är ett Visual Studio ASP.net Core-webbprogram, baserat på den _tomma_ mallen. Detta gör att exempel koden kan distribueras som fristående, som finns i behållare eller inom Internet Information Services. Det implementerar också gränssnittet _Microsoft. scim. IProvider_ och använder klasserna i minnet som ett exempel på identitets lager.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Skapa en anpassad SCIM-slutpunkt

SCIM-tjänsten måste ha ett certifikat för HTTP-adress och serverautentisering som rot certifikat utfärdaren är ett av följande namn:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* Förtroende
* GlobalSign
* Go-Daddy
* VeriSign
* WoSign
* DST-rot CA-x3

I .NET Core SDK ingår ett HTTPS-utvecklings certifikat som kan användas under utvecklingen, certifikatet installeras som en del av upplevelsen för första körningen. Beroende på hur du kör ASP.NET Core webb program lyssnar den på en annan port:

* Microsoft. SCIM. WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Om du vill ha mer information om HTTPS i ASP.NET Core använder du följande länk: Använd [https i ASP.net Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Hantering av slut punkts autentisering

Begär Anden från Azure Active Directory innehåller en OAuth 2,0 Bearer-token. Alla tjänster som tar emot begäran ska autentisera utfärdaren som Azure Active Directory för den förväntade Azure Active Directory klienten.

I token identifieras utfärdaren av ett ISS-anspråk, t. ex `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` .. I det här exemplet är bas adressen för anspråk svärdet, `https://sts.windows.net` identifierar Azure Active Directory som utfärdare, medan det relativa adress segmentet _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_ är en unik identifierare för den Azure Active Directory klient som token utfärdades för.

Mål gruppen för token är programmets mall-ID för programmet i galleriet. varje program som registreras i en enskild klient kan få samma `iss` anspråk med scim-begäranden. Programmets mall-ID för alla anpassade appar är _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_. Den token som genereras av Azure AD Provisioning-tjänsten bör endast användas för testning. Den bör inte användas i produktions miljöer.

I exempel koden autentiseras begär Anden med hjälp av paketet Microsoft. AspNetCore. Authentication. JwtBearer. Följande kod framtvingar att begär anden till någon av tjänstens slut punkter autentiseras med hjälp av Bearer-token som utfärdats av Azure Active Directory för en angiven klient organisation:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

En Bearer-token krävs också för att använda de tillhandahållna [Postman-testen](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) och utföra lokal fel sökning med localhost. Exempel koden använder ASP.NET Core miljöer för att ändra autentiseringsinställningarna under utvecklings fasen och aktivera Använd en självsignerad token.

Om du vill ha mer information om flera miljöer i ASP.NET Core använder du följande länk: [Använd flera miljöer i ASP.net Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Följande kod framtvingar att begär anden till någon av tjänstens slut punkter autentiseras med hjälp av en Bearer-token signerad med en anpassad nyckel:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Skicka en GET-begäran till token-styrenheten för att få en giltig Bearer-token är metoden _GenerateJSONWebToken_ ansvarig för att skapa en token som matchar parametrarna som har kon figurer ATS för utveckling:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Hantera etablering och avetablering av användare

***Exempel 1. Fråga tjänsten om matchande användare** _

Azure Active Directory skickar en fråga till tjänsten för en användare med ett `externalId` attributvärde som matchar värdet för attributet smek namn för en användare i Azure AD. Frågan uttrycks som en Hypertext Transfer Protocol (HTTP)-begäran, till exempel det här exemplet, där jyoung är ett exempel på ett smek namn för en användare i Azure Active Directory.

>[!NOTE]
> Detta är endast ett exempel. Alla användare får inte ett smek namn-attribut och värdet som en användare har kanske inte är unikt i katalogen. Dessutom kan attributet som används för matchning (som i det här fallet `externalId` ) konfigureras i [mappningar för Azure AD-attribut](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

I exempel koden översätts begäran till ett anrop till QueryAsync-metoden för tjänstens Provider. Här är signaturen för den metoden: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

I exempel frågan, för en användare med ett angivet värde för `externalId` attributet, är värdena för argumenten som skickas till metoden QueryAsync:

komponentparametrar. AlternateFilters. Count: 1
* komponentparametrar. AlternateFilters. ElementAt (0). AttributePath: "externalId"
* komponentparametrar. AlternateFilters. ElementAt (0). ComparisonOperator: ComparisonOperator. equals
* komponentparametrar. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"

***Exempel 2. Etablera en användare** _

Om svaret på en fråga till webb tjänsten för en användare med ett `externalId` attributvärde som matchar värdet för ett värde för en användare inte returnerar några användare, kommer Azure Active Directory begär Anden som tjänsten etablerar en användare som motsvarar den som finns i Azure Active Directory.  Här är ett exempel på en sådan begäran: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

I exempel koden översätts begäran till ett anrop till CreateAsync-metoden för tjänstens Provider. Här är signaturen för den metoden: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

I en begäran om att etablera en användare är värdet för resurs argumentet en instans av klassen Microsoft. SCIM. Core2EnterpriseUser som definieras i biblioteket Microsoft. SCIM. schemas.  Om begäran att etablera användaren lyckas förväntas implementeringen av metoden returnera en instans av klassen Microsoft. SCIM. Core2EnterpriseUser med värdet för egenskapen Identifier inställd på den unika identifieraren för den nyligen etablerade användaren.  

_*_Exempel 3. Fråga efter användarens aktuella tillstånd_*_ 

Om du vill uppdatera en användare som är känd att finnas i ett identitets lager som har en SCIM, kan Azure Active Directory fortsätta genom att begära det aktuella status för användaren från tjänsten med en begäran, till exempel: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

I exempel koden översätts begäran till ett anrop till RetrieveAsync-metoden för tjänstens Provider. Här är signaturen för den metoden: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

I exemplet på en begäran om att hämta det aktuella status för en användare är värdena för egenskaperna för objektet som anges som värde för argumentet Parameters följande: 
  
_ Identifierare: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: User"

***Exempel 4. Fråga värdet för ett referens-attribut som ska uppdateras** _ 

Om ett referens-attribut ska uppdateras, så Azure Active Directory frågar tjänsten om det aktuella värdet för referens-attributet i identitets arkivet som är baserat på tjänsten redan matchar värdet för attributet i Azure Active Directory. För användare är det enda attributet där det aktuella värdet frågas på det här sättet är attributet Manager. Här är ett exempel på en begäran om att avgöra om attributet Manager för ett användar objekt har ett visst värde: i exempel koden översätts begäran till ett anrop till QueryAsync-metoden för tjänstens Provider. Värdet för egenskaperna för objektet som anges som värde för parameter argumentet är följande: 
  
komponentparametrar. AlternateFilters. Count: 2
* komponentparametrar. AlternateFilters. ElementAt (x). AttributePath: "ID"
* komponentparametrar. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator. equals
* komponentparametrar. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* komponentparametrar. AlternateFilters. ElementAt (y). AttributePath: "chef"
* komponentparametrar. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator. equals
* komponentparametrar. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* komponentparametrar. RequestedAttributePaths. ElementAt (0): "ID"
* komponentparametrar. SchemaIdentifier: "urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: User"

Här kan värdet för index x vara 0 och värdet för indexet y kan vara 1 eller värdet för x kan vara 1 och värdet för y kan vara 0, beroende på ordningen på uttrycken för filter fråge parametern.   

***Exempel 5. Begär från Azure AD till en SCIM-tjänst för att uppdatera en användare** _ 

Här är ett exempel på en begäran från Azure Active Directory till en SCIM-tjänst för att uppdatera en användare: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

I exempel koden översätts begäran till ett anrop till UpdateAsync-metoden för tjänstens Provider. Här är signaturen för den metoden: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

I exemplet på en begäran om att uppdatera en användare har det objekt som angavs som värde för argumentet patch följande egenskaps värden: 
  
_ ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: User"
* (PatchRequest som PatchRequest2). Åtgärder. Count: 1
* (PatchRequest som PatchRequest2). Operations. ElementAt (0). OperationName: OperationName. Add
* (PatchRequest som PatchRequest2). Operations. ElementAt (0). Path. AttributePath: "Manager"
* (PatchRequest som PatchRequest2). Operations. ElementAt (0). Värde. Count: 1
* (PatchRequest som PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Referens: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest som PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Värde: 2819c223-7f76-453A-919d-413861904646

***Exempel 6. Avetablera en användare** _

För att avetablera en användare från ett identitets lager med en SCIM-tjänst skickar Azure AD en begäran som:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

I exempel koden översätts begäran till ett anrop till DeleteAsync-metoden för tjänstens Provider. Här är signaturen för den metoden: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Det tillhandahållna objektet som värde för argumentet resourceIdentifier har följande egenskaps värden i exemplet på en begäran om att avetablera en användare: 

_ ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Steg 4: integrera din SCIM-slutpunkt med Azure AD SCIM-klienten

Azure AD kan konfigureras att automatiskt etablera tilldelade användare och grupper till program som implementerar en särskild profil för [SCIM 2,0-protokollet](https://tools.ietf.org/html/rfc7644). Information om profilen beskrivs i [steg 2: förstå Azure AD scim-implementeringen](#step-2-understand-the-azure-ad-scim-implementation).

Kontakta din programprovider eller dokumentationen för program leverantören om du vill ha information om kompatibilitet med dessa krav.

> [!IMPORTANT]
> Azure AD SCIM-implementeringen bygger på Azure AD-tjänsten för användar etablering, som är utformad för att ständigt hålla användarna synkroniserade mellan Azure AD och mål programmet och implementerar en mycket specifik uppsättning standard åtgärder. Det är viktigt att förstå dessa beteenden för att förstå beteendet för Azure AD SCIM-klienten. Mer information finns i avsnittet [etablerings cykler: initial och stegvisa](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [anvisningar för hur etablering fungerar](how-provisioning-works.md).

### <a name="getting-started"></a>Komma igång

Program som stöder SCIM-profilen som beskrivs i den här artikeln kan anslutas till Azure Active Directory med hjälp av funktionen "icke-galleriprogram" i program galleriet för Azure AD. När du är ansluten kör Azure AD en synkroniseringsprocess var 40 minut där den frågar programmets SCIM-slutpunkt för tilldelade användare och grupper och skapar eller ändrar dem enligt tilldelnings informationen.

**Så här ansluter du ett program som stöder SCIM:**

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com). Observera att du kan få åtkomst till en kostnads fri utvärderings version av Azure Active Directory med P2-licenser genom att registrera dig för [programmet för utvecklare](https://developer.microsoft.com/office/dev-program)
2. Välj **företags program** i det vänstra fönstret. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
3. Välj **+ nytt program**  >  **+ skapa ditt eget program**.
4. Ange ett namn för ditt program, Välj alternativet "*integrera andra program som du inte hittar i galleriet*" och välj **Lägg till** för att skapa ett app-objekt. Den nya appen läggs till i listan över företags program och öppnas på sidan för hantering av appar.

   ![Skärm bild som visar Azure AD Application Gallery ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *Azure AD-programgalleriet*

   > [!NOTE]
   > Om du använder den gamla App Gallery-upplevelsen följer du skärm guiden nedan.
   
   ![Skärm bild som visar Azure ADs gamla App Gallery Experience ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *Azure AD-upplevelse*

5. På skärmen hantering av appar väljer du **etablering** i den vänstra panelen.
6. I menyn **etablerings läge** väljer du **Automatisk**.

   ![Exempel: etablerings sidan för en app i Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurera etablering i Azure Portal*

7. I fältet **klient-URL** anger du URL: en för programmets scim-slutpunkt. Exempel: `https://api.contoso.com/scim/`
8. Om SCIM-slutpunkten kräver en OAuth Bearer-token från en annan utfärdare än Azure AD kopierar du den obligatoriska OAuth Bearer-token till fältet valfritt **hemligt token** . Om det här fältet lämnas tomt innehåller Azure AD en OAuth Bearer-token som utfärdats från Azure AD med varje begäran. Appar som använder Azure AD som en identitetsprovider kan verifiera den här Azure AD-utfärdade token. 
   > [!NOTE]
   > Det är **_inte_* _ rekommenderat att lämna det här fältet tomt och förlita sig på en token som genereras av Azure AD. Det här alternativet är i första hand tillgängligt i test syfte.
9. Välj _ *Testa anslutning** för att Azure Active Directory försöka ansluta till scim-slutpunkten. Om försöket Miss lyckas visas fel information.  

    > [!NOTE]
    > **Test anslutning** frågar scim-slutpunkten för en användare som inte finns med ett slumpmässigt GUID som den matchande egenskap som valts i Azure AD-konfigurationen. Det förväntade korrekta svaret är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande.

10. Om försöket att ansluta till programmet lyckas väljer du **Spara** för att spara administratörens autentiseringsuppgifter.
11. I avsnittet **mappningar** finns det två valbara uppsättningar av [mappningar för attribut](customize-application-attributes.md): en för användar objekt och en för grupp objekt. Välj var och en för att granska de attribut som synkroniseras från Azure Active Directory till din app. Attributen som väljs som **matchande** egenskaper används för att matcha användare och grupper i appen för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    > [!NOTE]
    > Du kan välja att inaktivera synkronisering av grupp objekt genom att inaktivera mappningen "grupper".

12. Under **Inställningar** definierar fältet **omfattning** vilka användare och grupper som ska synkroniseras. Välj **Synkronisera endast tilldelade användare och grupper** (rekommenderas) om du bara vill synkronisera användare och grupper som tilldelats på fliken **användare och grupper** .
13. När konfigurationen är klar ställer du in **etablerings statusen** **på på**.
14. Välj **Spara** för att starta Azure AD Provisioning-tjänsten.
15. Om du bara synkroniserar tilldelade användare och grupper (rekommenderas), måste du välja fliken **användare och grupper** och tilldela de användare eller grupper som du vill synkronisera.

När den första cykeln har startats kan du välja **etablerings loggar** i den vänstra panelen för att övervaka förloppet, vilket visar alla åtgärder som utförs av etablerings tjänsten i din app. Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](check-status-user-account-provisioning.md).

> [!NOTE]
> Den första cykeln tar längre tid att utföra än senare synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Steg 5: publicera ditt program i Azure AD-programgalleriet

Om du skapar ett program som ska användas av fler än en klient kan du göra det tillgängligt i Azure AD-programgalleriet. Detta gör det enkelt för organisationer att identifiera programmet och konfigurera etablering. Det är enkelt att publicera din app i Azure AD-galleriet och göra etableringen tillgänglig för andra. Kolla in stegen [här](../develop/v2-howto-app-gallery-listing.md). Microsoft kommer att samar beta med dig för att integrera ditt program i vårt galleri, testa din slut punkt och publicera onboarding- [dokumentation](../saas-apps/tutorial-list.md) för kunder att använda.

### <a name="gallery-onboarding-checklist"></a>Check lista för Galleri registrering
Följ check listan nedan för att säkerställa att ditt program registreras snabbt och att kunderna har en smidig distributions upplevelse. Informationen kommer att samlas in från dig när du registrerar dig i galleriet. 
> [!div class="checklist"]
> * Stöd för en [SCIM 2,0](#step-2-understand-the-azure-ad-scim-implementation) -användare och grupp slut punkt (endast en krävs, men båda rekommenderas)
> * Stöd minst 25 begär Anden per sekund per klient för att säkerställa att användare och grupper etableras och avetableras utan fördröjning (krävs)
> * Upprätta teknisk support och support kontakter som hjälper kunder att publicera gallerier (krävs)
> * 3 ej utgångna testautentiseringsuppgifter för ditt program (krävs)
> * Stöd för OAuth-auktoriseringskod eller en lång livs längd enligt beskrivningen nedan (krävs)
> * Upprätta en teknisk kontakt punkt för att få support för kunder efter Galleri registrering (krävs)
> * Stöd för uppdatering av flera grupp medlemskap med en enda korrigering (rekommenderas) 
> * Dokumentera din SCIM-slutpunkt offentligt (rekommenderas) 
> * [Stöd för schema identifiering](https://tools.ietf.org/html/rfc7643#section-6) (rekommenderas)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Auktorisering för etablering av anslutningar i program galleriet
SCIM-specifikationen definierar inte ett SCIM schema för autentisering och auktorisering. Den förlitar sig på användningen av befintliga bransch standarder. Azure AD Provisioning-klienten har stöd för två autentiseringsmetoder för program i galleriet. 

|Auktoriseringsmetod|Fördelar|Nackdelar|Support|
|--|--|--|--|
|Användar namn och lösen ord (rekommenderas inte eller stöds inte av Azure AD)|Lätt att implementera|Osäker – [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Stöds från fall till fall för Galleri-appar. Stöds inte för appar som inte är gallerier.|
|Token med lång livs längd|Token för lång livs längd kräver inte att en användare finns. De är enkla för administratörer att använda vid konfigurations etablering.|Token för lång livs längd kan vara svårt att dela med en administratör utan att använda oskyddade metoder, till exempel e-post. |Stöds för Galleri-och appar som inte är gallerier. |
|Bevilja OAuth-auktoriseringskod|Åtkomst-token är mycket kortare än lösen ord och har en automatiserad uppdaterings funktion som används av token med lång livs längd.  En riktig användare måste närvara vid den första auktoriseringen och lägga till en ansvars nivå. |Kräver att en användare är närvarande. Om användaren lämnar organisationen är token ogiltig och auktoriseringen måste slutföras igen.|Stöds för Galleri-appar, men inte för appar som inte är gallerier. Du kan dock ange en åtkomsttoken i användar gränssnittet som den hemliga token för kortsiktiga testnings ändamål. Stöd för OAuth-kod beviljande av icke-galleri är i vår efter släpning, förutom stöd för konfigurerbara URL: er för autentisering/token i Galleri appen.|
|Beviljande av OAuth-klientautentiseringsuppgifter|Åtkomst-token är mycket kortare än lösen ord och har en automatiserad uppdaterings funktion som används av token med lång livs längd. Både behörigheten Tillåt och klientens autentiseringsuppgifter skapar samma typ av åtkomsttoken, så att flytta mellan dessa metoder är transparent för API: et.  Etableringen kan vara helt automatiserad och nya token kan utföras tyst utan användar interaktion. ||Stöds inte för Galleri-och appar som inte är gallerier. Support finns i vår efter släpning.|

> [!NOTE]
> Vi rekommenderar inte att du lämnar fältet token tomt i konfigurations gränssnittet för Azure AD-etablering av anpassade appar. Den token som genereras är primärt tillgänglig i test syfte.

**Utfärdande flöde för OAuth-auktoriseringskod:** Etablerings tjänsten har stöd för [beviljande av auktoriseringskod](https://tools.ietf.org/html/rfc6749#page-24). När du har skickat din begäran om att publicera din app i galleriet, kommer vårt team att samar beta med dig för att samla in följande information:
*  URL för auktorisering: en URL som klienten kan använda för att få behörighet från resurs ägaren via omdirigering av användar agent. Användaren omdirigeras till denna URL för att ge åtkomst. Observera att denna URL för närvarande inte kan konfigureras per klient.
*  URL för token Exchange: en URL till klienten som utbyter en auktorisering för en åtkomsttoken, vanligt vis med klientautentisering. Observera att denna URL för närvarande inte kan konfigureras per klient.
*  Klient-ID: auktoriseringsservern utfärdar den registrerade klienten ett klient-ID, vilket är en unik sträng som representerar den registrerings information som tillhandahålls av klienten.  Klient-ID: n är inte en hemlighet. den exponeras för resurs ägaren och **får inte** användas separat för klientautentisering.  
*  Klient hemlighet: klient hemligheten är en hemlighet som genereras av auktoriseringsservern. Det bör vara ett unikt värde som endast är känt för auktoriseringsservern. 

Observera att OAuth v1 inte stöds på grund av exponering av klient hemligheten. OAuth v2 stöds.  

Metod tips (rekommenderas men krävs inte):
* Stöder flera omdirigerings-URL: er. Administratörer kan konfigurera etablering från både "portal.azure.com" och "aad.portal.azure.com". Genom att stödja flera omdirigerings-URL: er ser du till att användarna kan auktorisera åtkomst från portalen.
* Stöd för flera hemligheter för att säkerställa en smidig hemlig förnyelse utan drift avbrott. 

Steg i OAuth Code Granting-flödet:
1. Logga in på Azure Portal > företags program > Välj program > etablering > Klicka på Auktorisera.
2. Azure Portal omdirigerar användaren till URL: en för auktorisering (inloggnings sidan för appen från tredje part).
3. Admin tillhandahåller autentiseringsuppgifter för programmet från tredje part. 
4. Appen från tredje part omdirigerar användaren tillbaka till Azure Portal och ger beviljande koden 
5. Azure AD Provisioning Services anropar token-URL: en och ger Grant-koden. Programmet från tredje part svarar med åtkomsttoken, uppdateringstoken och förfallo datum
6. När etablerings cykeln börjar kontrollerar tjänsten om den aktuella åtkomsttoken är giltig och utbyter den för en ny token vid behov. Åtkomsttoken anges i varje begäran som görs till appen och giltigheten för begäran kontrol leras före varje begäran.

> [!NOTE]
> Det går inte att konfigurera OAuth i det program som inte är galleriet i dag, men du kan skapa en åtkomsttoken manuellt från din Authorization Server och inmatade värden i fältet hemligt token i program som inte är Galleri. På så sätt kan du kontrol lera kompatibiliteten för SCIM-servern med Azure AD SCIM-klienten innan du registrerar i app-galleriet, som har stöd för OAuth-kod beviljande.  

**OAuth-token för lång livs längd:** Om ditt program inte stöder OAuth-auktoriseringsvärdet för OAuth-auktoriseringskod kan du också generera en lång livs längd för OAuth Bearer-token som en administratör kan använda för att konfigurera etablerings integrationen. Token ska vara beständig, annars placeras etablerings jobbet i [karantän](application-provisioning-quarantine-status.md) när token upphör att gälla.

Om du vill ha ytterligare metoder för autentisering och auktorisering kan du berätta för oss på [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Check lista för att gå till marknads start
För att hjälpa till att öka medvetenheten och behovet av vår gemensamma integrering rekommenderar vi att du uppdaterar din befintliga dokumentation och utvidgar integrationen i dina marknadsförings kanaler.  Nedan visas en uppsättning med check lista aktiviteter som vi rekommenderar att du slutför för att stödja lanseringen

* **Beredskap för försäljning och kund support.** Se till att dina Sälj-och support team är medvetna och kan prata med integrerings funktionerna. Korta ditt sälj-och support team och ge dem med vanliga frågor och svar och inkludera integreringen i ditt försäljnings material. 
* **Blogg inlägg och/eller pressmeddelanden.** Skapa en blogg post eller pressmeddelande som beskriver den gemensamma integrationen, fördelarna och hur du kommer igång. [Exempel: InPrivate och Azure Active Directory pressmeddelande](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociala medier.** Utnyttja dina sociala medier som Twitter, Facebook eller LinkedIn för att marknadsföra integreringen med kunderna. Var noga med att ta med @AzureAD så att vi kan göra en Tweet för ditt inlägg. [Exempel: InPrivate Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marknadsförings webbplats.** Skapa eller uppdatera dina marknadsförings sidor (t. ex. integrations sidan, partner sidan, sidan med priser osv.) för att inkludera den gemensamma integreringens tillgänglighet. [Exempel: Pingboard integration Page](https://pingboard.com/org-chart-for), sidan för [Smartsheet-integrering](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), sidan [Monday.com prissättning](https://monday.com/pricing/) 
* **Teknisk dokumentation.** Skapa en hjälp Center-artikel eller teknisk dokumentation om hur kunder kan komma igång. [Exempel: mottagare + Microsoft Azure Active Directory-integration.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Kund kommunikation.** Meddela kunder om den nya integrationen genom din kund kommunikation (månads Visa nyhets brev, e-postkampanjer, produkt viktig information). 

## <a name="related-articles"></a>Relaterade artiklar

* [Utveckla en exempel SCIM-slutpunkt](use-scim-to-build-users-and-groups-endpoints.md)
* [Automatisera användar etablering och avetablering för SaaS-appar](user-provisioning.md)
* [Anpassa mappningar av attribut för användar etablering](customize-application-attributes.md)
* [Skriver uttryck för mappningar av attribut](functions-for-customizing-application-data.md)
* [Omfångs filter för användar etablering](define-conditional-rules-for-provisioning-user-accounts.md)
* [Meddelanden om konto etablering](user-provisioning.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)

