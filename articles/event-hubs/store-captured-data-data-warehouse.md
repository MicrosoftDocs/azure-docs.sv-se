---
title: 'Självstudie: Migrera händelse data till Azure Synapse Analytics – Azure Event Hubs'
description: Beskriver hur du använder Azure Event Grid och funktioner för att migrera Event Hubs fångade data till Azure Synapse Analytics.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854427"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Självstudie: Migrera insamlade Event Hubs data till Azure Synapse Analytics med Event Grid och Azure Functions
Med Azure Event Hubs [Capture](./event-hubs-capture-overview.md) kan du automatiskt samla in strömmande data i Event Hubs i Azure Blob storage eller Azure Data Lake Storage. Den här självstudien visar hur du migrerar insamlade Event Hubs data från lagring till Azure Synapse Analytics med hjälp av en Azure-funktion som utlöses av [Event Grid](../event-grid/overview.md).

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Nästa steg 
Du kan använda kraftfulla verktyg för datavisualisering med ditt informationslager för att få värdefull kunskap.

Den här artikeln visar hur du använder [Power BI med Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)