---
title: Indexerings åtkomst till Azure Storage med ett betrott tjänst undantag
titleSuffix: Azure Cognitive Search
description: Aktivera data åtkomst av en indexerare i Azure Kognitiv sökning till data som lagrats säkert i Azure Storage.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92101383"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Indexerings åtkomst till Azure Storage med hjälp av Trusted service Exception (Azure Kognitiv sökning)

Indexerare i en Azure Kognitiv sökning-tjänst som har åtkomst till data i Azure Storage-konton kan använda funktionen för [betrott tjänst undantag](../storage/common/storage-network-security.md#exceptions) för säker åtkomst till data. Den här mekanismen ger kunder som inte kan bevilja [indexerings åtkomst med IP-brandvägg-regler](search-indexer-howto-access-ip-restricted.md) ett enkelt, säkert och kostnads fritt alternativ för åtkomst till data i lagrings konton.

> [!NOTE]
> Stöd för åtkomst till data i lagrings konton via ett betrott tjänst undantag är begränsat till Azure Blob Storage och Azure Data Lake Gen2-lagring. Azure Table Storage stöds inte.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Steg 1: Konfigurera en anslutning med en hanterad identitet

Följ anvisningarna i [skapa en anslutning till ett Azure Storage konto med en hanterad identitet](search-howto-managed-identities-storage.md). När du är klar har du registrerat din Sök tjänst med Azure Active Directory som en betrodd tjänst, och du har beviljat behörigheter i Azure Storage som ger Sök identiteten behörighet att komma åt data eller information.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Steg 2: Tillåt att betrodda Microsoft-tjänster får åtkomst till lagrings kontot

I Azure Portal går du till fliken **brand väggar och virtuella nätverk** i lagrings kontot. Kontrol lera att alternativet **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** är markerat. Det här alternativet tillåter endast den angivna search service-instansen med lämplig rollbaserad åtkomst till lagrings kontot (stark autentisering) för att komma åt data i lagrings kontot, även om det skyddas av IP-brandväggs regler.

![Undantag för betrodda tjänster](media\search-indexer-howto-secure-access\exception.png "Undantag för betrodda tjänster")

Indexerare kommer nu att kunna komma åt data i lagrings kontot, även om kontot skyddas via regler för IP-brandvägg.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Storage indexerare:

- [Azure Blob-indexeraren](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 indexerare](search-howto-index-azure-data-lake-storage.md)
- [Azure Table-indexeraren](search-howto-indexing-azure-tables.md)