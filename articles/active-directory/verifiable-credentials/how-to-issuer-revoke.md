---
title: Återkalla en verifierbar autentiseringsuppgift som en utfärdare – Azure Active Directory verifierbara autentiseringsuppgifter
description: Läs om hur du återkallar en verifierbar autentiseringsuppgift som du har utfärdat
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222851"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Återkalla tidigare utfärdade verifierbara autentiseringsuppgifter (för hands version)

Som en del av processen att arbeta med verifierbara autentiseringsuppgifter (VCs) behöver du inte bara utfärda autentiseringsuppgifter, men ibland måste du också återkalla dem. I den här artikeln går vi igenom **status** egenskaps delen av VC-specifikationen och tar en närmare titt på återkallnings processen, varför vi kan vilja återkalla autentiseringsuppgifter och vissa data-och sekretess aspekter.

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Status egenskap i specifikationen för verifierbara autentiseringsuppgifter

Innan vi kan förstå konsekvenserna av att återkalla en verifierbar autentiseringsuppgift kan det vara bra att veta vad **status kontrollen** är och hur den fungerar idag.

I [specifikationen W3C verifierbara autentiseringsuppgifter](https://www.w3.org/TR/vc-data-model/) refererar du till egenskapen **status** i avsnitt [4,9:](https://www.w3.org/TR/vc-data-model/#status)

"Den här specifikationen definierar följande **credentialStatus** -egenskap för identifiering av information om den aktuella statusen för en verifierbar autentiseringsuppgift, till exempel om den är inaktive rad eller återkallad."

W3C-specifikationen definierar dock inte något format för hur **status kontrollen** ska implementeras.

"Definiering av data modellen, format och protokoll för status scheman ligger utanför omfånget för den här specifikationen. Ett register för verifierbara autentiseringsuppgifter för tillägg [VC-EXTENSION-REGISTRY] finns som innehåller tillgängliga status scheman för implementer som vill implementera status kontroll för verifierbara autentiseringsuppgifter. "

>[!NOTE]
>För tillfället är Microsofts status kontroll implementering patentskyddad men vi arbetar aktivt med den framställda communityn för att justera på en standard.

## <a name="how-does-the-status-property-work"></a>Hur fungerar **status** egenskapen?

I alla Microsoft utfärdade verifierbara autentiseringsuppgifter finns ett attribut med namnet credentialStatus. Den fylls med ett status-API som Microsoft hanterar för din räkning. Här är ett exempel på det som ser ut.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

SDK: er för verifierbara autentiseringsuppgifter för öppen källkod hanterar anrop av status-API och tillhandahåller nödvändiga data.

När API: et anropas och angav rätt information, returnerar API: t antingen true eller false. True är att den verifierbara autentiseringsuppgiften fortfarande är aktiv med utfärdaren och falskt som bevisar att den verifierbara autentiseringsuppgiften har återkallats aktivt av utfärdaren.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Varför du kanske vill återkalla en VC?

Varje kund har sina egna unika skäl för att vilja återkalla en verifierbar autentiseringsuppgift, men här är några av de vanliga teman som vi har hört, hittills. 

- Student-ID: studenten är inte längre en aktiv student på University.
- Medarbetar-ID: medarbetaren är inte längre en aktiv medarbetare.
- Tillstånds driv rutins licens: driv rutinen finns inte längre i det aktuella läget.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Så här konfigurerar du en verifierbar autentiseringsuppgift med möjlighet att återkalla

Alla verifierbara autentiseringsuppgifter lagras inte med Microsoft som standard. Därför har vi inga data att referera till för att återkalla ett särskilt ID för verifierbara autentiseringsuppgifter. Utfärdaren måste ange ett särskilt fält från attributet verifierbar Credential för Microsoft till index och därefter salt och hash.

>[!NOTE]
>Hashing är en krypterings åtgärd på ett sätt som aktiverar indata, som kallas för ```preimage``` och genererar utdata som kallas en hash som har en fast längd. Det är inte beräknings mässigt genomförbart för tillfället att återföra en hash-åtgärd.

Du kan ange Microsoft vilket attribut för de verifierbara autentiseringsuppgifter som du vill indexera. Indirekt indexering är att indexerade värden kan användas för att söka efter de autentiseringsuppgifter som du vill återkalla.

**Exempel:** Alice är en Sparbanks medarbetare. Alice till vänster för att arbeta på contoso. Jane, IT-administratören för Sparbanken, söker efter Alices e-post i frågan om att återkalla en verifierbar behörighet. I det här exemplet är Jane, indexerat e-postfältet för den Sparbank som verifierats som autentiseringsuppgifter för anställd. 

Nedan visas ett exempel på hur regel filen ändras för att inkludera indexet.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Endast ett attribut kan indexeras från en regel fil.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Hur gör jag för att återkalla en verifierbar autentiseringsuppgift

När ett index anspråk har ställts in och verifierbara autentiseringsuppgifter har utfärdats till dina användare, är det dags att se hur du kan återkalla en verifierbar autentiseringsuppgift på VC-bladet.

1. Gå till bladet verifierbara autentiseringsuppgifter i Azure Active Directory.
1. Välj autentiseringsuppgifter för verifierbar där du tidigare har konfigurerat index anspråk och utfärdat en verifierbar autentiseringsuppgift till en användare. =
1. På den vänstra menyn väljer du **återkalla en autentiseringsuppgift** 
    ![ återkalla autentiseringsuppgifter](media/how-to-issuer-revoke/settings-revoke.png) 
1. Sök efter attributet index för den användare som du vill återkalla. 

   ![Hitta de autentiseringsuppgifter som ska återkallas](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Eftersom vi bara lagrar en hash för det indexerade anspråket från den verifierbara autentiseringsuppgiften, fylls bara Sök resultaten i i en exakt matchning. Vi tar de indata som söks igenom av IT-administratören och vi använder samma hash-algoritm för att se om vi har en hash-matchning i vår databas.
    
1. När du har hittat en matchning väljer du alternativet **återkalla** till höger om den autentiseringsuppgift som du vill återkalla.

   ![En varning som låter dig veta att efter återkallande har användaren fortfarande behörigheten](media/how-to-issuer-revoke/warning.png) 

1. Efter en lyckad åter kallelse ser du status uppdateringen och en grön banderoll visas överst på sidan. 
   ![Verifiera den här domänen i inställningar](media/how-to-issuer-revoke/revoke-successful.png) 

Nu när en förlitande parts anrop för att kontrol lera status för den här typen av verifierbara autentiseringsuppgifter, returnerar Microsofts status-API, som agerar på uppdrag av klienten, ett "falskt"-svar.

## <a name="next-steps"></a>Nästa steg

Testa funktionen på egen hand med en testautentiseringsuppgift som används för flödet. Du kan se information om hur du konfigurerar klienten så att den utfärdar verifierbara autentiseringsuppgifter genom att [granska våra självstudier](get-started-verifiable-credentials.md).