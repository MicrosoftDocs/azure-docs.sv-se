---
title: Aktiviteter och program för händelse replikering – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över hur du skapar aktiviteter och program för händelse replikering med Azure Functions
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97663700"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Aktiviteter och program för händelse replikering med Azure Functions

> [!TIP]
> För alla tillstånds känsliga aktiviteter där du behöver fundera över nytto lasterna för dina händelser och transformera, aggregera, utöka eller minska dem använder [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i stället för Azure Functions.

Som förklaras i [händelse replikeringen och Federations artikeln över flera regioner](event-hubs-federation-overview.md) är tillstånds lös replikering av händelse strömmar mellan par av Event Hubs och mellan Event Hubs och andra händelse Ströms källor och mål som är Lean-baserade på Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) är en skalbar och tillförlitlig körnings miljö för att konfigurera och köra program utan server, inklusive Event replikering och Federations uppgifter.

I den här översikten får du lära dig mer om att Azure Functions inbyggda funktioner för sådana program, om kodblock som du kan anpassa och ändra för omvandlings uppgifter samt hur du konfigurerar ett Azure Functions program så att det kan integreras med Event Hubs och andra Azure Messaging-tjänster. För många detaljer kommer den här artikeln att peka på Azure Functions-dokumentationen.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









