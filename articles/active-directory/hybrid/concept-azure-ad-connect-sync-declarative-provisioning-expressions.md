---
title: 'Azure AD Connect: deklarativ etablerings uttryck | Microsoft Docs'
description: Förklarar deklarativ etablerings uttryck.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02490839a9e35695ae2e8b3f750e139ad7413aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280221"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect synkronisering: förstå uttryck för deklarativ etablering
Azure AD Connect Sync bygger på deklarativ etablering först i Forefront Identity Manager 2010. Du kan använda den fullständiga affärs logiken för identitets integrering utan att behöva skriva kompilerad kod.

En viktig del av deklarativ etablering är det uttrycks språk som används i Attribute-flöden. Språket som används är en del av Microsoft® Visual Basic® för program (VBA). Det här språket används i Microsoft Office och användare med erfarenhet av VBScript kan också identifiera det. Deklarativ etablerings uttrycks språk använder bara Functions och är inte ett strukturerat språk. Det finns inga metoder eller instruktioner. Funktionerna är i stället kapslade till Express-programflöde.

Mer information finns i [Välkommen till Visual Basic for Applications språk referens för Office 2013](/office/vba/api/overview/language-reference).

Attributen har strikt typ. En funktion accepterar bara attribut av rätt typ. Det är också Skift läges känsligt. Både funktions namn och attributnamn måste ha rätt Skift läge eller ett fel genereras.

## <a name="language-definitions-and-identifiers"></a>Språk definitioner och identifierare
* Funktioner har ett namn följt av argument inom hakparenteser: FunctionName (argument 1, argument N).
* Attribut identifieras av hakparenteser: [attributeName]
* Parametrarna identifieras med procent tecken:% ParameterName%
* String-konstanter omges av citat tecken: till exempel "contoso" (Obs: måste använda raka citat tecken "" och inte typografiska citat tecken "")
* Numeriska värden uttrycks utan citationstecken och förväntas vara decimal. Hexadecimala värden föregås av &H. Till exempel 98052, &HFF
* Booleska värden uttrycks med konstanter: true, false.
* Inbyggda konstanter och litteraler uttrycks med enbart sitt namn: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Deklarativ etablering använder många funktioner för att tillåta möjlighet att transformera attributvärden. Dessa funktioner kan kapslas så att resultatet från en funktion skickas till en annan funktion.

`Function1(Function2(Function3()))`

Du hittar en fullständig lista över funktioner i [funktions referensen](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametrar
En parameter definieras antingen av en anslutning eller av en administratör med hjälp av PowerShell. Parametrar innehåller vanligt vis värden som skiljer sig från system till system, till exempel namnet på den domän som användaren finns i. Dessa parametrar kan användas i Attribute-flöden.

Active Directory anslutningen angav följande parametrar för regler för inkommande synkronisering:

| Parameternamn | Kommentar |
| --- | --- |
| Domain. NetBIOS |NetBIOS-formatet för den domän som importeras för tillfället, till exempel FABRIKAMSALES |
| Domän. FQDN |FQDN-format för den domän som importeras för tillfället, till exempel sales.fabrikam.com |
| Domän. LDAP |LDAP-formatet för den domän som för närvarande importeras, till exempel DC = Sales, DC = Fabrikam, DC = com |
| Skog. NetBIOS |NetBIOS-format för det skogs namn som för närvarande importeras, till exempel FABRIKAMCORP |
| Skog. FQDN |FQDN-format för det skogs namn som för närvarande importeras, till exempel fabrikam.com |
| Skog. LDAP |LDAP-formatet för det skogs namn som för närvarande importeras, till exempel DC = Fabrikam, DC = com |

Systemet tillhandahåller följande parameter, som används för att hämta identifieraren för den koppling som körs för tillfället:  
`Connector.ID`

Här är ett exempel som fyller i metaversum-Attribute-domänen med NetBIOS-namnet för den domän där användaren finns:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatorer
Följande operatorer kan användas:

* **Jämförelse**: <, <=,  <>, =, >, >=
* **Matematik**: +,-, \* ,-
* **Sträng**: & (sammanfoga)
* **Logiskt**:  &&  (och), | | eller
* **Utvärderings ordning**: ()

Operatorerna utvärderas vänster till höger och har samma utvärderings prioritet. Det vill säga \* (multiplikatorn) utvärderas inte före-(subtraktion). 2 \* (5 + 3) är inte samma som 2 \* 5 + 3. Hakparenteserna () används för att ändra utvärderings ordningen när vänster till höger-utvärderings ordning inte är lämplig.

## <a name="multi-valued-attributes"></a>Attribut med flera värden
Funktionerna kan användas på både enkelvärdesattribut och attribut med flera värden. För attribut med flera värden fungerar funktionen över alla värden och tillämpar samma funktion på alla värden.

Exempel:  
`Trim([proxyAddresses])` Gör en trimning av alla värden i proxyAddress-attributet.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Ersätt domänen med för varje värde med @-sign @contoso.com .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Leta efter SIP-adressen och ta bort den från värdena.

## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurations modellen i att [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Se hur deklarativ etablering används direkt i [att förstå standard konfigurationen](concept-azure-ad-connect-sync-default-configuration.md).
* Se hur du gör en praktisk ändring med deklarativ etablering i [hur du ändrar standard konfigurationen](how-to-connect-sync-change-the-configuration.md).

**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

**Referensämnen**

* [Azure AD Connect Sync: Functions reference](reference-connect-sync-functions-reference.md)