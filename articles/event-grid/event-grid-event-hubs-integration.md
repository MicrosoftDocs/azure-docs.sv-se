---
title: 'Självstudie: Skicka Event Hubs data till data lagret – Event Grid'
description: Beskriver hur du lagrar Event Hubs fångade data i Azure Synapse Analytics via Azure Functions och Event Grid utlösare.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854724"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Självstudie: strömma Big data till ett informations lager
Azure [Event Grid](overview.md) är en intelligent Event routing-tjänst som gör att du kan reagera på meddelanden eller händelser från appar och tjänster. Den kan till exempel utlösa en Azure-funktion för att bearbeta Event Hubs data som har registrerats till en blob-lagring eller Data Lake Storage. Det här [exemplet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) visar hur du använder Event Grid och Azure Functions för att migrera insamlade Event Hubs data från Blob Storage till Azure Synapse Analytics, särskilt en dedikerad SQL-pool.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Nästa steg

* Läs om skillnaderna mellan Azures meddelandetjänster i [Välj mellan Azure-tjänster som levererar meddelanden](compare-messaging-services.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* En introduktion till Event Hubs Capture finns i [Enable Event Hubs Capture using the Azure portal](../event-hubs/event-hubs-capture-enable-through-portal.md) (Aktivera Event Hubs Capture med hjälp av Azure Portal).
* Mer information om hur du konfigurerar och kör exemplet finns i [Event Hubs Capture- och Event Grid-exemplet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
