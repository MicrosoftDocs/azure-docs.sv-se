---
title: 'Kända problem: Migrera från MongoDB till Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Läs om kända problem och begränsningar vid migrering med migreringar från MongoDB till Azure Cosmos DB med hjälp av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/27/2020
ms.openlocfilehash: be6d9d3b8a20e11c874234baae26629ce0187e4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291818"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kända problem/migrerings begränsningar med migreringar från MongoDB till Azure Cosmos DB s API för MongoDB

Kända problem och begränsningar som är kopplade till migreringar från MongoDB till Cosmos DBs API för MongoDB beskrivs i följande avsnitt.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Migreringen Miss lyckas på grund av att det felaktiga SSL-certifikatet används

* **Symptom**: det här problemet är uppenbart när en användare inte kan ansluta till MongoDB-käll servern. Användaren kan fortfarande inte ansluta trots att alla brand Väggs portar är öppna.

| Orsak         | Lösning |
| ------------- | ------------- |
| Att använda ett självsignerat certifikat i Azure Database Migration Service kan leda till att migreringen Miss lyckas på grund av ett felaktigt SSL-certifikat. Fel meddelandet kan innehålla "Fjärrcertifikatet är ogiltigt enligt validerings proceduren". | Använd ett äkta certifikat från CA.  Självsignerade certifikat används vanligt vis endast i interna tester. När du installerar ett äkta certifikat från en CA-auktoritet kan du sedan använda SSL i Azure Database Migration Service utan problem (anslutningar till Cosmos DB använda SSL över Mongo-API).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Det går inte att hämta listan över databaser som ska mappas i DMS

* **Symptom**: det går inte att hämta DB-listan på bladet **databas inställning** när du använder **data från Azure Storage** läge på bladet **Välj källa** .

| Orsak         | Lösning |
| ------------- | ------------- |
| Lagrings kontots anslutnings sträng saknar SAS-information och kan därför inte autentiseras. | Skapa SAS på BLOB-behållaren i Storage Explorer och Använd URL: en med SAS-information för behållare som källans informations anslutnings sträng.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Använda en version av databasen som inte stöds

* **Symptom**: migreringen Miss lyckas.

| Orsak         | Lösning |
| ------------- | ------------- |
| Du försöker migrera till Azure Cosmos DB från en version av MongoDB som inte stöds. | När nya versioner av MongoDB lanseras testas de för att säkerställa kompatibilitet med Azure Database Migration Service och tjänsten uppdateras regelbundet för att godkänna de senaste versionerna. Om du behöver migrera omedelbart kan du, som en lösning, exportera databaserna/samlingarna till Azure Storage och sedan peka källan till den resulterande dumpningen. Skapa SAS på BLOB-behållaren i Storage Explorer och Använd sedan URL: en med SAS-information för behållare som källans informations anslutnings sträng.<br><br> |

## <a name="next-steps"></a>Nästa steg

* Visa självstudierna [migrera MongoDB till Azure Cosmos DB s API för MongoDB online med DMS](tutorial-mongodb-cosmos-db-online.md).
* Visa självstudierna [migrera MongoDB till Azure Cosmos DB s API för MongoDB offline med DMS](tutorial-mongodb-cosmos-db.md).