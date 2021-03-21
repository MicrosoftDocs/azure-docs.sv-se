---
title: Översikt över Azure Key Vault nycklar, hemligheter och certifikat
description: Översikt över Azure Key Vault REST-gränssnittet och information om utvecklare för nycklar, hemligheter och certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: f2a33b4011b0bb89ca17010fc787aebe8c17fc92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183938"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Översikt över Azure Key Vault nycklar, hemligheter och certifikat

Azure Key Vault gör det möjligt för Microsoft Azure program och användare att lagra och använda flera typer av hemliga/viktiga data. Key Vault Resource provider stöder två resurs typer: valv och hanterade HSM: er.

## <a name="dns-suffixes-for-base-url"></a>DNS-suffix för bas-URL
 Tabellen nedan visar det grundläggande URL-suffixet som används av data Plans slut punkten för valv och hanterade HSM-pooler i olika moln miljöer.

Moln miljö | DNS-suffix för valv | DNS-suffix för hanterade HSM: er
---|---|---
Azure-moln | . vault.azure.net | . managedhsm.azure.net
Azure Kina-moln | . vault.azure.cn | Stöds inte
Azure US Government | . vault.usgovcloudapi.net | Stöds inte
Azure tyskt-moln | . vault.microsoftazure.de | Stöds inte
|||


## <a name="object-types"></a>Objekt typer
 Tabellen nedan visar objekt typer och deras suffix i bas-URL: en.

Objekttyp|URL-suffix|Valv|Hanterade HSM-pooler
--|--|--|--
**Kryptografiska nycklar**||
HSM-skyddade nycklar|/keys|Stöds|Stöds
Program vara – skyddade nycklar|/keys|Stöds|Stöds inte
**Andra objekt typer**||
Hemligheter|/secrets|Stöds|Stöds inte
Certifikat|/certificates|Stöds|Stöds inte
Lagringskontonycklar|/storage|Stöds|Stöds inte
|||
- **Kryptografiska nycklar**: stöder flera nyckel typer och algoritmer och möjliggör användning av program skyddade och HSM-skyddade nycklar. Mer information finns i [om nycklar](../keys/about-keys.md).
- **Hemligheter**: ger säker lagring av hemligheter, t. ex. lösen ord och databas anslutnings strängar. Mer information finns i [om hemligheter](../secrets/about-secrets.md).
- **Certifikat**: stöder certifikat, som bygger på nycklar och hemligheter och lägger till en automatiserad förnyelse funktion. Mer information finns i [om certifikat](../certificates/about-certificates.md).
- **Azure Storage konto nycklar**: kan hantera nycklar för ett Azure Storage konto åt dig. Internt Key Vault kan ange (Sync) nycklar med ett Azure Storage konto och återskapa (rotera) nycklarna med jämna mellanrum. Mer information finns i [Hantera lagrings konto nycklar med Key Vault](../secrets/overview-storage-keys.md).

Mer allmän information om Key Vault finns i [om Azure Key Vault](overview.md). Mer information om hanterade HSM-pooler finns i vad är [Azure Key Vault hanterad HSM?](../managed-hsm/overview.md)


## <a name="data-types"></a>Datatyper

Se JOSE-specifikationerna för relevanta data typer för nycklar, kryptering och signering.  

-   **algoritm** – en algoritm som stöds för en nyckel åtgärd, till exempel RSA1_5  
-   **chiffertexten-värde** -cipher text-oktetter, kodade med Base64URL  
-   **sammandrag-värde** – utdata från en hash-algoritm kodas med hjälp av Base64URL  
-   **nyckel typ** – en av de nyckel typer som stöds, till exempel RSA (Rivest-Shamir-Adleman).  
-   **klartext – värde** – oformaterade oktetter, kodade med Base64URL  
-   **Signature-värde** – utdata från en Signeringsalgoritm som är kodad med Base64URL  
-   **base64URL** – a BASE64URL [RFC4648] kodat binärt värde  
-   **boolesk** -antingen sant eller falskt  
-   **Identitet** – en identitet från Azure Active Directory (AAD).  
-   **IntDate** – ett JSON-standardvärde som representerar antalet sekunder från 1970-01-01T0:0: 0Z UTC tills angivet UTC-datum/tid. Se RFC3339 för information om datum/tider, i allmänhet och UTC i synnerhet.  

## <a name="objects-identifiers-and-versioning"></a>Objekt, identifierare och versions hantering

Objekt som lagras i Key Vault versioner skapas när en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL. När ett objekt skapas första gången får den ett unikt versions-ID och markerat som den aktuella versionen av objektet. Om du skapar en ny instans med samma objekt namn får det nya objektet ett unikt versions-ID, vilket gör att den blir den aktuella versionen.  

Objekt i Key Vault kan åtgärdas genom att ange en version eller genom att utesluta version för åtgärder på den aktuella versionen av objektet. Till exempel kan en nyckel med namnet, som `MasterKey` utför åtgärder utan att ange en version, orsaka att systemet använder den senaste tillgängliga versionen. Att utföra åtgärder med den versions bara identifierade identifieraren gör att systemet använder den aktuella versionen av objektet.  

### <a name="vault-name-and-object-name"></a>Valv-namn och objekt namn
Objekt identifieras unikt inom Key Vault med hjälp av en URL. Inga två objekt i systemet har samma URL, oavsett Geo-plats. Den fullständiga URL: en till ett objekt kallas för objekt-ID. URL: en består av ett prefix som identifierar Key Vault, objekt typ, användardefinierat objekt namn och en objekt version. Objekt namnet är Skift läges okänsligt och oföränderligt. Identifierare som inte innehåller objekt versionen kallas för bas identifierare.  

Mer information finns i [autentisering, begär Anden och svar](authentication-requests-and-responses.md)

En objekt identifierare har följande allmänna format (beroende på behållar typ):  

- **För valv**: `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **För hanterade HSM-pooler**: `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Se [stöd för objekt](#object-types) typer för typer av objekt som stöds av varje behållar typ.

Plats:  

| Element | Beskrivning |  
|-|-|  
|`vault-name` eller `hsm-name`|Namnet på ett valv eller en hanterad HSM-pool i Microsoft Azure Key Vault-tjänsten.<br /><br />Valv namn och namn på hanterad HSM-pool väljs av användaren och är globalt unika.<br /><br />Valv namnet och namnet på den hanterade HSM-poolen måste vara en 3-24-tecken sträng som bara innehåller 0-9, a-z, A-Z och-.|  
|`object-type`|Objektets typ, "nycklar", "hemligheter" eller "certifikat".|  
|`object-name`|Ett `object-name` är ett användardefinierat namn för och måste vara unikt inom en Key Vault. Namnet måste vara en 1-127-tecken sträng, med början på en bokstav och endast innehålla 0-9, a-z, A-Z och-.|  
|`object-version`|En `object-version` är en systemgenererad 32 tecken Strängs identifierare som kan användas för att adressera en unik version av ett objekt.|  

## <a name="next-steps"></a>Nästa steg

- [Om nycklar](../keys/about-keys.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
