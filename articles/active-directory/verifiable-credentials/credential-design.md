---
title: Anpassa dina Azure Active Directory verifierbara autentiseringsuppgifter (för hands version)
description: Den här artikeln visar hur du skapar dina egna anpassade autentiseringsuppgifter för verifierbar
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a43e734c0a5bfa7c3698dcde5cb5b17f15575d90
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222936"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Anpassa dina verifierbara autentiseringsuppgifter (förhands granskning)

Verifierbara autentiseringsuppgifter består av två komponenter, reglerna och visnings filerna. Regel filen bestämmer vad användaren behöver ange innan de får verifierbara autentiseringsuppgifter. Visnings filen styr anpassningen av autentiseringsuppgifterna och formateringen av anspråken. I den här hand boken förklarar vi hur du ändrar båda filerna för att uppfylla kraven i din organisation. 

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Regelfil: krav från användaren

Regel filen är en enkel JSON-fil som beskriver viktiga egenskaper för verifierbara autentiseringsuppgifter. I synnerhet beskriver det hur anspråk används för att fylla dina verifierbara autentiseringsuppgifter.

Det finns för närvarande tre indatatyper som du kan konfigurera i regel filen. De här typerna används av tjänsten för att utfärda verifierbara autentiseringsuppgifter för att infoga anspråk i en verifierbar autentiseringsuppgift och intyga att informationen har genomförts. Följande är de tre typerna med förklaringar.

- ID-token
- Verifierbara autentiseringsuppgifter via en verifierbar presentation.
- Self-Attested anspråk

**ID-token:** Exempel appen och självstudien använder ID-token. När det här alternativet har kon figurer ATS måste du ange en öppen ID Connect-konfigurations-URI och inkludera de anspråk som ska tas med i VC. Användaren uppmanas att logga in i Authenticator-appen för att uppfylla det här kravet och lägga till associerade anspråk från sitt konto. 

**Autentiseringsuppgifter för verifierbar:** Slut resultatet av ett utfärdande flöde är att skapa en verifierbar autentiseringsuppgift, men du kan också be användaren att presentera en verifierbar autentiseringsuppgift för att kunna utfärda ett. Regel filen kan utföra vissa anspråk från den identifierade verifierbara autentiseringsuppgiften och inkludera dessa anspråk i den nyligen utfärdade verifierbara autentiseringsuppgiften från din organisation. 

**Självattesterings anspråk:** När det här alternativet är markerat kommer användaren att kunna skriva information direkt till autentiseraren. För närvarande är strängarna de enda inmatade anspråken för självattesterings anspråk. 

![detaljerad vy över kort för verifierbar autentiseringsuppgift](media/credential-design/issuance-doc.png) 

**Statiska anspråk:** Vi kan också deklarera ett statiskt anspråk i regel filen, men indatan kommer inte från användaren. Utfärdaren definierar ett statiskt anspråk i regel filen och ser ut som alla andra anspråk i verifierbara autentiseringsuppgifter. Lägg bara till en credentialSubject efter VC. type och deklarera attributet och anspråket. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Indatatyp: ID-token

För att hämta ID-token som indata måste regel filen konfigurera den välkända slut punkten för OIDC-kompatibla identitets systemet. I det systemet måste du registrera ett program med rätt information från [kommunikations exemplen för Issuer service](issuer-openid.md). Dessutom måste client_id placeras i regel filen, samt en omfattnings parameter måste fyllas i med rätt omfång. Azure Active Directory behöver till exempel e-postscopet om du vill returnera ett e-postanspråk i ID-token.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Egenskap | Beskrivning |
| -------- | ----------- |
| `attestations.idTokens` | En matris med OpenID ansluter identitets leverantörer som stöds för käll användar information. |
| `...mapping` | Ett objekt som beskriver hur anspråk i varje ID-token mappas till attribut i de resulterande verifierbara autentiseringsuppgifterna. |
| `...mapping.{attribute-name}` | Det attribut som ska vara ifyllt i den resulterande verifierbara autentiseringsuppgiften. |
| `...mapping.{attribute-name}.claim` | Anspråk i ID-tokens vars värde ska användas för att fylla attributet. |
| `...configuration` | Platsen för identitets leverantörens konfigurations dokument. URL: en måste följa [OpenID Connect-standarden för identitets leverantörens metadata](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). Konfigurations dokumentet måste innehålla `issuer` fälten, `authorization_endpoint` , `token_endpoint` och `jwks_uri` . |
| `...client_id` | Det klient-ID som hämtades under klient registrerings processen. |
| `...scope` | En blankstegsavgränsad lista över omfattningar som IDP måste kunna returnera rätt anspråk i ID-token. |
| `...redirect_uri` | Måste alltid använda värdet `vcclient://openid/` . |
| `validityInterval` | En tids period, i sekunder, som representerar livs längden för dina verifierbara autentiseringsuppgifter. När den här tids perioden har löpt ut är den verifierbara autentiseringsuppgiften inte längre giltig. Om du utelämnar det här värdet kommer alla verifierbara autentiseringsuppgifter att förbli giltiga tills den återkallas explicit. |
| `vc.type` | En sträng mat ris som visar de scheman som din verifierbara autentiseringsuppgift uppfyller. Mer information finns i avsnittet nedan. |

