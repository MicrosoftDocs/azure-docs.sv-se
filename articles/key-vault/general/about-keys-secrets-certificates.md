---
title: Azure Key Vault översikt över nycklar, hemligheter och certifikat
description: Översikt över Azure Key Vault REST-gränssnitt och utvecklarinformation för nycklar, hemligheter och certifikat.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: e3eb77d15c288c93298da6dd79a76565e5d67f96
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749950"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Azure Key Vault översikt över nycklar, hemligheter och certifikat

Azure Key Vault kan Microsoft Azure program och användare att lagra och använda flera typer av hemliga data/nyckeldata. Key Vault har stöd för två resurstyper: valv och hanterade HSM:er.

## <a name="dns-suffixes-for-base-url"></a>DNS-suffix för bas-URL
 Tabellen nedan visar det grundläggande URL DNS-suffixet som används av dataplansslutpunkten för valv och hanterade HSM-pooler i olika molnmiljöer.

Molnmiljö | DNS-suffix för valv | DNS-suffix för hanterade HSM:er
---|---|---
Azure Cloud | .vault.azure.net | .managedhsm.azure.net
Azure China Cloud | .vault.azure.cn | Stöds inte
Azure US Government | .vault.usgovcloudapi.net | Stöds inte
Azure German Cloud | .vault.microsoftazure.de | Stöds inte
|||


## <a name="object-types"></a>Objekttyper
 Tabellen nedan visar objekttyper och deras suffix i bas-URL:en.

Objekttyp|URL-suffix|Valv|Hanterade HSM-pooler
--|--|--|--
**Krypteringsnycklar**||
HSM-skyddade nycklar|/keys|Stöds|Stöds
Programvaruskyddade nycklar|/keys|Stöds|Stöds inte
**Andra objekttyper**||
Hemligheter|/secrets|Stöds|Stöds inte
Certifikat|/certificates|Stöds|Stöds inte
Lagringskontonycklar|/storage|Stöds|Stöds inte
|||
- **Kryptografiska nycklar:** Stöder flera nyckeltyper och algoritmer och möjliggör användning av programvaruskyddade och HSM-skyddade nycklar. Mer information finns i [Om nycklar.](../keys/about-keys.md)
- **Hemligheter:** Ger säker lagring av hemligheter, till exempel lösenord och databasanslutningssträngar. Mer information finns i [Om hemligheter.](../secrets/about-secrets.md)
- **Certifikat:** Stöder certifikat som bygger på nycklar och hemligheter och lägger till en automatisk förnyelsefunktion. Mer information finns i [Om certifikat](../certificates/about-certificates.md).
- **Azure Storage kontonycklar:** Kan hantera nycklar för ett Azure Storage-konto åt dig. Internt kan Key Vault lista (synkronisera) nycklar med ett Azure Storage-konto och återskapa (rotera) nycklarna med jämna mellanrum. Mer information finns i Hantera [lagringskontonycklar med Key Vault](../secrets/overview-storage-keys.md).

Mer allmän information om Key Vault finns i [Om Azure Key Vault](overview.md). Mer information om Managed HSM-pooler finns i Vad är [Azure Key Vault Managed HSM?](../managed-hsm/overview.md)


## <a name="data-types"></a>Datatyper

Se SPECIFIKATIONERna för relevanta datatyper för nycklar, kryptering och signering.  

-   **algoritm** – en algoritm som stöds för en nyckelåtgärd, till exempel RSA1_5  
-   **ciphertext-value** – chiffertextokett, kodade med Base64URL  
-   **digest-value –** utdata från en hash-algoritm, kodad med Base64URL  
-   **key-type** – en av nyckeltyperna som stöds, till exempel RSA (Rivest-Shamir-Adleman).  
-   **plaintext-value** – oktett i klartext, kodade med Base64URL  
-   **signature-value –** utdata från en signaturalgoritm, kodad med Base64URL  
-   **base64URL** – ett Base64URL [RFC4648] kodat binärt värde  
-   **booleskt** – antingen sant eller falskt  
-   **Identitet** – en identitet från Azure Active Directory (AAD).  
-   **IntDate** – ett JSON-decimalvärde som representerar antalet sekunder från 1970-01-01T0:0:0Z UTC till angivet UTC-datum/tid. Se RFC3339 för information om datum/tider, i allmänhet och UTC i synnerhet.  

## <a name="objects-identifiers-and-versioning"></a>Objekt, identifierare och versionshantering

Objekt som lagras Key Vault version skapas varje gång en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL. När ett objekt först skapas får det en unik versionsidentifierare och markeras som den aktuella versionen av objektet. Om du skapar en ny instans med samma objektnamn får det nya objektet en unik versionsidentifierare, vilket gör att det blir den aktuella versionen.  

Objekt i Key Vault kan åtgärdas genom att ange en version eller genom att utelämna version för åtgärder på den aktuella versionen av objektet. Om du till exempel har en nyckel med namnet , och utför åtgärder utan att ange en version, kommer `MasterKey` systemet att använda den senaste tillgängliga versionen. Om du utför åtgärder med den versionsspecifika identifieraren använder systemet den specifika versionen av objektet.  

### <a name="vault-name-and-object-name"></a>Valvnamn och objektnamn
Objekt identifieras unikt i en Key Vault med hjälp av en URL. Inga två objekt i systemet har samma URL, oavsett geografisk plats. Den fullständiga URL:en till ett objekt kallas objektidentifierare. URL:en består av ett prefix som identifierar Key Vault, objekttyp, användarens angivna objektnamn och en objektversion. Objektnamnet är icke-anpassningsbart och oföränderligt. Identifierare som inte innehåller objektversionen kallas basidentifierare.  

Mer information finns i [Autentisering, begäranden och svar](authentication-requests-and-responses.md)

En objektidentifierare har följande allmänna format (beroende på containertyp):  

- **För valv:**`https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **För hanterade HSM-pooler:**`https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Se [Objekttypsstöd för](#object-types) typer av objekt som stöds av varje containertyp.

Plats:  

| Element | Beskrivning |  
|-|-|  
|`vault-name` eller `hsm-name`|Namnet på ett valv eller en hanterad HSM-pool i Microsoft Azure Key Vault tjänsten.<br /><br />Valvnamn och hanterade HSM-poolnamn väljs av användaren och är globalt unika.<br /><br />Valvnamnet och namnet på managed HSM-poolen måste vara en sträng på 3–24 tecken som endast innehåller 0–9, a–z, A–Z och -.|  
|`object-type`|Typ av objekt, "nycklar", "hemligheter" eller "certifikat".|  
|`object-name`|En `object-name` är ett användargränssnittsnamn för och måste vara unikt inom en Key Vault. Namnet måste vara en sträng på 1–127 tecken som börjar med en bokstav och som endast innehåller 0–9, a–z, A–Z och -.|  
|`object-version`|En `object-version` är en systemgenererad strängidentifierare med 32 tecken som kan användas för att hantera en unik version av ett objekt.|  

## <a name="next-steps"></a>Nästa steg

- [Om nycklar](../keys/about-keys.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
