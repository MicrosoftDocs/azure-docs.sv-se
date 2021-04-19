---
title: Översikt över principnycklar – Azure Active Directory B2C
description: Lär dig mer om de typer av krypteringsprincipnycklar som kan användas i Azure Active Directory B2C för signering och validering av token, klienthemligheter, certifikat och lösenord.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a41717e9be0918dead9f77a5f5472494d734b38a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717539"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Översikt över principnycklar i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) lagrar hemligheter och certifikat i form av principnycklar för att upprätta förtroende med de tjänster som den integrerar med. Dessa förtroenden består av:

- Externa identitetsprovidrar
- Ansluta med [REST API tjänster](restful-technical-profile.md)
- Tokensignering och kryptering

 Den här artikeln beskriver vad du behöver veta om de principnycklar som används av Azure AD B2C.

> [!NOTE]
> För närvarande är konfigurationen av principnycklar begränsad till [endast anpassade](./user-flow-overview.md) principer.

Du kan konfigurera hemligheter och certifikat för att upprätta förtroende mellan tjänster i Azure Portal under **menyn Principnycklar.** Nycklarna kan vara symmetriska eller asymmetriska. *Symmetrisk* kryptografi, eller kryptografi med privat nyckel, är där en delad hemlighet används för att både kryptera och dekryptera data. *Asymmetrisk* kryptografi, eller kryptografi med offentlig nyckel, är ett kryptografiskt system som använder nyckelpar som består av offentliga nycklar som delas med förlitande partprogram och privata nycklar som endast är kända för Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Principnycklar och nycklar

Resursen på den översta nivån för principnycklar i Azure AD B2C är **Keyset-containern.** Varje nyckeluppsättning innehåller minst en **nyckel.** En nyckel har följande attribut:

| Attribut |  Obligatorisk | Kommentarer |
| --- | --- |--- |
| `use` | Yes | Användning: Identifierar den avsedda användningen av den offentliga nyckeln. Kryptera data `enc` eller verifiera signaturen för data `sig` .|
| `nbf`| No | Aktiveringsdatum och -tid. |
| `exp`| No | Förfallodatum och -tid. |

Vi rekommenderar att du anger nyckelaktiverings- och förfallovärden enligt dina PKI-standarder. Du kan behöva rotera dessa certifikat regelbundet av säkerhets- eller principskäl. Du kan till exempel ha en princip för att rotera alla certifikat varje år.

Om du vill skapa en nyckel kan du välja någon av följande metoder:

- **Manuell** – Skapa en hemlighet med en sträng som du definierar. Hemligheten är en symmetrisk nyckel. Du kan ange aktiverings- och förfallodatum.
- **Genererad** – Generera en nyckel automatiskt. Du kan ange aktiverings- och förfallodatum. Det finns två alternativ:
  - **Hemlighet** – genererar en symmetrisk nyckel.
  - **RSA** – Genererar ett nyckelpar (asymmetriska nycklar).
- **Ladda** upp – Ladda upp ett certifikat eller en PKCS12-nyckel. Certifikatet måste innehålla privata och offentliga nycklar (asymmetriska nycklar).

## <a name="key-rollover"></a>Nyckel-rollover

Av säkerhetsskäl kan Azure AD B2C över nycklar med jämna mellanrum eller omedelbart i nödfall. Alla program, identitetsproviders eller REST API som integreras med Azure AD B2C bör vara förberedda på att hantera en nyckel-rollover-händelse, oavsett hur ofta den kan ske. Om ditt program eller Azure AD B2C försöker använda en utgången nyckel för att utföra en kryptografisk åtgärd misslyckas inloggningsbegäran.

Om Azure AD B2C en nyckeluppsättning har flera nycklar är endast en av nycklarna aktiv åt gången, baserat på följande kriterier:

- Nyckelaktiveringen baseras på **aktiveringsdatumet**.
  - Nycklarna sorteras efter aktiveringsdatum i stigande ordning. Nycklar med aktiveringsdatum längre fram i framtiden visas längre ned i listan. Nycklar utan aktiveringsdatum finns längst ned i listan.
  - När aktuellt datum och tid är större än aktiveringsdatumet för en nyckel Azure AD B2C nyckeln och slutar använda den tidigare aktiva nyckeln.
- När den aktuella nyckelns förfallotid har gått ut och  nyckelcontainern innehåller en ny nyckel med giltig inte före och förfallotider, aktiveras den nya nyckeln automatiskt. 
- När den aktuella nyckelns förfallotid har  gått ut och nyckelcontainern  inte innehåller en ny nyckel med giltig inte före och förfallotider kan Azure AD B2C inte använda den utgångna nyckeln.  Azure AD B2C visas ett felmeddelande i en beroende komponent i din anpassade princip. För att undvika det här problemet kan du skapa en standardnyckel utan aktivering och förfallodatum som ett säkerhetsnät.
- Nyckelns slutpunkt (JWKS URI) för den välkända konfigurationsslutpunkten för OpenId Connect återspeglar de nycklar som konfigurerats i nyckelcontainern när nyckeln refereras till i den tekniska profilen [JwtIssuer.](./jwt-issuer-technical-profile.md) Ett program som använder ett OIDC-bibliotek hämtar automatiskt dessa metadata för att säkerställa att det använder rätt nycklar för att validera token. Mer information finns i Använda [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md), som alltid hämtar de senaste tokensigneringsnycklarna automatiskt.

## <a name="policy-key-management"></a>Hantering av principnyckel

Om du vill hämta den aktuella aktiva nyckeln i en nyckelcontainer använder Microsoft Graph API [getActiveKey-slutpunkten.](/graph/api/trustframeworkkeyset-getactivekey)

Så här lägger du till eller tar bort signerings- och krypteringsnycklar:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog +** prenumeration i portalens verktygsfält och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.
1. I Azure Portal du efter och väljer **Azure AD B2C**.
1. På översiktssidan går du till **Principer** och väljer **Identity Experience Framework**.
1. Välj **principnycklar** 
    1. Om du vill lägga till en ny nyckel väljer du **Lägg till**.
    1. Om du vill ta bort en ny nyckel markerar du nyckeln och väljer sedan Ta **bort.** Om du vill ta bort nyckeln skriver du namnet på nyckelcontainern som ska tas bort. Azure AD B2C tar bort nyckeln och skapar en kopia av nyckeln med suffixet .bak.

### <a name="replace-a-key"></a>Ersätta en nyckel

Nycklarna i en nyckeluppsättning kan inte bytas ut eller kan inte tas bort. Om du behöver ändra en befintlig nyckel:

- Vi rekommenderar att du lägger till en ny nyckel **med aktiveringsdatumet** inställt på aktuellt datum och tid. Azure AD B2C den nya nyckeln och slutar använda den tidigare aktiva nyckeln.
- Du kan också skapa en ny nyckeluppsättning med rätt nycklar. Uppdatera principen så att den använder den nya nyckeluppsättningen och ta sedan bort den gamla nyckeluppsättningen. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder Microsoft Graph för att automatisera en [distribution av nycklar och](microsoft-graph-operations.md#trust-framework-policy-keyset) [principnycklar.](microsoft-graph-operations.md#trust-framework-policy-key)

::: zone-end
