---
title: Så här skapar du en Azure-attesterings princip
description: En förklaring av hur du skapar en attesterings princip.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3e36de62b79788e2efdc3e9abf711924c4fba0c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341815"
---
# <a name="how-to-author-an-attestation-policy"></a>Så här skapar du en policy för attestering

Attesterings principen är en fil som laddats upp för att Microsoft Azure attestering. Azure-attestering erbjuder flexibiliteten att ladda upp en princip i ett beskrivande princip format. Du kan också ladda upp en kodad version av principen i JSON-webbsignaturen. Princip administratören ansvarar för att skriva policyn för attestering. I de flesta attesterings scenarier fungerar den förlitande parten som princip administratör. Klienten som gör attesterings anropet skickar bevis för attestering, som tjänsten parsar och konverterar till inkommande anspråk (uppsättning egenskaper, värde). Tjänsten bearbetar sedan anspråken baserat på vad som definieras i principen och returnerar det beräknade resultatet.

Principen innehåller regler som avgör auktoriseringsregler, egenskaper och innehållet i attesterings-token. En exempel princip fil ser ut så här:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
En princip fil har tre segment, som du ser ovan:

- **version**: versionen är versions numret för den grammatik som följs. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    För närvarande är den enda version som stöds version 1,0.

- **authorizationrules**: en samling anspråks regler som ska kontrol leras först, för att avgöra om Azure-attesteringen bör fortsätta till **issuancerules**. Anspråks reglerna gäller i den ordning som de definieras.

- **issuancerules**: en samling anspråks regler som kommer att utvärderas för att lägga till ytterligare information till attesterings resultatet enligt vad som definieras i principen. Anspråks reglerna gäller i den ordning som de är definierade och är också valfria.

Mer information finns i [anspråk och anspråks regler](claim-rule-grammar.md) .
   
## <a name="drafting-the-policy-file"></a>Utkast till princip filen

1. Skapa en ny fil.
1. Lägg till version i filen.
1. Lägg till avsnitt för **authorizationrules** och **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Auktoriseringsregler innehåller åtgärden Deny () utan något villkor för att se till att inga regler för utfärdande har bearbetats. Dessutom kan auktoriseringsregeln även innehålla åtgärden Tillåt () för att tillåta bearbetning av utgivnings regler.
  
4. Lägg till anspråks regler i auktoriseringsregler

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Om den inkommande anspråks uppsättningen innehåller ett anspråk som matchar typ, värde och utfärdare, kommer åtgärden Tillåt () att instruera princip motorn att bearbeta **issuancerules**.
  
5. Lägg till anspråks regler i **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Den utgående anspråks uppsättningen kommer att innehålla ett anspråk med:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Komplexa principer kan användas på samma sätt. Mer information finns i [exempel på attesterings principer](policy-examples.md).
  
6. Spara filen.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Skapa princip filen i JSON Web Signature-format

När du har skapat en princip fil, för att överföra en princip i JWS-format, följer du stegen nedan.

1. Generera JWS, RFC 7515 med princip (UTF-8-kodad) som nytto lasten
     - Nytto Last-ID: t för den Base64Url kodade principen ska vara "AttestationPolicy".
     
     Exempel-JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. Valfritt Signera principen. Azure-attestering stöder följande algoritmer:
     - **Ingen**: Signera inte princip nytto lasten.
     - **RS256**: algoritm som stöds för att signera principens nytto Last

3. Ladda upp JWS och validera principen.
     - Om princip filen är fri från syntaxfel godkänns princip filen av tjänsten.
     - Om princip filen innehåller syntaxfel avvisas princip filen av tjänsten.

## <a name="next-steps"></a>Nästa steg
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
- [Intyga en SGX-enklaven med hjälp av kod exempel](/samples/browse/?expanded=azure&terms=attestation)