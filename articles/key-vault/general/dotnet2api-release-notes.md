---
title: Viktig information om Key Vault .NET 2. x API | Microsoft Docs
description: Lär dig hur du uppdaterar appar som skrivits för tidigare versioner av Azure Key Vault att arbeta med 2,0-versionen av Azure Key Vault-biblioteket för C# och .NET.
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013963"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2,0 – viktig information och migrations guide
Med följande information kan du migrera till 2,0-versionen av Azure Key Vault-biblioteket för C# och .NET.  Appar som är skrivna för tidigare versioner måste uppdateras för att stödja den senaste versionen.  De här ändringarna krävs för att fullständigt stödja nya och förbättrade funktioner, till exempel **Key Vault certifikat**.

## <a name="key-vault-certificates"></a>Key Vault certifikat

Key Vault certifikat hanterar x509-certifikat och stöder följande beteenden:  

* Skapa certifikat via en Key Vault skapande process eller importera ett befintligt certifikat. Detta inkluderar både självsignerade certifikat och certifikat utfärdare (CA) som genereras.
* Lagra och hantera x509-certifikat Storage på ett säkert sätt utan interaktion med hjälp av material från privat nyckel.  
* Definiera principer som dirigerar Key Vault för att hantera certifikat livs cykeln.  
* Ange kontakt information för livs cykel händelser, till exempel utgångs varningar och förnyelse meddelanden.  
* Förnya automatiskt certifikat med valda utfärdare (Key Vault partners för X509-certifikat och certifikat utfärdare). * stöd certifikat från alternativ (icke-partner) tillhandahåller och certifikat utfärdare (stöder inte automatisk förnyelse).  

## <a name="net-support"></a>.NET-support

* **.Net 4,0** stöds inte av 2,0-versionen av Azure Key Vault .net-biblioteket
* **.NET Framework 4.5.2** stöds av 2,0-versionen av Azure Key Vault .net-biblioteket
* **.Net Standard 1,4** stöds av 2,0-versionen av Azure Key Vault .net-biblioteket

## <a name="namespaces"></a>Namnrymder

* Namn området för **modeller** ändras från **Microsoft. Azure. nyckel valv** till **Microsoft. Azure. nyckel valv. modeller**.
* **Microsoft. Azure. nyckel valvet. internt** namn område har släppts.
* Följande namn områden för Azure SDK-beroenden har 

    - **Hyak. common** är nu **Microsoft. rest**.
    - **Hyak. Common. internals** är nu **Microsoft. rest. serializering**.

## <a name="type-changes"></a>Skriv ändringar

* *Hemligheten* ändrades till *SecretBundle*
* *Ord listan* har ändrats till *IDictionary*
* *Lista \<T> , sträng [] har* ändrats *till \<T> ilist*
* *NextList* ändrades till  *NextPageLink*

## <a name="return-types"></a>Retur typer

* I **list** -och **SecretList** returneras nu *iPage \<T>* i stället för *ListKeysResponseMessage*
* Den genererade **BackupKeyAsync** returnerar nu *BackupKeyResult*, som innehåller *värde* (back upp BLOB). Tidigare har metoden omslutits och returnerade bara värdet.

## <a name="exceptions"></a>Undantag

* *KeyVaultClientException* har ändrats till *KeyVaultErrorException*
* Tjänst felet har ändrats från *undantag. Fel* i *undantag. Body. error. Message*.
* Ytterligare information togs bort från fel meddelandet för **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorer

* I stället för att acceptera ett *httpclient* som ett konstruktor-argument accepterar konstruktorn bara *HttpClientHandler* eller *DelegatingHandler []*.

## <a name="downloaded-packages"></a>Hämtade paket

När en klient bearbetar ett Key Vault beroende hämtas följande paket:

### <a name="previous-package-list"></a>Lista över tidigare paket

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Lista över aktuella paket

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klass ändringar

* **UnixEpoch** -klassen har tagits bort.
* **Base64UrlConverter** -klassen har bytt namn till **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Andra ändringar

* Stöd för konfiguration av KV-åtgärdens återförsöks princip för tillfälliga fel har lagts till i den här versionen av API: et.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft. Azure. Management. NuGet för nyckel valv

* För de åtgärder som returnerade ett *valv* var retur typen en klass som innehöll en **valv** egenskap. Retur typen är nu *valv*.
* *PermissionsToKeys* och *PermissionsToSecrets* är nu *behörigheter. nycklar* och *behörigheter. hemligheter*
* Vissa ändringar av retur typer gäller även för kontroll planet.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft. Azure. nyckel valv. NuGet för tillägg

* Paketet är uppdelat till **Microsoft. Azure. nyckel valv. Extensions** och **Microsoft. Azure. nyckel valv. kryptografi** för kryptografi åtgärder.

