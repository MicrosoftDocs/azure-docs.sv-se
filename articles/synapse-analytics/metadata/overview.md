---
title: Modell för delad metadata
description: Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och tabeller mellan server lös Apache Spark pooler, SQL-pool utan server och dedikerade SQL-pooler.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: b10b6f011fa7daee4094f0cc7b819d36127fedcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460351"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Delade Azure Synapse Analytics-metadata

Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och tabeller mellan server lös Apache Spark pooler och SQL-pool utan server.

Delningen har stöd för det s.k. moderna informations lager mönstret och ger arbets ytans SQL-motorer åtkomst till databaser och tabeller som skapats med Spark. Det gör det också möjligt för SQL-motorer att skapa egna objekt som inte delas med andra motorer.

## <a name="support-the-modern-data-warehouse"></a>Stöd för modern informations lager

Den delade metadata-modellen stöder det moderna informations lagrets mönster på följande sätt:

1. Data från data Lake förbereds och struktureras effektivt med Spark genom att lagra de förberedda data i (eventuellt partitionerade) Parquet tabeller som finns i möjligen flera databaser.

2. Spark skapade databaser och alla deras tabeller blir synliga i någon av instanserna i Azure Synapse-arbets ytan Spark-pool och kan användas från alla Spark-jobb. Den här funktionen omfattas av [behörigheterna](#security-model-at-a-glance) eftersom alla Spark-pooler i en arbets yta delar samma underliggande katalog-meta-arkiv.

3. Spark-skapade databaser och deras Parquet tabeller blir synliga i SQL-poolen för arbets ytans Server. [Databaser](database.md) skapas automatiskt i SQL-poolens metadata för SQL-poolen och både de [externa och hanterade tabeller](table.md) som skapas av ett Spark-jobb blir tillgängliga som externa tabeller i SQL-poolens schema-metadata i `dbo` schemat för motsvarande databas. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Synkronisering av objekt sker asynkront. Objekt får en liten fördröjning på några sekunder tills de visas i SQL-kontexten. När de visas kan de frågas, men inte uppdateras eller ändras inte av de SQL-motorer som har åtkomst till dem.

## <a name="shared-metadata-objects"></a>Delade metadata-objekt

Med Spark kan du skapa databaser, externa tabeller, hanterade tabeller och vyer. Eftersom Spark-vyer kräver en spark-motor för att bearbeta definitionen av Spark SQL-instruktionen och inte kan bearbetas av en SQL-motor, delas bara databaser och deras externa och hanterade tabeller som använder lagrings Parquet lagrings utrymme med SQL-motorn för arbets ytan. Spark-vyer delas bara mellan instanserna i Spark-poolen.

## <a name="security-model-at-a-glance"></a>Säkerhets modell snabbt

Spark-databaser och-tabeller, tillsammans med deras synkroniserade representationer i SQL-motorn, skyddas på den underliggande lagrings nivån. När en fråga skickas från någon av de motorer som den som skickar frågan har rätt att använda, skickas frågans säkerhets objekt till de underliggande filerna. Behörigheter kontrolleras på filsystemnivå.

Mer information finns i den [delade Azure Synapse Analytics-databasen](database.md).

## <a name="change-maintenance"></a>Ändra underhåll

Om ett metadataobjekt tas bort eller ändras med Spark, hämtas ändringarna och sprids sedan till den serverbaserade SQL-poolen. Synkroniseringen är asynkron och ändringar visas i SQL-motorn efter en kort fördröjning.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om databaserna för delade metadata i Azure Synapse Analytics](database.md)
- [Läs mer om delade metadata-tabeller för Azure Synapse Analytics](table.md)

