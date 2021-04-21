---
title: Om Azure Key Vault certifikat – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnitt och certifikat.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: fb69068ddac311a8020a76eec9b18fab3256fea6
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752542"
---
# <a name="about-azure-key-vault-certificates"></a>Om Azure Key Vault-certifikat

Key Vault certifikat stöder hantering av x509-certifikat och följande beteenden:  

-   Gör att en certifikatägare kan skapa ett certifikat Key Vault en process för att skapa ett certifikat eller genom import av ett befintligt certifikat. Innehåller både själv signerade certifikat och certifikatutfärdare genererade certifikat.
-   Gör att Key Vault kan implementera säker lagring och hantering av X509-certifikat utan interaktion med privat nyckelmaterial.  
-   Gör att en certifikatägare kan skapa en princip som Key Vault att hantera livscykeln för ett certifikat.  
-   Gör att certifikatägare kan ange kontaktinformation för meddelanden om livscykelhändelser när certifikatet upphör att gälla och förnyas.  
-   Stöder automatisk förnyelse med valda utfärdare – Key Vault X509-certifikatproviders/certifikatutfärdare.

>[!Note]
>Leverantörer/myndigheter som inte är partner är också tillåtna, men kommer inte att stödja funktionen för automatisk förnyelse.

## <a name="composition-of-a-certificate"></a>Sammansättningen av ett certifikat

När ett Key Vault-certifikat skapas skapas även en adresserbar nyckel och hemlighet med samma namn. Nyckelnyckeln Key Vault nyckelåtgärder och den Key Vault hemligheten gör det möjligt att hämta certifikatvärdet som en hemlighet. Ett Key Vault certifikat innehåller också offentliga x509-certifikatmetadata.  

Identifieraren och versionen för certifikat liknar den för nycklar och hemligheter. En specifik version av en adresserbar nyckel och hemlighet som skapats med Key Vault-certifikatversionen finns i Key Vault certifikatsvaret.
 