### <a name="vctype-choose-credential-types"></a>VC. Type: Välj inloggnings typ (er) 

Alla verifierbara autentiseringsuppgifter måste deklarera sina "Type" i sin regel fil. Typen av autentiseringsuppgift särskiljer dina verifierbara autentiseringsuppgifter från autentiseringsuppgifter som utfärdats av andra organisationer och säkerställer samverkan mellan utfärdare och verifierare. Om du vill ange en typ av autentiseringsuppgift måste du ange en eller flera autentiseringstyper som autentiseringsuppgiften uppfyller. Varje typ representeras av en unik sträng – ofta används en URI för att säkerställa global unikhet. URI: n behöver inte vara adresser bara. den behandlas som en sträng. 

Till exempel kan ett diplom som utfärdats av Contoso University deklarera följande typer:

| Typ | Syfte |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Förklarar att utbildningsprogram som utfärdats av Contoso University innehåller attribut som definieras av schema. org- `EducationaCredential` objektet. |
| `https://schemas.ed.gov/universityDiploma2020` | Deklarerar att utbildningsprogram som utfärdats av Contoso University innehåller attribut som definieras av USA Department of Education. |
| `https://schemas.contoso.edu/diploma2020` | Förklarar att utbildningsprogram som utfärdats av Contoso University innehåller attribut som definierats av Contoso University. |

Genom att deklarera alla tre typerna kan Contoso Universitys utbildnings bevis användas för att uppfylla olika förfrågningar från verifierare. En bank kan begära en uppsättning `EducationCredential` s från en användare och det kan användas för att uppfylla begäran. Men Contoso University studenter-associationen kan begära autentiseringsuppgifter av typen `https://schemas.contoso.edu/diploma2020` , och utbildningsen kommer också att uppfylla begäran.

För att säkerställa samverkan mellan dina autentiseringsuppgifter rekommenderar vi att du arbetar nära relaterade organisationer för att definiera autentiseringstyper, scheman och URI: er för användning i din bransch. Många bransch organisationer ger vägledning om strukturen hos officiella dokument som kan användas för att definiera innehållet i verifierbara autentiseringsuppgifter. Du bör också ha ett nära samarbete med verifierarna för dina autentiseringsuppgifter för att förstå hur de ska begära och använda dina verifierbara autentiseringsuppgifter.

## <a name="input-type-verifiable-credential"></a>Indatatyp: verifierbara autentiseringsuppgifter

>[!NOTE]
>Regelfiler som frågar efter verifierbara autentiseringsuppgifter använder inte presentationens Exchange-format för att begära autentiseringsuppgifter. Detta kommer att uppdateras när den utfärdande tjänsten har stöd för standard manifestet för autentiseringsuppgifter. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Egenskap | Beskrivning |
| -------- | ----------- |
| `attestations.presentations` | En matris med verifierbara autentiseringsuppgifter som begärs som indata. |
| `...mapping` | Ett objekt som beskriver hur anspråk i varje bevisad verifierbar autentiseringsuppgift mappas till attribut i den resulterande verifierbara autentiseringsuppgiften. |
| `...mapping.{attribute-name}` | Det attribut som ska vara ifyllt i den resulterande verifierbara autentiseringsuppgiften. |
| `...mapping.{attribute-name}.claim` | Anspråket i den verifierbara autentiseringsuppgiften vars värde ska användas för att fylla attributet. |
| `...mapping.{attribute-name}.indexed` | Endast en kan vara aktive rad per verifierbara autentiseringsuppgifter som ska sparas för återkallning. Mer information finns i [artikeln om hur du återkallar autentiseringsuppgifter](how-to-issuer-revoke.md) . |
| `credentialType` | CredentialType för den verifierbara autentiseringsuppgift som du ber användaren att presentera. |
| `contracts` | URI för kontraktet i portalen för verifierbara autentiseringsuppgifter. |
| `issuers.iss` | Utfärdaren för den verifierbara autentiseringsuppgiften där användaren tillfrågades. |
| `validityInterval` | En tids period, i sekunder, som representerar livs längden för dina verifierbara autentiseringsuppgifter. När den här tids perioden har löpt ut är den verifierbara autentiseringsuppgiften inte längre giltig. Om du utelämnar det här värdet kommer alla verifierbara autentiseringsuppgifter att förbli giltiga tills den återkallas explicit. |
| `vc.type` | En sträng mat ris som visar de scheman som din verifierbara autentiseringsuppgift uppfyller. |


## <a name="input-type-self-attested-claims"></a>Indatatyp: självattesterings anspråk

