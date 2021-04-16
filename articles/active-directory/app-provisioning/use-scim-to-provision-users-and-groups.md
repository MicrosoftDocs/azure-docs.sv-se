---
title: Självstudie – Utveckla en SCIM-slutpunkt för användareablering till appar från Azure AD
description: System for Cross-domain Identity Management (SCIM) standardiserar automatisk användareablering. I den här självstudien lär du dig att utveckla en SCIM-slutpunkt, integrera ditt SCIM-API med Azure Active Directory och börja automatisera etableringen av användare och grupper i dina molnprogram.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3d53c96c4b0306911b0c8a0b8576f35a73419db0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498160"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Självstudie: Utveckla och planera etablering för en SCIM-slutpunkt

Som programutvecklare kan du använda API:et System for Cross-Domain Identity Management (SCIM) för användarhantering för att aktivera automatisk etablering av användare och grupper mellan ditt program och Azure AD (AAD). Den här artikeln beskriver hur du skapar en SCIM-slutpunkt och integrerar med AAD-etableringstjänsten. SCIM-specifikationen innehåller ett gemensamt användarschema för etablering. När SCIM används tillsammans med federationsstandarder som SAML eller OpenID Connect, ger SCIM administratörer en standardbaserad lösning från hela slutet för åtkomsthantering.

![Etablering från Azure AD till en app med SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM är en standardiserad definition av två slutpunkter: en `/Users` slutpunkt och en `/Groups` slutpunkt. Det använder vanliga REST-verb för att skapa, uppdatera och ta bort objekt och ett fördefinierat schema för vanliga attribut som gruppnamn, användarnamn, förnamn, efternamn och e-post. Appar som erbjuder en SCIM 2.0-REST API kan minska eller eliminera problem med att arbeta med ett egenutvecklat API för användarhantering. En kompatibel SCIM-klient kan till exempel göra en HTTP POST för ett JSON-objekt till slutpunkten `/Users` för att skapa en ny användarpost. I stället för att behöva ett något annorlunda API för samma grundläggande åtgärder kan appar som följer SCIM-standarden omedelbart dra nytta av befintliga klienter, verktyg och kod. 

Standardschemat för användarobjekt och rest-API:er för hantering som definieras i SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) gör det möjligt för identitetsproviders och appar att enkelt integreras med varandra. Programutvecklare som skapar en SCIM-slutpunkt kan integrera med alla SCIM-kompatibla klienter utan att behöva göra anpassat arbete.

För att automatisera etableringen till ett program krävs att du skapar och integrerar en SCIM-slutpunkt med Azure AD SCIM-klienten. Använd följande steg för att börja etablera användare och grupper i ditt program. 
    
1. Utforma ditt användar- och gruppschema

   Identifiera programmets objekt och attribut för att avgöra hur de mappar till det användar- och gruppschema som stöds av AAD SCIM-implementeringen.

1. Förstå AAD SCIM-implementeringen

   Förstå hur AAD SCIM-klienten implementeras för att modellera hanteringen av scim-protokollbegäran och svar.

1. Skapa en SCIM-slutpunkt

   En slutpunkt måste vara SCIM 2.0-kompatibel för att integrera med AAD-etableringstjänsten. Som ett alternativ kan du använda Microsoft CLI-bibliotek (Common Language Infrastructure) och kodexempel för att skapa slutpunkten. Dessa exempel är endast för referens och testning. vi rekommenderar att du inte använder dem som beroenden i produktionsappen.

1. Integrera din SCIM-slutpunkt med AAD SCIM-klienten 

   Om din organisation använder ett program från tredje part för att implementera en profil av SCIM 2.0 som AAD stöder kan du snabbt automatisera både etablering och avetablering av användare och grupper.

1. Publicera programmet i AAD-programgalleriet 

   Gör det enkelt för kunderna att identifiera ditt program och enkelt konfigurera etablering. 

