---
title: Länka din domän till ditt decentraliserade ID (förhands granskning)
description: Lär du dig hur du gör DNS-bindning?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: be7db16a8e3a827d08c0db637961bf004af1d621
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170240"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Länka din domän till din decentraliserade identifierare (DID)

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln:
> [!div class="checklist"]
> * Varför behöver vi länka oss till vår domän?
> * Hur länkar vi DIDs och domäner?
> * Hur fungerar domän länknings processen?
> * Hur fungerar verifiera/overifierad domän logik?

## <a name="prerequisites"></a>Förutsättningar

För att länka till din domän måste du ha slutfört följande.

- Slutför [komma igång](get-started-verifiable-credentials.md) och efterföljande [själv studie kurs uppsättning](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Varför behöver vi länka oss till vår domän?

En påbörjades som en identifierare som inte är fäst vid befintliga system. Det var användbart eftersom en användare eller organisation kan äga den och kontrol lera den. Om en entitet som interagerar med organisationen inte vet att "vem" tillhör, är det inte så användbart.

Att länka en till en domän löser det första förtroende problemet genom att tillåta att alla entiteter kryptografiskt kan verifiera relationen mellan en och en domän.

## <a name="how-do-we-link-dids-and-domains"></a>Hur länkar vi DIDs och domäner?

Vi skapar en länk mellan en domän och en genomförd genom att implementera en öppen standard skriven av den decentraliserade Identity Foundation som kallas [välkänd konfiguration](https://identity.foundation/.well-known/resources/did-configuration/). Tjänsten verifierbara autentiseringsuppgifter i Azure Active Directory (Azure AD) hjälper din organisation att skapa länken mellan domänen och domänen genom att inkludera den domän information som du angav i den gjorde du och generera en välkänd konfigurations fil:

1. Azure AD använder den domän information som du anger under organisations installationen för att skriva en tjänst slut punkt i dokumentet. Alla parter som interagerar med kan se den domän som du gjorde anspråk på för att associeras med.  

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

2. Tjänsten verifierbar behörighet i Azure AD genererar en kompatibel och känd konfigurations resurs som du kan använda som värd för din domän. Konfigurations filen innehåller en självutfärdad verifierbar autentiseringsuppgift för credentialType ' DomainLinkageCredential ' som är signerad med den som har sitt ursprung i din domän. Här är ett exempel på det config-dokument som lagras i rot domänens URL.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

När du har en välkänd konfigurations fil måste du göra filen tillgänglig med det domän namn som du angav när du aktiverade AAD för verifierbara autentiseringsuppgifter.

* VAR värd för den välkända konfigurations filen i domänens rot.
* Använd inte omdirigeringar.
* Använd https för att distribuera konfigurations filen.

>[!IMPORTANT]
>Microsoft Authenticator inte följer omdirigeringar, den angivna URL: en måste vara den slutgiltiga mål-URL: en.

## <a name="user-experience"></a>Användarupplevelse 

När en användare går igenom ett utfärdande flöde eller visar en verifierbar autentiseringsuppgift, bör de känna till något om organisationen och dess gjort. Om domänens plån boks referens för verifierbar, Microsoft Authenticator, validerar en relation med domänen i dokumentet i dokumentet och visar användare två olika upplevelser beroende på resultatet.

## <a name="verified-domain"></a>Verifierad domän

Innan Microsoft Authenticator visar en **verifierad** ikon måste några saker vara sanna:

* Den signerade SIOP-begäran (Open ID) måste ha en tjänst slut punkt för den länkade domänen.
* Rot domänen använder inte en omdirigering och använder https.
* Domänen som anges i dokumentet har en välkänd känd resurs som kan matchas.
* Den välkända resursens verifierbara autentiseringsuppgifter är signerad med samma som användes för att signera SIOP som Microsoft Authenticator använt för att koppla start flödet.

Om alla tidigare angivna villkor är uppfyllda visar Microsoft Authenticator en verifierad sida och inkluderar den domän som har verifierats.

![ny behörighets förfrågan](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Overifierad domän

Om något av ovanstående inte är sant, visar Microsoft Authenticator en fullständig sid varning för användaren att domänen är overifierad, användaren är i mitten av en riskfylld transaktion och de bör fortsätta med försiktighet. Vi har valt att ta den här vägen eftersom:

* Den har antingen inte fästs på en domän.
* Konfigurationen har inte ställts in korrekt.
* Användaren interagerar med är skadlig och kan inte bevisa att de äger en domän (eftersom de faktiskt inte är det). På grund av den här sista punkten är det absolut nödvändigt att du länkar till den domän som användaren är van vid, för att undvika att sprida varnings meddelandet.

![overifierad domän varning på skärmen Lägg till autentiseringsuppgifter](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Distribuera välkända config

1. Gå till sidan Inställningar i autentiseringsuppgifter för verifierbar och välj **verifiera den här domänen**

   ![Verifiera den här domänen i inställningar](media/how-to-dnsbind/settings-verify.png) 

2. Ladda ned did-configuration.jspå filen som visas på bilden nedan.

   ![Hämta välkänd välkänd konfiguration](media/how-to-dnsbind/verify-download.png) 

3. Kopiera JWT, öppna [JWT.MS](https://www.jwt.ms) och verifiera att domänen är korrekt.

4. Kopiera din DID-filen och öppna [Jon nätverks Utforskaren](https://identity.foundation/ion/explorer) för att kontrol lera att samma domän ingår i dokumentet. 

5. Var värd för den välkända konfigurations resursen på den angivna platsen. Exempel: https://www.example.com/.well-known/did-configuration.json

6. Testa utfärdande eller presentation med Microsoft Authenticator för att verifiera. Kontrol lera att inställningen i autentiseraren ' varna om osäkra appar ' är växlad på.

>[!NOTE]
>Som standard är "varna om osäkra appar" aktiverat.

Grattis, nu har du startat förtroendet med din!

## <a name="next-steps"></a>Nästa steg

Om du anger fel domän information som du väljer att ändra i när du registrerar den, måste du [avanmäla](how-to-opt-out.md)dig. För tillfället stöder vi inte uppdatering av dokumentet. Väljer ut och väljer tillbaka i kommer att skapa ett helt nytt.