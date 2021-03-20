---
title: ta med fil
description: ta med fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76279579"
---
Azure Cosmos DB-bindningar stöds endast för användning med SQL-API:n. För alla andra Azure Cosmos DB-API: er ska du komma åt databasen från din funktion med hjälp av den statiska klienten för ditt API, inklusive [Azure Cosmos DB s API för MongoDB](../articles/cosmos-db/mongodb-introduction.md), [API för Cassandra](../articles/cosmos-db/cassandra-introduction.md), [Gremlin API](../articles/cosmos-db/graph-introduction.md)och [tabell-API](../articles/cosmos-db/table-introduction.md).
