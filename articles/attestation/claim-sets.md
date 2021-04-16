---
title: Azure Attestation anspråksuppsättningar
description: Anspråksuppsättningarna för Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517563"
---
# <a name="claim-sets"></a>Anspråksuppsättningar

Anspråk som genereras under processen för attestera enklaver med hjälp Microsoft Azure attestering kan delas in i följande kategorier:

- **Inkommande anspråk:** Anspråk som genereras av Microsoft Azure Attestation efter parsning av attestation bevis och kan användas av principförfattare för att definiera auktoriseringsregler i en anpassad princip

- **Utgående anspråk:** Anspråk som genereras av Azure Attestation och ingår i attestationstoken

- **Egenskapsanspråk:** Anspråken som skapas som utdata av Azure Attestation. Den innehåller alla anspråk som representerar egenskaper för attestationstoken, till exempel kodning av rapporten, rapportens giltighetstid och så vidare.

## <a name="incoming-claims"></a>Inkommande anspråk 

### <a name="sgx-attestation"></a>SGX-attestation

Anspråk som ska användas av principförfattare för att definiera auktoriseringsregler i en SGX-atterstationsprincip:

- **x-ms-sgx-is-debuggable:** Ett booleskt värde som anger om enklavfelsökning är aktiverat eller inte.
  
  SGX-enklaver kan läsas in med felsökning inaktiverat eller aktiverat. När flaggan är inställd på true i enklaven aktiveras felsökningsfunktioner för enklavkoden. Detta inkluderar möjligheten att komma åt enklavens minne. Därför rekommenderar vi att du endast ställer in flaggan på sant i utvecklingssyfte. Om det är aktiverat i produktionsmiljön behålls inte SGX-säkerhetsgarantier.
  
  Azure Attestation kan använda atterstationsprincipen för att verifiera om felsökning har inaktiverats för SGX-enklaven. När principregeln har lagts till misslyckas attestation när en obehörig användare aktiverar felsökningsstöd för att få åtkomst till enklavinnehållet.

- **x-ms-sgx-product-id:** Ett heltalsvärde som anger produkt-ID för SGX-enklaven.

  Enklavförfattaren tilldelar ett produkt-ID till varje enklav. Med produkt-ID:t kan enklavförfattaren segmentera enklaver som signerats med samma MRSIGNER. Genom att lägga till en valideringsregel i attestationsprincipen kan kunder kontrollera om de använder de avsedda enklaverna. Attestation misslyckas om enklavens produkt-ID inte matchar värdet som publicerats av enklavförfattaren.

- **x-ms-sgx-mrsigner:** Ett strängvärde som identifierar författaren till SGX-enklaven.

  MRSIGNER är hashen för enklavförfattarens offentliga nyckel som används för att signera enklav-binärtecknet. Genom att verifiera MRSIGNER via en attestationsprincip kan kunder kontrollera om betrodda binärfiler körs inuti en enklav. Om principanspråk inte matchar enklavförfattarens MRSIGNER, innebär det att enklav-binärfilen inte har signerats av en betrodd källa och attestation misslyckas.
  
  När en enklavförfattare föredrar att rotera MRSIGNER av säkerhetsskäl måste Azure Attestation-principen uppdateras för att stödja de nya och gamla MRSIGNER-värdena innan binärfilerna uppdateras. Annars misslyckas auktoriseringskontroller, vilket resulterar i attestationsfel.
  
  Attestationsprincipen måste uppdateras med formatet nedan. 
 
  #### <a name="before-key-rotation"></a>Före nyckelrotation
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Under nyckelrotation

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Efter nyckelrotation

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave:** Ett strängvärde som identifierar koden och data som läses in i enklavminnet. 

  MRENCLAVE är en av enklavmåtten som kan användas för att verifiera enklav binärfilerna. Det är hashen för koden som körs i enklaven. Måttet ändras med varje ändring i enklavens binära kod. Genom att verifiera MRENCLAVE via en attestationsprincip kan kunder kontrollera om avsedda binärfiler körs inuti en enklav. Men eftersom MRENCLAVE förväntas ändras ofta med eventuella triviala ändringar i den befintliga koden rekommenderar vi att du verifierar enklav binärfiler med hjälp av MRSIGNER-validering i en attestationsprincip.