![Certifikat är komplexa objekt](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exporterbar eller icke exporterbar nyckel

När ett Key Vault certifikat skapas kan det hämtas från den adresserbara hemligheten med den privata nyckeln i PFX- eller PEM-format. Principen som används för att skapa certifikatet måste ange att nyckeln kan exporteras. Om principen indikerar att den inte kan exporteras är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.  

Den adresserbara nyckeln blir mer relevant med icke exporterbara KV-certifikat. Den adresserbara KV-nyckelns åtgärder  mappas från nyckelanvändarfältet i den KV-certifikatprincip som används för att skapa KV-certifikatet.  

Typ av nyckelpar som stöds för certifikat

 - Nyckeltyper som stöds: RSA, RSA-HSM, EC, EC-HSM, okt (anges [här)](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)Exporterbar tillåts endast med RSA, EC. HSM-nycklar skulle inte kunna exporteras.

|Nyckeltyp|Om|Säkerhet|
|--|--|--|
|**RSA**| RSA-nyckel "Programvaruskyddad"|FIPS 140-2 Nivå 1|
|**RSA-HSM**| "HSM-skyddad" RSA-nyckel (endast Premium SKU)|FIPS 140-2 Level 2 HSM|
|**EC**| "Programvaruskyddad" Elliptic Curve-nyckel|FIPS 140-2 Nivå 1|
|**EC-HSM**| "HSM-skyddad" Elliptic Curve-nyckel (endast Premium SKU)|FIPS 140-2 Level 2 HSM|
|||

## <a name="certificate-attributes-and-tags"></a>Certifikatattribut och taggar

Förutom certifikatmetadata, en adresserbar nyckel och adresserbar hemlighet, innehåller Key Vault certifikat även attribut och taggar.  

### <a name="attributes"></a>Attribut

Certifikatattributen speglas till attributen för den adresserbara nyckeln och hemligheten som skapades när KV-certifikatet skapades.  

Ett Key Vault-certifikat har följande attribut:  

-   *enabled*: boolean, optional, default is **true**. Kan anges för att ange om certifikatdata kan hämtas som hemliga eller kan användas som en nyckel. Används även tillsammans med *nbf* och *exp* när en åtgärd inträffar mellan *nbf* och *exp* och tillåts endast om aktiverad är inställd på sant. Åtgärder utanför *nbf-* och *exp-fönstret* tillåts inte automatiskt.  

Det finns ytterligare skrivskyddade attribut som ingår i svaret:

-   *created*: IntDate: anger när den här versionen av certifikatet skapades.  
-   *updated*: IntDate: anger när den här versionen av certifikatet uppdaterades.  
-   *exp:* IntDate: innehåller värdet för x509-certifikatets förfallodatum.  
-   *nbf:* IntDate: innehåller värdet för x509-certifikatets datum.  

> [!Note] 
> Om ett nyckelvalvscertifikat upphör att gälla går det inte att använda dess adresserbara nyckel och hemlighet.  

### <a name="tags"></a>Taggar

 Klient angiven ordlista med nyckelvärdepar, liknande taggar i nycklar och hemligheter.  

 > [!Note]
> Taggar kan läsas av en anropare  om de har listan eller får behörighet till den objekttypen (nycklar, hemligheter eller certifikat). 

## <a name="certificate-policy"></a>Certifikatprincip

En certifikatprincip innehåller information om hur du skapar och hanterar livscykeln för ett Key Vault certifikat. När ett certifikat med privat nyckel importeras till nyckelvalvet skapas en standardprincip genom att läsa x509-certifikatet.  

När ett Key Vault-certifikat skapas från grunden måste en princip anges. Principen anger hur du skapar den här Key Vault certifikatversionen eller nästa Key Vault certifikatversion. När en princip har upprättats krävs den inte med efterföljande åtgärder för att skapa för framtida versioner. Det finns bara en instans av en princip för alla versioner av ett Key Vault certifikat.  

På en hög nivå innehåller en certifikatprincip följande information (deras definitioner finns [här](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy)):  

-   X509-certifikategenskaper: Innehåller ämnesnamn, alternativa ämnesnamn och andra egenskaper som används för att skapa en x509-certifikatbegäran.  
-   Nyckelegenskaper: innehåller fälten nyckeltyp, nyckellängd, exporterbar och ReuseKeyOnRenewal. Dessa fält instruerar nyckelvalvet om hur du genererar en nyckel. 
     - Nyckeltyper som stöds: RSA, RSA-HSM, EC, EC-HSM, okt (anges [här)](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) 
-   Hemliga egenskaper: innehåller hemliga egenskaper som innehållstypen adresserbar hemlighet för att generera det hemliga värdet, för att hämta certifikat som en hemlighet.  
-   Livslängdsåtgärder: Innehåller livslängdsåtgärder för KV-certifikatet. Varje livslängdsåtgärd innehåller:  

     - Utlösare: anges via dagar före upphörande eller livslängdsintervall i procent  

     - Åtgärd: ange åtgärdstyp *– emailContacts* *eller autoRenew*  

-   Utfärdare: Parametrar om certifikatutfärdaren som ska användas för att utfärda x509-certifikat.  
-   Principattribut: innehåller attribut som är associerade med principen  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 för att Key Vault användningsmappning

Följande tabell representerar mappningen av x509-nyckelanvändningsprincipen till effektiva nyckelåtgärder för en nyckel som skapats som en del av en Key Vault ett certifikat.

|**Flaggor för X509-nyckelanvändning**|**Key Vault nyckel ops**|**Standardbeteende**|
|----------|--------|--------|
|DataEncipherment|kryptera, dekryptera| Ej tillämpligt |
|DecipherOnly|Dekryptera| Ej tillämpligt  |
|DigitalSignature|signera, verifiera| Key Vault standard utan användningsspecifikation när certifikatet skapas | 
|EncipherOnly|encrypt| Ej tillämpligt |
|KeyCertSign|signera, verifiera|Ej tillämpligt|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault standard utan användningsspecifikation när certifikatet skapas | 
|Oavvislighet|signera, verifiera| Ej tillämpligt |
|crlsign|signera, verifiera| Ej tillämpligt |

## <a name="certificate-issuer"></a>Certifikatutfärdare

Ett Key Vault innehåller en konfiguration som används för att kommunicera med en vald certifikatutfärdarprovider för att beställa x509-certifikat.  

-   Key Vault partner med följande certifikatutfärdarproviders för TLS-/SSL-certifikat

|**Leverantörens namn**|**Platser**|
|----------|--------|
|DigiCert|Stöds på alla nyckelvalvstjänstplatser i offentliga moln och Azure Government|
|GlobalSign|Stöds på alla nyckelvalvstjänstplatser i offentliga moln och Azure Government|

Innan en certifikatutfärdare kan skapas i en Key Vault måste följande steg 1 och 2 slutföras.  

1. Publicera till certifikatutfärdare (CA)-providers  

    -   En organisationsadministratör måste gå med i företaget (t.ex. Contoso) med minst en CA-provider.  

2. Administratören skapar autentiseringsuppgifter för begärande Key Vault att registrera (och förnya) TLS/SSL-certifikat  

    -   Tillhandahåller konfigurationen som ska användas för att skapa ett utfärdarobjekt för providern i nyckelvalvet  

Mer information om hur du skapar utfärdarobjekt från certifikatportalen finns på [bloggen Key Vault certifikat](/archive/blogs/kv/manage-certificates-via-azure-key-vault)  

Key Vault kan du skapa flera utfärdarobjekt med olika utfärdarproviderkonfigurationer. När ett utfärdarobjekt har skapats kan dess namn refereras till i en eller flera certifikatprinciper. Referens av objektet issuer instruerar Key Vault att använda konfigurationen som anges i utfärdarobjektet när du begär x509-certifikatet från CA-providern när certifikatet skapas och förnyas.  

Utfärdarobjekt skapas i valvet och kan bara användas med KV-certifikat i samma valv.  

## <a name="certificate-contacts"></a>Certifikatkontakter

Certifikatkontakter innehåller kontaktinformation för att skicka meddelanden som utlöses av certifikatlivshändelser. Kontaktinformationen delas av alla certifikat i nyckelvalvet. Ett meddelande skickas till alla angivna kontakter för en händelse för alla certifikat i nyckelvalvet. Information om hur du ställer in certifikatkontakt finns [här](overview-renew-certificate.md#steps-to-set-certificate-notifications)  

## <a name="certificate-access-control"></a>Certifikat Access Control

 Åtkomstkontroll för certifikat hanteras av Key Vault och tillhandahålls av Key Vault som innehåller dessa certifikat. Principen för åtkomstkontroll för certifikat skiljer sig från åtkomstkontrollprinciperna för nycklar och hemligheter i samma Key Vault. Användare kan skapa ett eller flera valv för att innehålla certifikat för att upprätthålla lämplig segmentering och hantering av certifikat i scenariot.  Mer information om åtkomstkontroll för certifikat finns [här](certificate-access-control.md)

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om nycklar](../keys/about-keys.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
