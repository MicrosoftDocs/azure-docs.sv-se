---
title: Om Azure Key Vault certifikat – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnitt och certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: b410dc89b286ef830f0d5b6a9c33fe77d380f5d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507219"
---
# <a name="about-azure-key-vault-certificates"></a>Om Azure Key Vault-certifikat

Stöd för Key Vault certifikat ger till gång till hantering av x509-certifikat och följande beteenden:  

-   Tillåter en certifikat ägare att skapa ett certifikat via en Key Vault skapande process eller genom att importera ett befintligt certifikat. Innehåller både självsignerade och certifikat utfärdare som genererade certifikat.
-   Tillåter en Key Vault certifikat ägare att implementera säker lagring och hantering av X509-certifikat utan interaktion med privat nyckel material.  
-   Tillåter en certifikat ägare att skapa en princip som dirigerar Key Vault för att hantera livs cykeln för ett certifikat.  
-   Tillåter certifikat ägare att tillhandahålla kontakt information för meddelanden om livs cykel händelser för förfallo datum och förnyelse av certifikat.  
-   Har stöd för automatisk förnyelse med valda utfärdare – Key Vault partner X509 certifikat leverantörer/certifikat utfärdare.

>[!Note]
>Leverantörer/myndigheter som inte är partner tillåtna är också tillåtna, men stöder inte funktionen för automatisk förnyelse.

## <a name="composition-of-a-certificate"></a>Sammansättning av ett certifikat

När ett Key Vault certifikat skapas, skapas även en adresserad nyckel och hemlighet med samma namn. Key Vault-nyckeln tillåter nyckel åtgärder och Key Vault hemligheten kan hämta certifikatets värde som en hemlighet. Ett Key Vault certifikat innehåller även metadata för offentliga x509-certifikat.  

Identifieraren och versionen av certifikat liknar nycklarna och hemligheterna. En speciell version av en adresserad nyckel och hemlighet som skapats med Key Vault certifikat version är tillgänglig i Key Vault certifikatets svar.
 
