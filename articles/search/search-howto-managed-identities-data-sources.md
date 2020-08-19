---
title: Konfigurera en anslutning till en data källa med hjälp av en hanterad identitet (förhands granskning)
titleSuffix: Azure Cognitive Search
description: Lär dig hur du konfigurerar en Indexer-anslutning till en data källa med hjälp av en hanterad identitet (förhands granskning)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 6b07236fd639c9878c59523f78de5215b173ffc6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553172"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity-preview"></a>Konfigurera en indexerare-anslutning till en data källa med hjälp av en hanterad identitet (förhands granskning)

> [!IMPORTANT] 
> Stöd för att konfigurera en anslutning till en data källa med hjälp av en hanterad identitet finns för närvarande i offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.

En [indexerare](search-indexer-overview.md) i Azure kognitiv sökning är en sökrobot som gör det möjligt att hämta data från data källan till Azure kognitiv sökning. En indexerare hämtar en data källans lutning från det data käll objekt som du skapar. Objektet datakällobjektet innehåller vanligt vis autentiseringsuppgifter för mål data källan. Objektet datakällobjektet kan till exempel innehålla en Azure Storage konto nyckel om du vill indexera data från en Blob Storage-behållare.

I många fall är det inte ett problem med att tillhandahålla autentiseringsuppgifter direkt i datakällobjektet, men det finns vissa utmaningar som kan uppstå:
* Hur gör jag för att ska autentiseringsuppgifterna vara säkra i min kod som skapar datakällobjektet?
* Om min konto nyckel eller lösen ord komprometteras och jag behöver ändra det, måste jag nu uppdatera mina data käll objekt med den nya konto nyckeln eller lösen ordet så att min indexerare kan ansluta till data källan igen.

Dessa problem kan lösas genom att konfigurera anslutningen med hjälp av en hanterad identitet.

## <a name="using-managed-identities"></a>Använda hanterade identiteter

[Hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) är en funktion som tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här funktionen i Azure Kognitiv sökning för att skapa ett data käll objekt med en anslutnings sträng som inte innehåller några autentiseringsuppgifter. I stället beviljas din Sök tjänst åtkomst till data källan via rollbaserad åtkomst kontroll (RBAC).

När du konfigurerar en data källa med hjälp av en hanterad identitet kan du ändra autentiseringsuppgifterna för data källan och indexerarna kommer fortfarande att kunna ansluta till data källan. Du kan också skapa data käll objekt i din kod utan att behöva inkludera en konto nyckel eller använda Key Vault för att hämta en konto nyckel.

## <a name="limitations"></a>Begränsningar

Följande data källor stöder konfiguration av en indexerare-anslutning med hanterade identiteter. 

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (för hands version), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

Följande funktioner stöder för närvarande inte användning av hanterade identiteter för att konfigurera anslutningen:
* Knowledge Store
* Anpassade färdigheter
 
## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du konfigurerar en indexerare-anslutning med hanterade identiteter:

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (för hands version), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)