- **x-ms-sgx-svn:** Ett heltalsvärde som anger säkerhetsversionsnumret för SGX-enklaven

  Enklavförfattaren tilldelar ett säkerhetsversionsnummer (SVN) till varje version av SGX-enklaven. När ett säkerhetsproblem identifieras i enklavkoden ökar enklavförfattaren SVN-värdet efter sårbarhetskorrigeringen. För att förhindra interaktion med osäker enklavkod kan kunder lägga till en valideringsregel i attestationsprincipen. Om enklavkodens SVN inte matchar den version som rekommenderas av enklavförfattaren misslyckas attestation.

Nedanstående anspråk betraktas som inaktuella men stöds fullt ut och kommer att fortsätta att tas med i framtiden. Vi rekommenderar att du använder inaktuella anspråksnamn.

Inaktuellt anspråk | Rekommenderat anspråk
--- | --- 
$is debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>TPM-attestering

Anspråk som ska användas av principförfattare för att definiera auktoriseringsregler i en TPM-attestationsprincip:

- **aikValidated:** Booleskt värde som innehåller information om attestation Identity Key (AIK)-certifikatet har verifierats eller inte
- **aikPubHash:** Sträng som innehåller base64(SHA256(offentlig AIK-nyckel i DER-format))
- **tpmVersion:** Heltalsvärde som innehåller huvudversionen Trusted Platform Module (TPM)
- **secureBootEnabled:** Booleskt värde för att ange om säker start är aktiverat
- **iommuEnabled:** Booleskt värde för att ange om Iommu (Input-Output Memory Management Unit) är aktiverat
- **bootDebuggingDisabled:** Booleskt värde för att ange om startfelsökning är inaktiverat
- **notSafeMode:** Booleskt värde för att ange om Windows inte körs i felsäkert läge
- **notWinPE:** Booleskt värde som anger om Windows inte körs i WinPE-läge
- **vbsEnabled:** Booleskt värde som anger om VBS är aktiverat
- **vbsReportPresent:** Booleskt värde som anger om VBS-enklavrapporten är tillgänglig

### <a name="vbs-attestation"></a>VBS-attestation

Förutom anspråken för TPM-attestationsprincipen kan anspråk nedan användas av principförfattare för att definiera auktoriseringsregler i en VBS-attestationsprincip.

- **enclaveAuthorId:** Strängvärde som innehåller det Base64Url-kodade värdet för enklavförfattaren id – Den primära modulens identifierare för enklaven
- **enclaveImageId:** Strängvärde som innehåller base64Url-kodat värde för enklavavbildnings-ID:t – bildidentifieraren för den primära modulen för enklaven
- **enclaveOwnerId:** Strängvärde som innehåller base64Url-kodat värde för enklavägarens ID – Identifieraren för enklavens ägare
- **enclaveFamilyId:** Strängvärde som innehåller det Base64Url-kodade värdet för enklavfamiljens ID. Familjeidentifieraren för enklavens primära modul
- **enclaveSvn:** Heltalsvärde som innehåller säkerhetsversionsnumret för den primära modulen för enklaven
- **enclavePlatformSvn:** Heltalsvärde som innehåller säkerhetsversionsnumret för plattformen som är värd för enklaven
- **enclaveFlags:** Enklavflags-anspråket är ett heltalsvärde som innehåller flaggor som beskriver körningsprincipen för enklaven

## <a name="outgoing-claims"></a>Utgående anspråk 

### <a name="common-for-all-attestation-types"></a>Gemensamt för alla attestationstyper

Azure Attestation anspråken nedan i attestationstoken för alla attestationstyper. 

- **x-ms-ver:** JWT-schemaversion (förväntas vara "1.0")
- **x-ms-attestation-type:** Strängvärde som representerar attestationstyp 
- **x-ms-policy-hash:** Hash för Azure Attestation-utvärderingsprincip som beräknas som BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text)))))
- **x-ms-policy-signer: JSON-objekt** med en "jwk"-medlem som representerar nyckeln som en kund använde för att signera sin princip. Detta gäller när kunden laddar upp en signerad princip