![Certifikat är komplexa objekt](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Export bar eller icke-export bar nyckel

När ett Key Vault-certifikat skapas, kan det hämtas från den adresser bara hemligheten med den privata nyckeln i antingen PFX-eller PEM-format. Principen som används för att skapa certifikatet måste ange att nyckeln kan exporteras. Om principen anger att det inte går att exportera, är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.  

Den adresser bara nyckeln blir mer relevant med icke exporter bara KV-certifikat. Den adresser bara KV-nyckelns åtgärder mappas från fältet nyckel *användning* i kv-certifikat policyn som används för att skapa kv-certifikatet.  

Typ av nyckel par som stöds för certifikat

 - Typer som stöds: RSA, RSA-HSM, EC, EC-HSM, okt (listade [här](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) kan exporteras endast med RSA, ec. HSM-nycklar kan inte exporteras.

|Nyckeltyp|Om|Säkerhet|
|--|--|--|
|**RSA**| "RSA-nyckel för" Software-Protected "|FIPS 140-2-nivå 1|
|**RSA-HSM**| "HSM-skyddad" RSA-nyckel (endast Premium-SKU)|FIPS 140-2 nivå 2 HSM|
|**EC**| "Elliptic Curve-nyckel för" Software-Protected "|FIPS 140-2-nivå 1|
|**EC-HSM**| "HSM-skyddad" Elliptic kurva-nyckel (endast Premium-SKU)|FIPS 140-2 nivå 2 HSM|
|||

## <a name="certificate-attributes-and-tags"></a>Certifikatets attribut och Taggar

Förutom certifikatets metadata, en adresserad nyckel och adresserbar hemlighet, innehåller ett Key Vault certifikat också attribut och taggar.  

### <a name="attributes"></a>Attribut

Certifikatets attribut speglas till attribut för den adress bara nyckel och hemlighet som skapas när KV-certifikatet skapas.  

Ett Key Vault certifikat har följande attribut:  

-   *aktive rad*: boolesk, valfritt, standardvärdet är **True**. Kan anges för att ange om certifikat data kan hämtas som hemliga eller fungerar som en nyckel. Används också tillsammans med *NBF* och *exp* när en åtgärd sker mellan *NBF* och *exp*, och är bara tillåten om aktive rad är inställd på True. Åtgärder utanför *NBF* och *exp* -fönstret tillåts inte automatiskt.  

Det finns ytterligare skrivskyddade attribut som ingår i svaret:

-   *skapad*: IntDate: anger när den här versionen av certifikatet skapades.  
-   *uppdaterad*: IntDate: anger när den här versionen av certifikatet uppdaterades.  
-   *exp*: IntDate: innehåller värdet för det utgångna datumet för x509-certifikatet.  
-   *NBF*: IntDate: innehåller värdet för datumet för x509-certifikatet.  

> [!Note] 
> Om ett nyckelvalvscertifikat upphör att gälla går det inte att använda dess adresserbara nyckel och hemlighet.  

### <a name="tags"></a>Taggar

 Klientens angivna ord lista med nyckel värdes par, liknar Taggar i nycklar och hemligheter.  

 > [!Note]
> Taggarna kan läsas av en anropare om de har *listan* eller *får* behörighet till den objekt typen (nycklar, hemligheter eller certifikat).

## <a name="certificate-policy"></a>Certifikat princip

En certifikat princip innehåller information om hur du skapar och hanterar livs cykeln för ett Key Vault certifikat. När ett certifikat med privat nyckel importeras till nyckel valvet skapas en standard princip genom att läsa in x509-certifikatet.  

När ett Key Vault-certifikat skapas från grunden måste en princip anges. Principen anger hur du skapar den här Key Vault certifikat versionen eller nästa Key Vault certifikat version. När en princip har upprättats krävs det inte med efterföljande skapande åtgärder för framtida versioner. Det finns bara en instans av en princip för alla versioner av ett Key Vault certifikat.  

På en hög nivå innehåller en certifikat princip följande information (deras definitioner hittar du [här](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy)):  

-   Egenskaper för X509-certifikat: innehåller ämnes namn, alternativa namn för certifikat mottagare och andra egenskaper som används för att skapa en x509-certifikatbegäran.  
-   Nyckel egenskaper: innehåller fält av nyckel typ, nyckel längd, export bar och ReuseKeyOnRenewal. Dessa fält instruerar nyckel valvet om hur man genererar en nyckel. 
     - Typer som stöds: RSA, RSA-HSM, EC, EC-HSM, okt (visas [här](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) 
-   Hemliga egenskaper: innehåller hemliga egenskaper som innehålls typ för adresser bar hemlighet för att generera det hemliga värdet, för att hämta certifikat som en hemlighet.  
-   Livs längds åtgärder: innehåller livs längds åtgärder för KV-certifikatet. Varje livs längds åtgärd innehåller:  

     - Utlösare: anges via dagar före förfallo datum eller livs längds intervall i procent  

     - Åtgärd: Ange åtgärds typ – *emailContacts* eller *autoförnyelse*  

-   Utfärdare: parametrar om den certifikat utfärdare som ska användas för att utfärda x509-certifikat.  
-   Princip-attribut: innehåller attribut som är associerade med principen  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 för att Key Vault användnings mappning

Följande tabell visar mappningen av principen för x509-nyckel användning till effektiva nyckel åtgärder i en nyckel som skapats som en del av ett Key Vault-certifikat skapas.

|**Användnings flaggor för X509-nyckel**|**Key Vault Key OPS**|**Standardbeteende**|
|----------|--------|--------|
|DataEncipherment|kryptera, dekryptera| Ej tillämpligt |
|DecipherOnly|innehållet| Ej tillämpligt  |
|DigitalSignature|signera, verifiera| Key Vault standard utan användnings specifikation när certifikat skapas | 
|EncipherOnly|encrypt| Ej tillämpligt |
|KeyCertSign|signera, verifiera|Ej tillämpligt|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault standard utan användnings specifikation när certifikat skapas | 
|Oavvislig het|signera, verifiera| Ej tillämpligt |
|cRLSign|signera, verifiera| Ej tillämpligt |

## <a name="certificate-issuer"></a>Certifikat utfärdare

Ett Key Vault certifikat objekt innehåller en konfiguration som används för att kommunicera med en vald certifikat utfärdare-Provider för att beställa x509-certifikat.  

-   Key Vault partner med följande providers för certifikat utfärdare för TLS/SSL-certifikat

|**Leverantörens namn**|**Platser**|
|----------|--------|
|DigiCert|Stöds i alla Key Vault service-platser i det offentliga molnet och Azure Government|
|GlobalSign|Stöds i alla Key Vault service-platser i det offentliga molnet och Azure Government|

Innan du kan skapa en certifikat utfärdare i en Key Vault måste du utföra följande steg 1 och 2 för att kunna utföra åtgärder.  

1. Publicera till certifikat utfärdare (CA)-leverantörer  

    -   En organisations administratör måste ombord på företaget (t. ex. Contoso) med minst en CA-Provider.  

2. Admin skapar beställarens autentiseringsuppgifter för Key Vault att registrera (och förnya) TLS/SSL-certifikat  

    -   Tillhandahåller den konfiguration som ska användas för att skapa ett Issuer-objekt för providern i nyckel valvet  

Mer information om hur du skapar Issuer-objekt från certifikat portalen finns i [bloggen för Key Vault certifikat](/archive/blogs/kv/manage-certificates-via-azure-key-vault)  

Key Vault gör det möjligt att skapa flera Issuer-objekt med en annan konfiguration av Issuer-providern. När ett Issuer-objekt har skapats kan namnet refereras till i en eller flera certifikat principer. Om du refererar till Issuer-objektet instrueras Key Vault att använda konfigurationen som anges i Issuer-objektet när du begär x509-certifikatet från CA-providern när certifikatet skapas och förnyas.  

Issuer-objekt skapas i valvet och kan bara användas med KV-certifikat i samma valv.  

## <a name="certificate-contacts"></a>Certifikat kontakter

Certifikat kontakter innehåller kontakt information för att skicka meddelanden som utlöses av händelser för certifikat livs längd. Kontakt information delas av alla certifikat i nyckel valvet. Ett meddelande skickas till alla angivna kontakter för en händelse för ett certifikat i nyckel valvet. Information om hur du anger certifikat kontakt, finns [här](overview-renew-certificate.md#steps-to-set-certificate-notifications)  

## <a name="certificate-access-control"></a>Certifikat Access Control

 Åtkomstkontroll för certifikat hanteras av Key Vault och tillhandahålls av Key Vault som innehåller dessa certifikat. Åtkomst kontroll principen för certifikat skiljer sig från principerna för åtkomst kontroll för nycklar och hemligheter i samma Key Vault. Användare kan skapa ett eller flera valv för att lagra certifikat, för att upprätthålla lämplig segmentering och hantering av certifikat.  Mer information om åtkomst kontroll för certifikat finns [här](certificate-access-control.md)

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om nycklar](../keys/about-keys.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
