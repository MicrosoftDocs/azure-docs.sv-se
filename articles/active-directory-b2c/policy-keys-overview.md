---
title: Översikt över princip nycklar – Azure Active Directory B2C
description: Lär dig mer om vilka typer av krypterings princip nycklar som kan användas i Azure Active Directory B2C för signering och validering av tokens, klient hemligheter, certifikat och lösen ord.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ff3300935e90304754b5f2b375df0adecdaefa
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256250"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Översikt över princip nycklar i Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) lagrar hemligheter och certifikat i form av princip nycklar för att upprätta förtroende med de tjänster som den integrerar med. Dessa förtroenden består av:

- Externa identitetsprovidrar
- Ansluta till [REST API Services](restful-technical-profile.md)
- Tokensignering och kryptering

 I den här artikeln beskrivs vad du behöver veta om de princip nycklar som används av Azure AD B2C.

> [!NOTE]
> För närvarande är konfiguration av princip nycklar begränsad till [anpassade principer](./user-flow-overview.md) .

Du kan konfigurera hemligheter och certifikat för att upprätta förtroende mellan tjänster i Azure Portal under menyn **princip nycklar** . Nycklar kan vara symmetriska eller asymmetriska. *Symmetrisk* kryptering eller kryptering med privat nyckel är den plats där en delad hemlighet används för att både kryptera och dekryptera data. *Asymmetrisk* kryptering eller kryptering med offentliga nycklar är ett kryptografiskt system som använder par nycklar, som består av offentliga nycklar som delas med det förlitande part programmet och privata nycklar som är kända för Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Princip-Keys och nycklar

Resursen på den översta nivån för princip nycklar i Azure AD B2C är behållaren **nyckel uppsättning** . Varje nyckel uppsättning innehåller minst en **nyckel**. En nyckel har följande attribut:

| Attribut |  Obligatorisk | Kommentarer |
| --- | --- |--- |
| `use` | Ja | Användning: identifierar den avsedda användningen av den offentliga nyckeln. Kryptera data `enc` eller verifiera signaturen på data `sig` .|
| `nbf`| Inga | Datum och tid för aktivering. |
| `exp`| Inga | Förfallo datum och-tid. |

Vi rekommenderar att du ställer in värdena för nyckel aktivering och förfallo datum enligt dina PKI-standarder. Du kan behöva rotera certifikaten regelbundet för säkerhets-eller princip orsaker. Du kan till exempel ha en princip för att rotera alla dina certifikat varje år.

Om du vill skapa en nyckel kan du välja någon av följande metoder:

- Skapa en hemlighet **manuellt** med en sträng som du definierar. Hemligheten är en symmetrisk nyckel. Du kan ange aktiverings-och förfallo datum.
- **Genererad** – generera en nyckel automatiskt. Du kan ange aktiverings-och förfallo datum. Det finns två alternativ:
  - **Secret** – genererar en symmetrisk nyckel.
  - **RSA** – genererar ett nyckel par (asymmetriska nycklar).
- **Ladda upp** – Ladda upp ett certifikat eller en PKCS12 nyckel. Certifikatet måste innehålla privata och offentliga nycklar (asymmetriska nycklar).

## <a name="key-rollover"></a>Nyckel förnyelse

Av säkerhets synpunkt kan Azure AD B2C regelbundet förnya nycklar eller direkt i nödfall. Alla program, identitets leverantörer eller REST API som integreras med Azure AD B2C bör vara för beredda för att hantera en nyckel förnyelse händelse, oavsett hur ofta det kan uppstå. Annars Miss lyckas inloggnings förfrågan om ditt program eller Azure AD B2C försöker använda en utgången nyckel för att utföra en kryptografisk åtgärd.

Om en Azure AD B2C nyckel uppsättning har flera nycklar, är det bara en av nycklarna som är aktiv i taget, baserat på följande kriterier:

- Nyckel aktiveringen baseras på **aktiverings datumet**.
  - Nycklarna sorteras efter aktiverings datum i stigande ordning. Nycklar med aktiverings datum längre fram i framtiden visas lägre i listan. Nycklar utan aktiverings datum finns längst ned i listan.
  - När aktuellt datum och tid är större än en nyckels aktiverings datum aktiverar Azure AD B2C nyckeln och slutar använda den tidigare aktiva nyckeln.
- När den aktuella nyckelns förfallo tid har förflutit och nyckel behållaren innehåller en ny nyckel som är giltig för *inte före* och *utgångs* tid, aktive ras den nya nyckeln automatiskt.
- När den aktuella nyckelns förfallo tid har förflutit och nyckel containern *inte* innehåller en ny nyckel som är giltig för *inte före* och *utgångs* tid kan Azure AD B2C inte använda den utgångna nyckeln. Azure AD B2C får ett fel meddelande inom en beroende komponent i den anpassade principen. För att undvika det här problemet kan du skapa en standard nyckel utan aktiverings-och förfallo datum som säkerhets-net.
- Nyckelns slut punkt (JWKS-URI) för OpenId Connect-välkända konfigurations slut punkten visar de nycklar som kon figurer ATS i nyckel behållaren när nyckeln refereras till i den [tekniska JwtIssuer-profilen](./jwt-issuer-technical-profile.md). Ett program som använder ett OIDC-bibliotek kommer automatiskt att hämta dessa metadata så att de använder rätt nycklar för att verifiera token. Mer information finns i använda [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md), som alltid hämtar de senaste token signerings nycklar automatiskt.

## <a name="policy-key-management"></a>Hantering av princip nyckel

Om du vill hämta den aktuella aktiva nyckeln i en nyckel behållare använder du Microsoft Graph-API [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) -slutpunkten.

Lägga till eller ta bort signerings-och krypterings nycklar:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. På sidan Översikt, under **principer**, väljer du **Identity Experience Framework**.
1. Välj **princip nycklar** 
    1. Om du vill lägga till en ny nyckel väljer du **Lägg till**.
    1. Om du vill ta bort en ny nyckel markerar du nyckeln och väljer sedan **ta bort**. Om du vill ta bort nyckeln skriver du namnet på den nyckel behållare som ska tas bort. Azure AD B2C tar bort nyckeln och skapar en kopia av nyckeln med suffixet. bak.

### <a name="replace-a-key"></a>Ersätta en nyckel

Nycklarna i en nyckel uppsättning är inte utbytbara eller flyttbara. Om du behöver ändra en befintlig nyckel:

- Vi rekommenderar att du lägger till en ny nyckel med **aktiverings datumet** inställt på aktuellt datum och aktuell tid. Azure AD B2C aktiverar den nya nyckeln och slutar använda den tidigare aktiva nyckeln.
- Du kan också skapa en ny nyckel uppsättning med rätt nycklar. Uppdatera principen för att använda den nya nyckel uppsättningen och ta sedan bort den gamla nyckel uppsättningen. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder Microsoft Graph för att automatisera en [nyckel uppsättning](microsoft-graph-operations.md#trust-framework-policy-keyset) och distribution av [princip nycklar](microsoft-graph-operations.md#trust-framework-policy-key) .