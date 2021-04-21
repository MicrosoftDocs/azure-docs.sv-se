---
title: Key Vault .NET 2.x API Release Notes| Microsoft Docs
description: Lär dig hur du uppdaterar appar som skrivits för tidigare versioner Azure Key Vault att fungera med 2.0-versionen av Azure Key Vault-biblioteket för C# och .NET.
services: key-vault
author: msmbaldwin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: b923cc289dc0229f18eba144b09ceb34e0edea4e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751894"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 – Release Notes and Migration Guide
Följande information hjälper dig att migrera till 2.0-versionen av Azure Key Vault för C# och .NET.  Appar som skrivits för tidigare versioner måste uppdateras för att stödja den senaste versionen.  Dessa ändringar krävs för att ge fullständigt stöd för nya och förbättrade funktioner, till **exempel Key Vault certifikat**.

## <a name="key-vault-certificates"></a>Key Vault certifikat

Key Vault hanterar x509-certifikat och stöder följande beteenden:  

* Skapa certifikat via en Key Vault skapa process eller importera ett befintligt certifikat. Detta inkluderar både själv signerade certifikat och certifikatutfärdargenererade certifikat.
* Lagra och hantera x509-certifikatlagring på ett säkert sätt utan interaktion med material med privat nyckel.  
* Definiera principer som dirigerar Key Vault att hantera certifikatets livscykel.  
* Ange kontaktuppgifter för livscykelhändelser, till exempel förfallovarningar och förnyelsemeddelanden.  
* Förnya certifikat automatiskt med valda utfärdare (Key Vault-partner X509-certifikatproviders och certifikatutfärdare).* Supportcertifikat från alternativa (icke-partner) tillhandahåller och certifikatutfärdare (stöder inte automatisk förnyelse).  

## <a name="net-support"></a>.NET-stöd

* **.NET 4.0** stöds inte av 2.0-versionen av Azure Key Vault .NET-biblioteket
* **.NET Framework 4.5.2** stöds av 2.0-versionen av Azure Key Vault .NET-biblioteket
* **.NET Standard 1.4** stöds av 2.0-versionen av Azure Key Vault .NET-biblioteket

## <a name="namespaces"></a>Namnrymder

* Namnområdet för **modeller ändras** från **Microsoft.Azure.KeyVault** till **Microsoft.Azure.KeyVault.Models**.
* Namnområdet **Microsoft.Azure.KeyVault.Internal** tas bort.
* Följande namnrymder för Azure SDK-beroenden har 

    - **Hyak.Common** är nu **Microsoft.Rest**.
    - **Hyak.Common.Internals är** nu **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Typändringar

* *Hemligheten* har *ändrats till SecretBundle*
* *Ordlistan* har ändrats *till IDictionary*
* *Lista \<T> , sträng [] har* ändrats till *IList \<T>*
* *NextList har* ändrats  *till NextPageLink*

## <a name="return-types"></a>Returtyper

* **KeyList** och **SecretList** returnerar nu *IPage i \<T>* stället för *ListKeysResponseMessage*
* Den **genererade BackupKeyAsync** returnerar *nu BackupKeyResult*, som *innehåller Värde* (säkerhetskopieringsblob). Tidigare var metoden omsluten och returnerade bara värdet.

## <a name="exceptions"></a>Undantag

* *KeyVaultClientException* har ändrats *till KeyVaultErrorException*
* Tjänstfelet har ändrats från *undantag. Fel* till *undantag. Body.Error.Message*.
* Ytterligare information har tagits bort från felmeddelandet **för [JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorer

* I stället för att *acceptera en HttpClient* som ett konstruktorargument accepterar konstruktorn bara *HttpClientHandler* *eller DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Hämtade paket

När en klient bearbetar Key Vault beroende laddas följande paket ned:

### <a name="previous-package-list"></a>Föregående paketlista

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuell paketlista

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klassändringar

* **UnixEpoch-klassen** har tagits bort.
* **Klassen Base64UrlConverter** har bytt namn till **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Andra ändringar

* Stöd för konfiguration av KV-åtgärdsprincip för återförsök vid tillfälliga fel har lagts till i den här versionen av API:et.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* För de åtgärder som returnerade ett *valv* var returtypen en klass som innehöll en **Vault-egenskap.** Returtypen är nu *Vault*.
* *PermissionsToKeys* och *PermissionsToSecrets är* nu *Permissions.Keys* och *Permissions.Secrets*
* Vissa ändringar av returtyper gäller även för kontrollplanet.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Paketet är uppdelat i **Microsoft.Azure.KeyVault.Extensions** och **Microsoft.Azure.KeyVault.Cryptography** för kryptografiåtgärderna.

