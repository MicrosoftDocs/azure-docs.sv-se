---
title: Länka din domän till din decentraliserade identifierare (DID) (förhandsversion) – Azure Active Directory autentiseringsuppgifter
description: Lär dig hur du dns-bindning?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588453"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Länka din domän till din decentraliserade identifierare (DID)

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln:
> [!div class="checklist"]
> * Varför behöver vi länka vår DID till vår domän?
> * Hur länkar vi DID:er och domäner?
> * Hur fungerar domänlänkningsprocessen?
> * Hur fungerar verifierad/overifierad domänlogik?

## <a name="prerequisites"></a>Förutsättningar

Om du vill länka DID till din domän måste du ha slutfört följande.

- Slutför Komma igång [och](get-started-verifiable-credentials.md) efterföljande [självstudie ange](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Varför behöver vi länka vår DID till vår domän?

DID börjar som en identifierare som inte är fäst vid befintliga system. En DID är användbar eftersom en användare eller organisation kan äga den och kontrollera den. Om en entitet som interagerar med organisationen inte vet "vem" SOM DID tillhör är DID inte lika användbart.

Länkning av en DID till en domän löser det första förtroendeproblemet genom att tillåta en entitet att kryptografiskt verifiera relationen mellan en DID och en domän.

## <a name="how-do-we-link-dids-and-domains"></a>Hur länkar vi DID:er och domäner?

Vi gör en länk mellan en domän och en DID genom att implementera en öppen standard som skrivits av den decentraliserade Identity Foundation [som kallas well-known DID-konfiguration.](https://identity.foundation/.well-known/resources/did-configuration/) Tjänsten för verifierbara autentiseringsuppgifter i Azure Active Directory (Azure AD) hjälper din organisation att skapa länken mellan DID och domänen genom att inkludera den domäninformation som du angav i DID och generera den välkända konfigurationsfilen:

1. Azure AD använder den domäninformation som du anger under organisationskonfigurationen för att skriva en tjänstslutpunkt i DID-dokumentet. Alla parter som interagerar med DID kan se domänen som dina DID-användare har associerats med.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Den verifierbara autentiseringstjänsten i Azure AD genererar en kompatibel välkänd konfigurationsresurs som du kan vara värd för i din domän. Konfigurationsfilen innehåller en självutfärdad verifierbar autentiseringsfil för credentialType 'DomainLinkageCredential' som signerats med din DID som har domänens ursprung. Här är ett exempel på konfigurationsdokument som lagras på rotdomänens URL.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

När du har den välkända konfigurationsfilen måste du göra filen tillgänglig med det domännamn som du angav när du aktiverar AAD för verifierbara autentiseringsuppgifter.

* Var värd för den välkända DID-konfigurationsfilen i roten av domänen.
* Använd inte omdirigeringar.
* Använd https för att distribuera konfigurationsfilen.

>[!IMPORTANT]
>Microsoft Authenticator inte respekterar omdirigeringar måste den angivna URL:en vara den slutliga mål-URL:en.

## <a name="user-experience"></a>Användarupplevelse 

När en användare går igenom ett utfärdandeflöde eller visar en verifierbar autentiseringsidentifiering bör de känna till något om organisationen och dess DID. Om domänen vår verifierbara plånbok för autentiseringsuppgifter Microsoft Authenticator verifierar en DID-relation med domänen i DID-dokumentet och ger användarna två olika upplevelser beroende på resultatet.

## <a name="verified-domain"></a>Verifierad domän

Innan Microsoft Authenticator visar **en verifierad** ikon måste några saker vara sanna:

* DID-signeringen av den självutgivna öppna ID-begäran (SIOP) måste ha en tjänstslutpunkt för länkad domän.
* Rotdomänen använder inte någon omdirigering och använder https.
* Domänen som anges i DID-dokumentet har en välkänd resurs som kan matchas.
* Den välkända resursens verifierbara autentiseringsuppgifter signeras med samma DID som användes för att signera DEN SIOP som Microsoft Authenticator för att starta flödet.

Om alla tidigare nämnda är sanna visar Microsoft Authenticator en verifierad sida och innehåller domänen som verifierades.

![ny behörighetsbegäran](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Overifierad domän

Om något av ovanstående inte är sant visar Microsoft Authenticator en helsidesvarning för användaren om att domänen är overifierad, att användaren är mitt i en riskabel transaktion och bör fortsätta med försiktighet. Vi har valt att ta den här vägen eftersom:

* DID är antingen inte fäst vid en domän.
* Konfigurationen har inte ställts in korrekt.
* DID användaren interagerar med är skadlig och kan faktiskt inte bevisa att de äger en domän (eftersom de faktiskt inte gör det). På grund av den sista punkten är det viktigt att du länkar DID till domänen som användaren är bekant med för att undvika att varningsmeddelandet sprids.

![varning om overifierad domän på skärmen lägg till autentiseringsuppgifter](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Distribuera välkänd konfiguration

1. Gå till sidan Inställningar i Verifieable Credentials (Verifiera autentiseringsuppgifter) och välj **Verify this domain (Verifiera den här domänen)**

   ![Verifiera den här domänen i inställningarna](media/how-to-dnsbind/settings-verify.png) 

2. Ladda ned did-configuration.jspå filen som visas i bilden nedan.

   ![Ladda ned välkänd konfiguration](media/how-to-dnsbind/verify-download.png) 

3. Kopiera JWT, öppna [jwt.ms](https://www.jwt.ms) och verifiera att domänen är korrekt.

4. Kopiera DID och öppna [ION-Nätverksutforskaren](https://identity.foundation/ion/explorer) för att verifiera att samma domän ingår i DID-dokumentet. 

5. Var värd för den välkända konfigurationsresursen på den angivna platsen. Exempel: `https://www.example.com/.well-known/did-configuration.json`

6. Testa att utfärda eller presentera med Microsoft Authenticator för att verifiera. Kontrollera att inställningen i Authenticator "Varna om osäkra appar" är på.

>[!NOTE]
>Som standard är "Varna om osäkra appar" aktiverat.

Grattis, nu har du bootstrappat förtroendewebb med did!

## <a name="next-steps"></a>Nästa steg

Om du under onboarding-registrering anger fel domäninformation för du bestämmer dig för att ändra den, måste du [välja bort](how-to-opt-out.md). För stunden stöder vi inte uppdatering av did-dokumentet. Om du väljer bort och anmäler dig igen skapas en helt ny DID.
