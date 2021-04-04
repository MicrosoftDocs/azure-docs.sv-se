---
title: Aktiviteter och program för meddelanderoutning – Azure Service Bus | Microsoft Docs
description: Den här artikeln innehåller en översikt över hur du utvecklar aktiviteter och program för meddelande replikering med Azure Functions
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657594"
---
# <a name="message-replication-tasks-and-applications"></a>Aktiviteter och program för meddelandereplikering

Som förklaras i [Federations artikeln för meddelande replikering och över flera regioner](service-bus-federation-overview.md) , är replikering av meddelande sekvenser mellan par av Service Bus entiteter och mellan Service Bus och andra meddelande källor och mål i allmänhet lean på Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) är en skalbar och tillförlitlig körnings miljö för att konfigurera och köra program utan server, inklusive [meddelande replikering och Federations](service-bus-federation-overview.md) uppgifter.

I den här översikten får du lära dig mer om att Azure Functions inbyggda funktioner för sådana program, om kodblock som du kan anpassa och ändra för omvandlings uppgifter samt hur du konfigurerar ett Azure Functions program så att det kan integreras med Service Bus och andra Azure Messaging-tjänster. För många detaljer kommer den här artikeln att peka på Azure Functions-dokumentationen.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