Under det utfärdande flödet kan användaren uppmanas att ange en viss självattesterings information. Från och med nu är den enda indatatypen en sträng. 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Egenskap | Beskrivning |
| -------- | ----------- |
| `attestations.selfIssued` | En matris med självutfärdade anspråk som kräver indata från användaren. |
| `...mapping` | Ett objekt som beskriver hur självutfärdade anspråk mappas till attribut i de resulterande verifierbara autentiseringsuppgifterna. |
| `...mapping.alias` | Det attribut som ska vara ifyllt i den resulterande verifierbara autentiseringsuppgiften. |
| `...mapping.alias.claim` | Anspråket i den verifierbara autentiseringsuppgiften vars värde ska användas för att fylla attributet. |
| `validityInterval` | En tids period, i sekunder, som representerar livs längden för dina verifierbara autentiseringsuppgifter. När den här tids perioden har löpt ut är den verifierbara autentiseringsuppgiften inte längre giltig. Om du utelämnar det här värdet kommer alla verifierbara autentiseringsuppgifter att förbli giltiga tills den återkallas explicit. |
| `vc.type` | En sträng mat ris som visar de scheman som din verifierbara autentiseringsuppgift uppfyller. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Visa fil: verifierbara autentiseringsuppgifter i Microsoft Authenticator

Verifierbara autentiseringsuppgifter erbjuder en begränsad uppsättning alternativ som kan användas för att återspegla ditt varumärke. Den här artikeln innehåller anvisningar för hur du anpassar dina autentiseringsuppgifter och metod tips för att utforma autentiseringsuppgifter som ser bra ut när de har utfärdats till användare.

Verifierbara autentiseringsuppgifter som utfärdats till användare visas som kort i Microsoft Authenticator. Som administratör kan du välja kort färg, ikon och text strängar som passar din organisations märke.

![dokumentation om utfärdande](media/credential-design/detailed-view.png) 

Kort innehåller också anpassningsbara fält som du kan använda för att låta användare veta syftet med kortet, de attribut som det innehåller och mer.

## <a name="create-a-credential-display-file"></a>Skapa en visnings fil för autentiseringsuppgifter

På samma sätt som regel filen är visnings filen en enkel JSON-fil som beskriver hur innehållet i dina verifierbara autentiseringsuppgifter ska visas i Microsoft Authenticator-appen. 

>[!NOTE]
> För närvarande används den här visnings modellen bara av Microsoft Authenticator.

Visnings filen har följande struktur.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Egenskap | Beskrivning |
| -------- | ----------- |
| `locale` | Språket för verifierbara autentiseringsuppgifter. Reserverad för framtida användning. | 
| `card.title` | Visar typen av autentiseringsuppgift för användaren. Rekommenderad maxlängd på 25 tecken. | 
| `card.issuedBy` | Visar namnet på den utfärdande organisationen för användaren. Rekommenderad maxlängd på 40 tecken. |
| `card.backgroundColor` | Anger kortets bakgrunds färg i hex-format. En diskret övertoning kommer att användas på alla kort. |
| `card.textColor` | Anger kortets text färg i hex-format. Rekommenderas för att använda svart eller vitt. |
| `card.logo` | En logo typ som visas på kortet. Den angivna URL: en måste vara offentligt adresserbar. Rekommenderad maximal höjd på 36 BPT och maximal bredd på 100 px oavsett telefon storlek. Rekommendera PNG med genomskinlig bakgrund. | 
| `card.description` | Kompletterande text som visas bredvid varje kort. Kan användas för alla ändamål. Rekommenderad maxlängd på 100 tecken. |
| `consent.title` | Kompletterande text som visas när ett kort utfärdas. Används för att ange information om utgivnings processen. Rekommenderad längd på 100 tecken. |
| `consent.instructions` | Kompletterande text som visas när ett kort utfärdas. Används för att ange information om utgivnings processen. Rekommenderad längd på 100 tecken. |
| `claims` | Gör att du kan ange etiketter för attribut som ingår i varje autentiseringsuppgift. |
| `claims.{attribute}` | Anger attributet för den autentiseringsuppgift som etiketten gäller. |
| `claims.{attribute}.type` | Anger attributtypen. För närvarande stöder vi endast String. |
| `claims.{attribute}.label` | Det värde som ska användas som etikett för attributet, som visas i autentiseraren. Detta kanske skiljer sig från den etikett som användes i regel filen. Rekommenderad maxlängd på 40 tecken. |

>[!NOTE]
  >Om ett anspråk ingår i regel filen och sedan utelämnas i visnings filen, finns det två olika typer av upplevelser. På iOS visas inte anspråket i detalj avsnittet som visas i bilden ovan, medan anspråket visas i Android.  

## <a name="next-steps"></a>Nästa steg

Nu har du en bättre förståelse för design av verifierbara autentiseringsuppgifter och hur du kan skapa egna som passar dina behov.

- [Kommunikations exempel för Issuer service](issuer-openid.md)