![Steg för att integrera en SCIM-slutpunkt med Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>Utforma ditt användar- och gruppschema

Varje program kräver olika attribut för att skapa en användare eller grupp. Starta integreringen genom att identifiera de objekt (användare, grupper) och attribut (namn, chef, befattning osv.) som programmet behöver. 

SCIM-standarden definierar ett schema för att hantera användare och grupper. 

Huvudanvändarschemat kräver bara tre attribut (alla andra attribut är valfria): 

- `id`, definierad identifierare för tjänstleverantör
- `userName`, en unik identifierare för användaren (mappar vanligtvis till Azure AD-användarens huvudnamn)
- `meta`, *skrivskyddade* metadata som underhålls av tjänstleverantören

Utöver huvudanvändarschemat definierar SCIM-standarden ett tillägg för företagsanvändare med en modell för att utöka användarschemat så att det uppfyller programmets behov.   

Om ditt program till exempel kräver både en användares e-post och användarens chef använder  du huvudschemat för att samla in användarens e-post och företagets användarschema för att samla in användarens chef. 

Följ dessa steg om du vill utforma schemat:

1. Lista de attribut som programmet kräver och kategorisera sedan som attribut som behövs för autentisering (t.ex. loginName och e-post), attribut som behövs för att hantera användarlivscykeln (t.ex. status/aktiv) och alla andra attribut som behövs för att programmet ska fungera (t.ex. chef, tagg).

1. Kontrollera om attributen redan har definierats i **huvudanvändarschemat** eller **i schemat för** företagsanvändare. Om inte måste du definiera ett tillägg till användarschemat som omfattar de attribut som saknas. Se exemplet nedan för ett tillägg till användaren som tillåter etablering av en `tag` användare.

1. Mappa SCIM-attribut till användarattributen i Azure AD. Om något av de attribut som du har definierat i SCIM-slutpunkten inte har en tydlig motsvarighet i Azure AD-användarschemat kan du vägleda klientadministratören för att utöka schemat eller använda ett tilläggsattribut enligt nedan `tags` för egenskapen.

|Obligatoriskt appattribut|Mappat SCIM-attribut|Mappat Azure AD-attribut|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|förnamn|
|lastName|name.familyName|Efternamn|
|workMail|emails[type eq "work"].value|E-post|
|manager|manager|manager|
|tagg|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|aktiv|isSoftDeleted (beräknat värde lagras inte på användaren)|

**Exempellista över obligatoriska attribut**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
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
**Exempelschema som definieras av en JSON-nyttolast**

> [!NOTE]
> Förutom de attribut som krävs för programmet innehåller JSON-representationen även de obligatoriska `id` `externalId` attributen , och `meta` .

Det hjälper till att kategorisera mellan och för att mappa standardanvändarattribut i Azure AD till SCIM RFC. Se hur anpassa attribut mappas mellan Azure AD och `/User` `/Group` [SCIM-slutpunkten.](customize-application-attributes.md)


| Azure Active Directory användare | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktiv |
|avdelning|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|Employeeid|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| förnamn |name.givenName |
| jobTitle |title |
| e-post |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobil |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxyadresser |emails[type eq "other"]. Värde |
| physical-Delivery-OfficeName |addresses[type eq "other"]. Formaterad |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telefonnumret |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

**Exempellista över användar- och gruppattribut**

| Azure Active Directory grupp | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| e-post |emails[type eq "work"].value |
| mailNickname |displayName |
| medlemmar |medlemmar |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"]. Värde |

**Exempellista över gruppattribut**

> [!NOTE]
> Du behöver inte stödja både användare och grupper, eller alla attribut som visas här. Det är bara en referens för hur attribut i Azure AD ofta mappas till egenskaper i SCIM-protokollet. 

Det finns flera definierade slutpunkter i SCIM RFC. Du kan börja med `/User` slutpunkten och sedan expandera därifrån. 

|Slutpunkt|Beskrivning|
|--|--|
|/Användare|Utföra CRUD-åtgärder på ett användarobjekt.|
|/Group|Utföra CRUD-åtgärder på ett gruppobjekt.|
|/Scheman|Den uppsättning attribut som stöds av varje klient och tjänstleverantör kan variera. En tjänstleverantör kan innehålla `name` , och , medan en annan `title` `emails` tjänstleverantör använder , och `name` `title` `phoneNumbers` . Schemaslutpunkten gör det möjligt att identifiera de attribut som stöds.|
|/Bulk|Med massåtgärder kan du utföra åtgärder på en stor samling resursobjekt i en enda åtgärd (t.ex. uppdatera medlemskap för en stor grupp).|
|/ServiceProviderConfig|Innehåller information om funktionerna i SCIM-standarden som stöds, till exempel vilka resurser som stöds och autentiseringsmetoden.|
|/ResourceTypes|Anger metadata om varje resurs.|

**Exempellista över slutpunkter**

> [!NOTE]
> Använd slutpunkten för att stödja anpassade attribut eller om schemat ändras ofta eftersom det gör att en klient kan hämta det `/Schemas` senaste schemat automatiskt. Använd `/Bulk` slutpunkten för att stödja grupper.

## <a name="understand-the-aad-scim-implementation"></a>Förstå AAD SCIM-implementeringen

Det här avsnittet beskriver hur AAD SCIM-klienten implementeras för att stödja ett SCIM 2.0-användarhanterings-API och visar hur du modellerar din HANTERING av SCIM-protokollbegäran och svar.

> [!IMPORTANT]
> Beteendet för Azure AD SCIM-implementeringen uppdaterades senast den 18 december 2018. Information om vad som har ändrats finns [i SCIM 2.0-protokollefterlevnad för Azure AD User Provisioning-tjänsten.](application-provisioning-config-problem-scim-compatibility.md)

I [scim 2.0-protokollspecifikationen](http://www.simplecloud.info/#Specification)måste ditt program ha stöd för följande krav:

|Krav|Referensanteckningar (SCIM-protokoll)|
|-|-|
|Skapa användare och eventuellt även grupper|[avsnitt 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Ändra användare eller grupper med PATCH-begäranden|[avsnitt 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). Stöd säkerställer att grupper och användare etableras på ett effektivt sätt.|
|Hämta en känd resurs för en användare eller grupp som skapats tidigare|[avsnitt 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Fråga användare eller grupper|[avsnitt 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Som standard hämtas användare av sina och `id` efterfrågas av deras `username` och , och grupper `externalId` efterfrågas av `displayName` .|
|Fråga användare efter ID och efter chef|avsnitt 3.4.2|
|Frågegrupper efter ID och efter medlem|avsnitt 3.4.2|
|Filtret [excludedAttributes= medlemmar när](#get-group) du frågar gruppresursen|avsnitt 3.4.2.5|
|Acceptera en enskild bearer-token för autentisering och auktorisering av AAD till ditt program.||
|Mjuk borttagning av en `active=false` användare och återställning av användaren `active=true`|Användarobjektet ska returneras i en begäran oavsett om användaren är aktiv eller inte. Den enda gången användaren inte ska returneras är när den är hård borttagning från programmet.|
|Stöd för /Schemas-slutpunkten|[avsnitt 7](https://tools.ietf.org/html/rfc7643#page-30) Slutpunkten för schemaidentifiering används för att identifiera ytterligare attribut.|

Använd de allmänna riktlinjerna när du implementerar en SCIM-slutpunkt för att säkerställa kompatibilitet med AAD:

* `id` är en obligatorisk egenskap för alla resurser. Varje svar som returnerar en resurs bör se till att varje resurs har den här egenskapen, `ListResponse` förutom med noll medlemmar.
* Svar på en fråga/filterbegäran ska alltid vara `ListResponse` en .
* Grupper är valfria, men stöds endast om SCIM-implementeringen stöder **PATCH-begäranden.**
* Du behöver inte inkludera hela resursen i **PATCH-svaret.**
* Microsoft AAD använder endast följande operatorer: `eq` , `and`
* Kräv inte en fallkänslig matchning på strukturella element  i SCIM, särskilt PATCH-åtgärdsvärden, enligt definitionen i `op` avsnitt [3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). AAD skickar värdena för som `op` Lägg **till,** **Ersätt och** **Ta bort.**
* Microsoft AAD gör begäranden om att hämta en slumpmässig användare och grupp för att säkerställa att slutpunkten och autentiseringsuppgifterna är giltiga. Det görs också som en del av flödet **Testa anslutning** i [Azure Portal](https://portal.azure.com). 
* Attributet som resurserna kan efterfrågas i ska anges som ett matchande attribut i programmet i [Azure Portal](https://portal.azure.com), se Anpassa attributmappningar för [användareetablering.](customize-application-attributes.md)
* Attributet entitlements stöds inte.
* Stöd för HTTPS på scim-slutpunkten.
* [Schemaidentifiering](#schema-discovery)
  * Schemaidentifiering stöds för närvarande inte i det anpassade programmet, men det används i vissa galleriprogram. I framtiden används schemaidentifiering som primär metod för att lägga till ytterligare attribut i en anslutningsapp. 
  * Skicka inte null-värden om det inte finns något värde.
  * Egenskapsvärdena ska vara kamelskalade (t.ex. readWrite).
  * Måste returnera ett listsvar.
  
### <a name="user-provisioning-and-deprovisioning"></a>Användareablering och avetablering

Följande bild visar meddelanden som AAD skickar till en SCIM-tjänst för att hantera livscykeln för en användare i programmets identitetsarkiv.  

![Visar användaretablerings- och avetableringssekvensen](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sekvens för användareablering och avetablering*

### <a name="group-provisioning-and-deprovisioning"></a>Gruppetablering och avetablering

Gruppetablering och avetablering är valfria. När den implementeras och aktiveras visar följande bild meddelanden som AAD skickar till en SCIM-tjänst för att hantera livscykeln för en grupp i programmets identitetsarkiv. Dessa meddelanden skiljer sig från meddelanden om användare på två sätt:

* Begäranden om att hämta grupper anger att medlemsattributet ska undantas från alla resurser som tillhandahålls som svar på begäran.  
* Begäranden för att avgöra om ett referensattribut har ett visst värde är begäranden om medlemsattributet.  

![Visar sekvensen för gruppetablering och avetablering](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sekvens för gruppetablering och avetablering*

### <a name="scim-protocol-requests-and-responses"></a>SCIM-protokollbegäranden och svar
Det här avsnittet innehåller exempel på SCIM-begäranden som skickas av AAD SCIM-klienten och exempel på förväntade svar. För bästa resultat bör du koda din app för att hantera dessa begäranden i det här formatet och generera förväntade svar.

> [!IMPORTANT]
> Information om hur och när AAD-användareableringstjänsten genererar de åtgärder som beskrivs nedan finns i avsnittet [Etableringscykler: Initial](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) och inkrementell i [Så här fungerar etablering.](how-provisioning-works.md)

[Användaråtgärder](#user-operations)
  - [Skapa användare](#create-user) [(begärandesvar)](#request)  /  [](#response)
  - [Hämta användare](#get-user) [(begärandesvar)](#request-1)  /  [](#response-1)
  - [Hämta användare efter fråga](#get-user-by-query) [(svar på](#request-2)  /  [begäran)](#response-2)
  - [Hämta användare efter fråga – noll resultat](#get-user-by-query---zero-results) [(svar på](#request-3)  /  [begäran)](#response-3)
  - [Uppdatera användaren [Egenskaper med flera värden]](#update-user-multi-valued-properties) ([Begärandesvar](#request-4)  /  [](#response-4))
  - [Uppdatera användaren [egenskaper med ett värde]](#update-user-single-valued-properties) ([Svar på](#request-5)  /  [begäran)](#response-5) 
  - [Inaktivera användare](#disable-user) [(begärandesvar)](#request-14)  /  [](#response-14)
  - [Ta bort användare](#delete-user) [(begärandesvar)](#request-6)  /  [](#response-6)

[Gruppåtgärder](#group-operations)
  - [Skapa grupp](#create-group) ([begärandesvar](#request-7)  /  [](#response-7))
  - [Hämta grupp](#get-group) ([begärandesvar](#request-8)  /  [](#response-8))
  - [Hämta grupp efter displayName](#get-group-by-displayname) [](#request-9)  /  [(begärandesvar)](#response-9)
  - [Uppdateringsgrupp [attribut som inte är medlemmar]](#update-group-non-member-attributes) ([Svar på](#request-10)  /  [begäran](#response-10))
  - [Uppdateringsgrupp [Lägg till medlemmar]](#update-group-add-members) [(svar på](#request-11)  /  [begäran)](#response-11)
  - [Uppdateringsgrupp [Ta bort medlemmar]](#update-group-remove-members) ([](#request-12)  /  [Begärandesvar](#response-12))
  - [Ta bort grupp](#delete-group) ([](#request-13)  /  [begärandesvar](#response-13))

[Schemaidentifiering](#schema-discovery)
  - [Identifiera schema](#discover-schema) [(begärandesvar)](#request-15)  /  [](#response-15)

### <a name="user-operations"></a>Användaråtgärder

* Användare kan frågas av `userName` `email[type eq "work"]` attributen eller .  

#### <a name="create-user"></a>Skapa användare

##### <a name="request"></a>Förfrågan

*POST /Users*
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

*HTTP/1.1 201 Skapad*
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
*GET /Users/5d48a0a8e9f04aa38008* 

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
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Svar (användaren hittades inte. Observera att information inte krävs, endast status.)

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

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

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

#### <a name="get-user-by-query---zero-results"></a>Hämta användare efter fråga – noll resultat

##### <a name="request"></a><a name="request-3"></a>Förfrågan

*GET /Users?filter=userName eq "non-existent user"*

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

#### <a name="update-user-multi-valued-properties"></a>Uppdatera användare [egenskaper med flera värden]

##### <a name="request"></a><a name="request-4"></a>Förfrågan

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
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

#### <a name="update-user-single-valued-properties"></a>Uppdatera användare [egenskaper med ett värde]

##### <a name="request"></a><a name="request-5"></a>Förfrågan

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Svarsåtgärder

*HTTP/1.1 204 Inget innehåll*

### <a name="group-operations"></a>Gruppåtgärder

* Grupper ska alltid skapas med en tom medlemslista.
* Grupper kan efterfrågas av `displayName` attributet .
* Uppdatering av gruppen PATCH-begäran bör ge *ett HTTP 204-innehåll* i svaret. Det är inte lämpligt att returnera en brödtext med en lista över alla medlemmar.
* Det är inte nödvändigt att stödja att returnera alla medlemmar i gruppen.

#### <a name="create-group"></a>Skapa grupp

##### <a name="request"></a><a name="request-7"></a>Förfrågan

*POST /Groups HTTP/1.1*
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

*HTTP/1.1 201 Skapad*
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

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

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
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

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

#### <a name="update-group-non-member-attributes"></a>Uppdatera grupp [attribut som inte är medlemmar]

##### <a name="request"></a><a name="request-10"></a>Förfrågan

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
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

*HTTP/1.1 204 Inget innehåll*

### <a name="update-group-add-members"></a>Uppdateringsgrupp [Lägg till medlemmar]

##### <a name="request"></a><a name="request-11"></a>Förfrågan

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 Inget innehåll*

#### <a name="update-group-remove-members"></a>Uppdateringsgrupp [Ta bort medlemmar]

##### <a name="request"></a><a name="request-12"></a>Förfrågan

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 Inget innehåll*

#### <a name="delete-group"></a>Ta bort grupp

##### <a name="request"></a><a name="request-13"></a>Förfrågan

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Svarsåtgärder

*HTTP/1.1 204 Inget innehåll*

### <a name="schema-discovery"></a>Schemaidentifiering
#### <a name="discover-schema"></a>Identifiera schema

##### <a name="request"></a><a name="request-15"></a>Förfrågan
*GET /Scheman* 
##### <a name="response"></a><a name="response-15"></a>Svarsåtgärder
*HTTP/1.1 200 OK*
```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:ListResponse"
    ],
    "itemsPerPage": 50,
    "startIndex": 1,
    "totalResults": 3,
    "Resources": [
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:User",
    "name" : "User",
    "description" : "User Account",
    "attributes" : [
      {
        "name" : "userName",
        "type" : "string",
        "multiValued" : false,
        "description" : "Unique identifier for the User, typically
used by the user to directly authenticate to the service provider.
Each User MUST include a non-empty userName value.  This identifier
MUST be unique across the service provider's entire set of Users.
REQUIRED.",
        "required" : true,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "server"
      },                
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:User"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:Group",
    "name" : "Group",
    "description" : "Group",
    "attributes" : [
      {
        "name" : "displayName",
        "type" : "string",
        "multiValued" : false,
        "description" : "A human-readable name for the Group.
REQUIRED.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:Group"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
    "name" : "EnterpriseUser",
    "description" : "Enterprise User",
    "attributes" : [
      {
        "name" : "employeeNumber",
        "type" : "string",
        "multiValued" : false,
        "description" : "Numeric or alphanumeric identifier assigned
to a person, typically based on order of hire or association with an
organization.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
"/v2/Schemas/urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
    }
  }
]
}
```

### <a name="security-requirements"></a>Säkerhetskrav
**TLS-protokollversioner**

De enda godkända TLS-protokollversionerna är TLS 1.2 och TLS 1.3. Inga andra versioner av TLS tillåts. Ingen version av SSL tillåts. 
- RSA-nycklar måste vara minst 2 048 bitar.
- ECC-nycklar måste vara minst 256 bitar, genererade med en godkänd ellipskurva

**Nyckellängder**

Alla tjänster måste använda X.509-certifikat som genereras med hjälp av kryptografiska nycklar med tillräcklig längd, vilket innebär:

**Chiffersviter**

Alla tjänster måste konfigureras för att använda följande chiffersviter i den exakta ordning som anges nedan. Observera att om du bara har ett RSA-certifikat har de ECDSA-chiffersviterna ingen effekt. </br>

Minsta TLS 1.2-chiffersviter:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP-intervall
Azure AD-etableringstjänsten fungerar för närvarande under IP-intervallen för AzureActiveDirectory enligt listan [här](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all). Du kan lägga till IP-intervallen som visas under taggen AzureActiveDirectory för att tillåta trafik från Azure AD-etableringstjänsten i ditt program. Observera att du måste granska listan över IP-intervall noggrant för beräknade adresser. En adress som "40.126.25.32" kan representeras i IP-intervalllistan som "40.126.0.0/18". Du kan också hämta IP-intervalllistan programmatiskt med hjälp av följande [API.](/rest/api/virtualnetwork/servicetags/list)

## <a name="build-a-scim-endpoint"></a>Skapa en SCIM-slutpunkt

Nu när du har utformat ditt schema och förstått Azure AD SCIM-implementeringen kan du komma igång med att utveckla scim-slutpunkten. I stället för att börja från grunden och skapa implementeringen helt på egen hand kan du förlita dig på ett antal SCIM-bibliotek med öppen källkod som publicerats av SCIM-communityn.

Vägledning om hur du skapar en SCIM-slutpunkt, inklusive exempel, finns i [Utveckla ett scim-exempelslutpunkt.](use-scim-to-build-users-and-groups-endpoints.md)

Exemplet med .NET Core-referenskod med [öppen](https://aka.ms/SCIMReferenceCode) källkod som publicerats av Azure AD-etableringsteamet är en sådan resurs som kan komma igång med utvecklingen. När du har skapat SCIM-slutpunkten kan du testa den. Du kan använda den samling [postman-tester som](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) tillhandahålls som en del av referenskoden eller köra genom exempelbegäranden/svar som anges [ovan.](#user-operations)  

   > [!Note]
   > Referenskoden är avsedd att hjälpa dig att komma igång med att skapa din SCIM-slutpunkt och tillhandahålls som "I AS IS". Bidrag från communityn är välkommen att hjälpa till att skapa och underhålla koden.

Lösningen består av två projekt, _Microsoft.SCIM_ och _Microsoft.SCIM.WebHostSample._

_Microsoft.SCIM-projektet_ är det bibliotek som definierar de komponenter i webbtjänsten som överensstämmer med SCIM-specifikationen. Den deklarerar gränssnittet _Microsoft.SCIM.IProvider_, begäranden översätts till anrop till leverantörens metoder, som skulle programmeras att fungera i ett identitetslager.

![Analys: En begäran översätts till anrop till leverantörens metoder](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Projektet _Microsoft.SCIM.WebHostSample är_ ett Visual Studio ASP.NET Core-webbprogram, baserat på _mallen_ Tom. På så sätt kan exempelkoden distribueras som fristående, värdindelade i containrar eller inom Internet Information Services. Den implementerar också gränssnittet _Microsoft.SCIM.IProvider_ och håller klasser i minnet som ett exempel på identitetsarkiv.

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

SCIM-tjänsten måste ha en HTTP-adress och ett certifikat för serverautentisering där rotcertifikatutfärdaren är ett av följande namn:

* Cnnic
* Comodo
* CyberTrust
* DigiCert
* Geotrust
* GlobalSign
* Go IoT
* Verisign
* WoSign
* DST-rotcertifikatutfärdare X3

Den .NET Core SDK innehåller ett HTTPS-utvecklingscertifikat som kan användas under utveckling, installeras certifikatet som en del av den första körningen. Beroende på hur du kör ASP.NET Core-webbappen kommer den att lyssna på en annan port:

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Mer information om HTTPS i ASP.NET Core finns på följande länk: [Framtvinga HTTPS i ASP.NET Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Hantera slutpunktsautentisering

Begäranden från Azure Active Directory inkluderar en OAuth 2.0-bearer-token. Alla tjänster som tar emot begäran ska autentisera utfärdaren som Azure Active Directory för den förväntade Azure Active Directory klientorganisationen.

I token identifieras utfärdaren av ett iss-anspråk, till exempel `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . I det här exemplet identifierar basadressen för anspråksvärdet, , Azure Active Directory som utfärdare, medan det relativa adresssegmentet, `https://sts.windows.net` _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, är en unik identifierare för den Azure Active Directory-klient som token utfärdades för.

Målgruppen för token är programmalls-ID:t för programmet i galleriet. Vart och ett av programmen som är registrerade i en enda klientorganisation kan få samma anspråk med `iss` SCIM-begäranden. Programmallens ID för alla anpassade appar _är 8adf8e6e-67b2-4cf2-a259-e3dc5476c621._ Den token som genereras av Azure AD-etableringstjänsten bör endast användas för testning. Den bör inte användas i produktionsmiljöer.

I exempelkoden autentiseras begäranden med hjälp av paketet Microsoft.AspNetCore.Authentication.JwtBearer. Följande kod framtvingar att begäranden till någon av tjänstens slutpunkter autentiseras med hjälp av den innehavartoken som utfärdats av Azure Active Directory för en angiven klientorganisation:

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

En bearer-token krävs också för att använda de angivna [postman-testerna](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) och utföra lokal felsökning med hjälp av localhost. Exempelkoden använder ASP.NET Core-miljöer för att ändra autentiseringsalternativen under utvecklingsfasen och aktivera användningen av en själv signerad token.

Mer information om flera miljöer i ASP.NET Core finns i [Använda flera miljöer i ASP.NET Core](/aspnet/core/fundamentals/environments).

Följande kod framtvingar att begäranden till någon av tjänstens slutpunkter autentiseras med hjälp av en bearer-token signerad med en anpassad nyckel:

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

Skicka en GET-begäran till tokenkontrollanten för att hämta en giltig bearer-token. Metoden _GenerateJSONWebToken_ ansvarar för att skapa en token som matchar de parametrar som konfigurerats för utveckling:

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

***Exempel 1. Fråga tjänsten efter en matchande användare***

Azure Active Directory (AAD) frågar tjänsten efter en användare med ett attributvärde som matchar `externalId` mailNickname-attributvärdet för en användare i AAD. Frågan uttrycks som en Hypertext Transfer Protocol-begäran (HTTP), till exempel i det här exemplet, där jyoung är ett exempel på ett mailNickname för en användare i Azure Active Directory.

>[!NOTE]
> Det här är bara ett exempel. Det är inte alla användare som har ett mailNickname-attribut och det värde som en användare har kanske inte är unikt i katalogen. Dessutom kan attributet som används för matchning (vilket i det här fallet `externalId` är ) konfigureras i [AAD-attributmappningarna](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

I exempelkoden översätts begäran till ett anrop till QueryAsync-metoden för tjänstens provider. Här är signaturen för den metoden: 

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

För en användare med ett användargränssnitt för attributet i exempelfrågan är värdena för argumenten som skickas till `externalId` QueryAsync-metoden:

* Parametrar. AlternateFilters.Count: 1
* Parametrar. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* Parametrar. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* Parametrar. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"

***Exempel 2. Etablera en användare***

Om svaret på en fråga till webbtjänsten för en användare med ett attributvärde som matchar attributvärdet mailNickname för en användare inte returnerar några användare, begär AAD att tjänsten etablerar en användare som motsvarar den i `externalId` AAD.  Här är ett exempel på en sådan begäran: 

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
   "userName":"jyoung@testuser.com",
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

I exempelkoden översätts begäran till ett anrop till metoden CreateAsync för tjänstens provider. Här är signaturen för den metoden: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

I en begäran om att etablera en användare är värdet för resursargumentet en instans av klassen Microsoft.SCIM.Core2EnterpriseUser, definierad i biblioteket Microsoft.SCIM.Schemas.  Om begäran om att etablera användaren lyckas förväntas implementeringen av metoden returnera en instans av klassen Microsoft.SCIM.Core2EnterpriseUser, med värdet för identifieraregenskapen inställt på den nya etablerade användarens unika identifierare.  

***Exempel 3. Fråga efter det aktuella tillståndet för en användare*** 

Om du vill uppdatera en användare som är känd för att finnas i ett identitetslager som frontad av en SCIM, fortsätter Azure Active Directory genom att begära det aktuella tillståndet för användaren från tjänsten med en begäran, till exempel: 

```
GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

I exempelkoden översätts begäran till ett anrop till metoden RetrieveAsync för tjänstens provider. Här är signaturen för den metoden: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource and 
// Microsoft.SCIM.IResourceRetrievalParameters 
// are defined in Microsoft.SCIM.Schemas 

Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

I exemplet med en begäran om att hämta det aktuella tillståndet för en användare är värdena för egenskaperna för objektet som anges som värdet för parameterargumentet följande: 
  
* Identifierare: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Exempel 4. Fråga värdet för ett referensattribut som ska uppdateras*** 

Om ett referensattribut ska uppdateras frågar Azure Active Directory tjänsten för att avgöra om det aktuella värdet för referensattributet i det identitetsarkiv som tjänsten visar redan matchar värdet för det attributet i Azure Active Directory. För användare är det enda attributet som det aktuella värdet efterfrågas på det här sättet manager-attributet. Här är ett exempel på en begäran för att avgöra om chefsattributet för ett användarobjekt för närvarande har ett visst värde: I exempelkoden översätts begäran till ett anrop till QueryAsync-metoden för tjänstens provider. Värdet för egenskaperna för objektet som anges som värdet för parameterargumentet är följande: 
  
* Parametrar. AlternateFilters.Count: 2
* Parametrar. AlternateFilters.ElementAt(x). AttributePath: "ID"
* Parametrar. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* Parametrar. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parametrar. AlternateFilters.ElementAt(y). AttributePath: "manager"
* Parametrar. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* Parametrar. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* Parametrar. RequestedAttributePaths.ElementAt(0): "ID"
* Parametrar. SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Här kan värdet för indexet x vara 0 och värdet för indexet y kan vara 1, eller så kan värdet för x vara 1 och värdet för y kan vara 0, beroende på ordningen på uttrycken i filterfrågeparametern.   

***Exempel 5. Begäran från Azure AD till en SCIM-tjänst för att uppdatera en användare*** 

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

I exempelkoden översätts begäran till ett anrop till UpdateAsync-metoden för tjänstens provider. Här är signaturen för den metoden: 

```csharp
// System.Threading.Tasks.Tasks and 
// System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in
// Microsoft.SCIM.Service.
// Microsoft.SCIM.IPatch, 
// is defined in Microsoft.SCIM.Protocol. 

Task UpdateAsync(IRequest<IPatch> request);
```

I exemplet med en begäran om att uppdatera en användare har objektet som anges som värdet för korrigeringsargumentet följande egenskapsvärden: 

|Argument|Värde|
|-|-|
|ResourceIdentifier.Identifier|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier.SchemaIdentifier|"urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"|
|(PatchRequest som PatchRequest2). Operations.Count|1|
|(PatchRequest som PatchRequest2). Operations.ElementAt(0). OperationName|OperationName.Add|
|(PatchRequest som PatchRequest2). Operations.ElementAt(0). Path.AttributePath|"manager"|
|(PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.Count|1|
|(PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referens|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Värde| 2819c223-7f76-453a-919d-413861904646|

***Exempel 6. Avetablera en användare***

Om du vill avetablera en användare från ett identitetsarkiv som frontad av en SCIM-tjänst, skickar AAD en begäran, till exempel:

```
DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

I exempelkoden översätts begäran till ett anrop till deleteAsync-metoden för tjänstens provider. Här är signaturen för den metoden: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.IResourceIdentifier, 
// is defined in Microsoft.SCIM.Protocol. 

Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Objektet som anges som värdet för argumentet resourceIdentifier har dessa egenskapsvärden i exemplet på en begäran om att avetablera en användare: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>Integrera din SCIM-slutpunkt med AAD SCIM-klienten

Azure AD kan konfigureras för att automatiskt etablera tilldelade användare och grupper till program som implementerar en specifik profil för [SCIM 2.0-protokollet](https://tools.ietf.org/html/rfc7644). Information om profilen finns dokumenterade i Förstå [Azure AD SCIM-implementeringen.](#understand-the-aad-scim-implementation)

Kontrollera med din programleverantör eller programleverantörens dokumentation för att få information om kompatibilitet med dessa krav.

> [!IMPORTANT]
> Azure AD SCIM-implementeringen bygger på Azure AD-tjänsten för användareablering, som är utformad för att kontinuerligt hålla användarna synkroniserade mellan Azure AD och målprogrammet och implementerar en mycket specifik uppsättning standardåtgärder. Det är viktigt att förstå dessa beteenden för att förstå beteendet för Azure AD SCIM-klienten. Mer information finns i avsnittet Etableringscykler: [Initial och inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [i Så här fungerar etablering.](how-provisioning-works.md)

### <a name="getting-started"></a>Komma igång

Program som stöder SCIM-profilen som beskrivs i den här artikeln kan anslutas till Azure Active Directory med hjälp av funktionen "icke-galleriprogram" i Azure AD-programgalleriet. När du är ansluten kör Azure AD en synkroniseringsprocess var 40:e minut där den frågar programmets SCIM-slutpunkt efter tilldelade användare och grupper och skapar eller ändrar dem enligt tilldelningsinformationen.

**Så här ansluter du ett program som stöder SCIM:**

1. Logga in på [AAD-portalen](https://aad.portal.azure.com). Observera att du kan få åtkomst till en kostnadsfri utvärderingsversion Azure Active Directory med P2-licenser genom att registrera dig för [utvecklarprogrammet](https://developer.microsoft.com/office/dev-program)
1. Välj **Företagsprogram** i det vänstra fönstret. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
1. Välj **+ Nytt program**+ Skapa ett eget  >  **program.**
1. Ange ett namn för programmet, välj alternativet "*integrera* andra program som du inte hittar i galleriet" och välj **Lägg** till för att skapa ett appobjekt. Den nya appen läggs till i listan över företagsprogram och öppnas på dess skärm för apphantering.

   ![Skärmbild som visar Azure AD-programgalleriet ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *Azure AD-programgalleriet*

   > [!NOTE]
   > Om du använder den gamla appgalleriupplevelsen följer du skärmguiden nedan.
   
   ![Skärmbild som visar den gamla azure AD-appgalleriupplevelsen ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *för gamla Azure AD-appgalleri*

1. På apphanteringsskärmen väljer du **Etablering** i den vänstra panelen.
1. I menyn **Etableringsläge** väljer du **Automatiskt.**

   ![Exempel: Sidan Etablering för en app i Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurera etablering i Azure Portal*

1. I fältet **Klientorganisations-URL** anger du URL:en för programmets SCIM-slutpunkt. Exempel: `https://api.contoso.com/scim/`
1. Om SCIM-slutpunkten kräver en OAuth-bearer-token från en annan utfärdare än Azure AD kopierar du nödvändig OAuth-bearertoken till det valfria **fältet Hemlig token.** Om det här fältet lämnas tomt inkluderar Azure AD en OAuth-bearer-token som utfärdats från Azure AD med varje begäran. Appar som använder Azure AD som identitetsprovider kan verifiera denna Azure AD-utfärdade token. 
   > [!NOTE]
   > Vi rekommenderar inte ***att du*** lämnar det här fältet tomt och förlitar dig på en token som genereras av Azure AD. Det här alternativet är främst tillgängligt i testsyfte.
1. Välj **Testanslutning för** att Azure Active Directory försöker ansluta till SCIM-slutpunkten. Om försöket misslyckas visas felinformationen.  

    > [!NOTE]
    > **Testanslutning** frågar SCIM-slutpunkten efter en användare som inte finns, med hjälp av ett slumpmässigt GUID som matchande egenskap som valts i Azure AD-konfigurationen. Det förväntade korrekta svaret är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande.

1. Om försöken att ansluta till programmet lyckas väljer du Spara för **att** spara administratörsautentiseringsuppgifterna.
1. I avsnittet **Mappningar** finns det två valbara uppsättningar attributmappningar: [](customize-application-attributes.md)en för användarobjekt och en för gruppobjekt. Välj var och en för att granska de attribut som synkroniseras från Azure Active Directory till din app. Attributen som valts **som** Matchande egenskaper används för att matcha användare och grupper i din app för uppdateringsåtgärder. Välj **Spara** för att genomföra eventuella ändringar.

    > [!NOTE]
    > Om du vill kan du inaktivera synkronisering av gruppobjekt genom att inaktivera mappningen "grupper".

1. Under **Inställningar** definierar **fältet** Omfång vilka användare och grupper som synkroniseras. Välj **Synkronisera endast tilldelade användare och grupper** (rekommenderas) för att endast synkronisera användare och grupper som har tilldelats på fliken Användare **och** grupper.
1. När konfigurationen är klar anger du **Etableringsstatus** till **På.**
1. Välj **Spara** för att starta Azure AD-etableringstjänsten.
1. Om du bara synkroniserar tilldelade användare och grupper  (rekommenderas) måste du välja fliken Användare och grupper och tilldela de användare eller grupper som du vill synkronisera.

När den inledande cykeln har  startats kan du välja Etableringsloggar i den vänstra panelen för att övervaka förloppet, som visar alla åtgärder som utförts av etableringstjänsten i din app. Mer information om hur du läser Etableringsloggar för Azure AD finns i [Rapportering om automatisk användarkontoetablering.](check-status-user-account-provisioning.md)

> [!NOTE]
> Den inledande cykeln tar längre tid att utföra än senare synkroniseringar, vilket sker ungefär var 40:e minut så länge tjänsten körs.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Publicera programmet i AAD-programgalleriet

Om du skapar ett program som ska användas av fler än en klientorganisation kan du göra det tillgängligt i Azure AD-programgalleriet. Detta gör det enkelt för organisationer att identifiera programmet och konfigurera etablering. Det är enkelt att publicera appen i Azure AD-galleriet och göra etablering tillgänglig för andra. Kolla in stegen [här.](../develop/v2-howto-app-gallery-listing.md) Microsoft hjälper dig att integrera ditt program i vårt galleri, testa slutpunkten och publicera onboarding-dokumentation [som](../saas-apps/tutorial-list.md) kunder kan använda.

### <a name="gallery-onboarding-checklist"></a>Checklista för registrering av galleri
Använd checklistan för att snabbt registrera ditt program och kunderna får en smidig distributionsupplevelse. Informationen samlas in från dig när du börjar använda galleriet. 
> [!div class="checklist"]
> * Stöd för [en SCIM 2.0-användare](#understand-the-aad-scim-implementation) och gruppslutpunkt (endast en krävs men båda rekommenderas)
> * Stöd för minst 25 begäranden per sekund per klientorganisation för att säkerställa att användare och grupper etableras och avetableras utan fördröjning (krävs)
> * Upprätta tekniska och supportkontakter för att vägleda kunder efter registrering av galleri (krävs)
> * 3 Testautentiseringsuppgifter som inte upphör att gälla för ditt program (krävs)
> * Stöd för beviljande av OAuth-auktoriseringskod eller en långlivad token enligt beskrivningen nedan (krävs)
> * Upprätta en kontaktpunkt för teknik och support för att stödja kunder efter registrering av galleri (krävs)
> * [Stöd för schemaidentifiering (krävs)](https://tools.ietf.org/html/rfc7643#section-6)
> * Stöd för uppdatering av flera gruppmedlemskap med en enda PATCH
> * Dokumentera scim-slutpunkten offentligt

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Auktorisering för att etablera anslutningsappar i programgalleriet
SCIM-specifikationen definierar inte ett SCIM-specifikt schema för autentisering och auktorisering och förlitar sig på användning av befintliga branschstandarder.

|Auktoriseringsmetod|Fördelar|Nackdelar|Support|
|--|--|--|--|
|Användarnamn och lösenord (rekommenderas inte eller stöds av Azure AD)|Lätt att implementera|Osäker – Din [Pa$$word spelar ingen roll](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Stöds från fall till fall för galleriappar. Stöds inte för appar som inte är galleriappar.|
|Långlivad bearer-token|Långlivade token kräver inte att en användare finns. De är enkla för administratörer att använda när de ställer in etablering.|Långlivade token kan vara svåra att dela med en administratör utan att använda osäkra metoder som e-post. |Stöds för galleriappar och appar som inte är galleriappar. |
|Beviljande av OAuth-auktoriseringskod|Åtkomsttoken är mycket kortare livslängd än lösenord och har en automatisk uppdateringsmekanism som långlivade bearer-tokens inte har.  En verklig användare måste vara närvarande under den inledande auktoriseringen och lägga till en nivå av ansvar. |Kräver att en användare finns. Om användaren lämnar organisationen är token ogiltig och auktoriseringen måste slutföras igen.|Stöds för galleriappar, men inte för icke-galleriappar. Du kan dock ange en åtkomsttoken i användargränssnittet som hemlig token för kortsiktig testning. Stöd för beviljande av OAuth-kod på icke-galleri finns i vår listan över listan, förutom stöd för konfigurerbara autentiserings-/token-URL:er i galleriappen.|
|Beviljande av autentiseringsuppgifter för OAuth-klient|Åtkomsttoken är mycket kortare livslängd än lösenord och har en automatisk uppdateringsmekanism som långlivade bearer-tokens inte har. Både auktoriseringskoden som beviljas och de klientautentiseringsuppgifter som beviljas skapar samma typ av åtkomsttoken, så flytten mellan dessa metoder är transparent för API:et.  Etablering kan automatiseras helt och nya token kan begäras tyst utan användarinteraktion. ||Stöds inte för galleriappar och appar som inte är galleriappar. Supporten finns i vår eftersläpning.|

> [!NOTE]
> Vi rekommenderar inte att lämna tokenfältet tomt i det anpassade appgränssnittet för AAD-etableringskonfiguration. Den token som genereras är främst tillgänglig för testning.

### <a name="oauth-code-grant-flow"></a>Beviljandeflöde för OAuth-kod

Etableringstjänsten stöder beviljande [](https://tools.ietf.org/html/rfc6749#page-24) av auktoriseringskod. När du har skickat din begäran om att publicera din app i galleriet kommer vårt team att samarbeta med dig för att samla in följande information:

- **Auktoriserings-URL**, en URL från klienten för att få auktorisering från resursägaren via omdirigering av användaragenten. Användaren omdirigeras till den här URL:en för att auktorisera åtkomst. 

- **Tokenutbytes-URL**, en URL från klienten för att utbyta ett auktoriseringsbe beviljande för en åtkomsttoken, vanligtvis med klientautentisering.

- **Klient-ID**, auktoriseringsservern utfärdar en klientidentifierare till den registrerade klienten, som är en unik sträng som representerar den registreringsinformation som tillhandahålls av klienten.  Klientidentifieraren är inte en hemlighet. den exponeras för resursägaren och **får inte användas** enbart för klientautentisering.  

- **Klienthemlighet**, en hemlighet som genereras av auktoriseringsservern och som ska vara ett unikt värde som endast är känt för auktoriseringsservern. 

> [!NOTE]
> **Auktoriserings-URL:en** **och tokenutbytes-URL:en** kan för närvarande inte konfigureras per klientorganisation.

> [!NOTE]
> OAuth v1 stöds inte på grund av exponering av klienthemligheten. OAuth v2 stöds.  

Metodtips (rekommenderas, men krävs inte):
* Stöd för flera omdirigerings-URL:er. Administratörer kan konfigurera etablering från både "portal.azure.com" och "aad.portal.azure.com". Stöd för flera omdirigerings-URL:er säkerställer att användarna kan auktorisera åtkomst från någon av portalerna.
* Stöd för flera hemligheter för enkel förnyelse, utan driftstopp. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>Konfigurera flödet för beviljande av OAuth-kod

1. Logga in på Azure Portal, gå till **Programetablering**  >    >  **för företagsprogram** och välj **Auktorisera**.

   1. Azure Portal omdirigerar användaren till auktoriserings-URL:en (inloggningssidan för tredjepartsappen).

   1. Administratören anger autentiseringsuppgifter för programmet från tredje part. 

   1. Tredjepartsappen omdirigerar användaren tillbaka till Azure Portal och tillhandahåller beviljandekoden 

   1. Azure AD-etableringstjänster anropar token-URL:en och tillhandahåller beviljandekoden. Programmet från tredje part svarar med åtkomsttoken, uppdateringstoken och förfallodatum

1. När etableringscykeln börjar kontrollerar tjänsten om den aktuella åtkomsttoken är giltig och utbyter den för en ny token om det behövs. Åtkomsttoken anges i varje begäran som görs till appen och giltigheten för begäran kontrolleras före varje begäran.

> [!NOTE]
> Det går inte att konfigurera OAuth på icke-galleriprogram, men du kan generera en åtkomsttoken manuellt från auktoriseringsservern och ange den som hemlig token till ett program som inte är ett galleriprogram. På så sätt kan du kontrollera kompatibiliteten för din SCIM-server med AAD SCIM-klienten innan du börjar i appgalleriet, som stöder OAuth-kods grant.  

**Långlivade OAuth-bearer-token:** Om ditt program inte stöder beviljandeflödet för OAuth-auktoriseringskod genererar du i stället en långlivad OAuth-bearertoken som en administratör kan använda för att konfigurera etableringsintegreringen. Token ska vara permanent, annars sätts etableringsjobbet [i karantän](application-provisioning-quarantine-status.md) när token upphör att gälla.

Om du vill ha ytterligare autentiserings- och auktoriseringsmetoder kan du meddela oss [på UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Startkontrolllista för galleri till marknad
För att öka medvetenheten och efterfrågan på vår gemensamma integrering rekommenderar vi att du uppdaterar din befintliga dokumentation och förstärker integrationen i dina marknadsföringskanaler.  Nedan visas en uppsättning checklistaaktiviteter som vi rekommenderar att du slutför för att stödja lanseringen

> [!div class="checklist"]
> * Se till att sälj- och kundsupporten är medvetna, redo och kan tala med integreringsfunktionerna. Informera dina team, ge dem vanliga frågor och svar och ta med integreringen i ditt försäljningsmaterial. 
> * Skapa ett blogginlägg eller ett pressutgåde som beskriver den gemensamma integreringen, fördelarna och hur du kommer igång. [Exempel: Imprivata och Azure Active Directory press release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Använd dina sociala medier som Twitter, Facebook eller LinkedIn för att marknadsföra integreringen till dina kunder. Se till att inkludera @AzureAD så att vi kan retweeta ditt inlägg. [Exempel: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
> * Skapa eller uppdatera dina marknadsföringssidor/webbplatser (t.ex. integrationssida, partnersida, prissättningssida osv.) för att inkludera tillgängligheten för den gemensamma integreringen. [Exempel: Sidan för Pingboard-integration,](https://pingboard.com/org-chart-for) [Smartsheet-integrationssidan](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [Monday.com sidan med prisinformation](https://monday.com/pricing/) 
> * Skapa en hjälpcenterartikel eller teknisk dokumentation om hur kunder kan komma igång. [Exempel: Envoy + Microsoft Azure Active Directory integration.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Aviseringskunder om den nya integreringen via kundkommunikation (månatliga nyhetsbrev, e-postkampanjer, produktinformation). 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utveckla en SCIM-exempelslutpunkt](use-scim-to-build-users-and-groups-endpoints.md) 
>  [Automatisera användareablering och avetablering för SaaS-appar](user-provisioning.md) 
>  [Anpassa attributmappningar för användareablering](customize-application-attributes.md) 
>  [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md) 
>  [Omfångsfilter för användareablering](define-conditional-rules-for-provisioning-user-accounts.md) 
>  [Meddelanden om kontoetablering](user-provisioning.md) 
>  [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