Nedanstående anspråksnamn används från [IETF JWT-specifikationen](https://tools.ietf.org/html/rfc7519)

- **"jti" (JWT ID) Anspråk** – unik identifierare för JWT
- **"iss" (issuer) anspråk –** det huvudnamn som utfärdade JWT 
- **"iat"-anspråk (utfärdat vid)** – den tid då JWT utfärdades 
- **"exp"-anspråk (förfallotid)** – Förfallotid efter vilken JWT inte får godkännas för bearbetning
- **"nbf" (inte före) anspråk** – inte före den tid innan JWT inte får godkännas för bearbetning 

Nedanstående anspråksnamn används från [IETF EAT utkastspecifikation](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce claim" (nonce)** – En oöversatt direktkopia av ett valfritt nonce-värde som tillhandahålls av en klient 

Nedanstående anspråk betraktas som inaktuella men stöds fullt ut och kommer att fortsätta att tas med i framtiden. Vi rekommenderar att du använder inaktuella anspråksnamn.

Inaktuellt anspråk | Rekommenderat anspråk
--- | --- 
Ver | x-ms-ver
Tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>SGX-attestation 

Anspråken nedan genereras och inkluderas i attestationstoken av tjänsten för SGX-attestation.

- **x-ms-sgx-is-debuggable:** Ett booleskt tecken som anger om enklaven har felsökning aktiverat eller inte
- **x-ms-sgx-product-id:** Produkt-ID-värdet för SGX-enklaven 
- **x-ms-sgx-mrsigner:** hexkodat värde för fältet "mrsigner" för offerten
- **x-ms-sgx-mrenclave:** hexkodat värde för fältet "mrenclave" för offerten
- **x-ms-sgx-svn:** säkerhetsversionsnummer kodat inom citattecken 
- **x-ms-sgx-yed:** enklavhållna data formaterade som BASE64URL(enklavhållna data)
- **x-ms-sgx-collateral: JSON-objekt** som beskriver de tillgångar som används för att utföra attestation. Värdet för anspråket x-ms-sgx-collateral är ett kapslat JSON-objekt med följande nyckel/värde-par:
    - **qeidcertshash:** SHA256-värdet för den utfärdande QE-identiteten (Quoting Enclave)
    - **qeidcrlhash:** SHA256-värdet för listan över återkallade certifikat för QE-identitet som utfärdar certifikat
    - **qeidhash:** SHA256-värdet för QE Identity-beteckning
    - **quotehash:** SHA256-värdet för det utvärderade citattecken
    - **tcbinfocertshash:** SHA256-värdet för TCB Info utfärdande certifikat
    - **tcbinfocrlhash:** SHA256-värdet för listan över TCB Info utfärdande certifikat CRL
    - **tcbinfohash:** SHA256-värdet för TCB Info-förseningen

Nedanstående anspråk betraktas som inaktuella men stöds fullt ut och kommer att fortsätta att tas med i framtiden. Vi rekommenderar att du använder inaktuella anspråksnamn.

Inaktuellt anspråk | Rekommenderat anspråk
--- | --- 
$is debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-ig

### <a name="tpm-and-vbs-attestation"></a>TPM- och VBS-attestation

- **cnf (bekräftelse):**"cnf"-anspråket används för att identifiera innehavarbevisnyckeln. Bekräftelseanspråk enligt definitionen i RFC 7800 innehåller den offentliga delen av den attesterade enklavnyckeln som representeras som ett JSON Web Key-objekt (JWK) (RFC 7517)
- **rp_data (förlitande partsdata):** Förlitande partsdata, om några, som anges i begäran, som används av den förlitande parten som en nonce för att garantera rapportens freshness. rp_data läggs bara till om det finns rp_data

## <a name="property-claims"></a>Egenskapsanspråk

### <a name="tpm-and-vbs-attestation"></a>TPM- och VBS-attestation

- **report_validity_in_minutes:** Ett heltalsanspråk som visar hur länge token är giltig.
  - **Standardvärde(tid):** En dag i minuter.
  - **Maximalt värde(tid):** Ett år i minuter.
- **omit_x5c:** Ett booleskt anspråk som anger om Azure Attestation ska utelämna det certifikat som används för att bevisa tjänstens äkthet. Om sant läggs x5t till i atterstationstoken. Om det är false (standard) läggs x5c till i attestationstoken.

## <a name="next-steps"></a>Nästa steg
- [Så här skapar och signerar du en attestationsprincip](author-sign-policy.md)
- [Konfigurera Azure Attestation PowerShell](quickstart-powershell.md)
