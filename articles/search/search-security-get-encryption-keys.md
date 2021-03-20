---
title: Hämta information om krypterings nyckel
titleSuffix: Azure Cognitive Search
description: Hämta krypterings nyckelns namn och version som används i en index-eller synonym mappning så att du kan hantera nyckeln i Azure Key Vault.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88932907"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Hämta kundhanterad viktig information från index och synonym kartor

I Azure Kognitiv sökning skapas, lagras och hanteras kund hanterade krypterings nycklar i Azure Key Vault. Om du behöver avgöra om ett objekt är krypterat, eller vilket nyckel namn eller vilken version som använts, använder du REST API eller ett SDK för att hämta egenskapen **encryptionKey** från en index-eller synonym mappnings definition. 

Vi rekommenderar att du [aktiverar loggning](../key-vault/general/logging.md) på Key Vault så att du kan övervaka nyckel användningen.

## <a name="get-the-admin-api-key"></a>Hämta administrations-API-nyckeln

Om du vill hämta objekt definitioner från en Sök tjänst måste du autentisera med administratörs behörighet. Det enklaste sättet att hämta Admin API-nyckeln är via portalen.

1. Logga in på [Azure Portal](https://portal.azure.com/) och öppna översikts sidan Sök tjänst.

1. Klicka på **nycklar** på vänster sida och kopiera ett administrations-API. Det krävs en administratörs nyckel för att hämta index och synonym mappning.

För återstående steg växlar du till PowerShell och REST API. Portalen visar inte synonym Maps eller egenskaperna för krypterings nyckeln för index.

## <a name="use-powershell-and-rest"></a>Använda PowerShell och REST

Kör följande kommandon för att ställa in variablerna och hämta objekt definitioner.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet vilken krypterings nyckel och version som används kan du hantera nyckeln i Azure Key Vault eller kontrol lera andra konfigurations inställningar.

+ [Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Konfigurera Kundhanterade nycklar för data kryptering i Azure Kognitiv sökning](search-security-manage-encryption-keys.md)