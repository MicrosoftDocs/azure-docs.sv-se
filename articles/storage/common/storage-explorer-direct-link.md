---
title: Azure Storage Explorer direkt länk | Microsoft Docs
description: Dokumentation för Azure Storage Explorer Direct Link
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582336"
---
# <a name="azure-storage-explorer-direct-link"></a>Azure Storage Explorer direkt länk

I Windows och macOS stöder Storage Explorer URI: er med `storageexplorer://` protokollet. Dessa URI: er kallas direkta länkar. En direkt länk pekar till en Azure Storage resurs i Storage Explorer. När du följer en direkt länk öppnas Storage Explorer och navigerar till den resurs som den pekar på. Den här artikeln beskriver hur direkta länkar fungerar och hur du kan använda dem.

## <a name="how-direct-links-work"></a>Så här fungerar direkta länkar

Storage Explorer använder trädvyn för att visualisera resurser i Azure. En direkt länk innehåller den hierarkiska informationen för den länkade resurs-noden i trädet. När en direkt länk följs, öppnar Storage Explorer och tar emot parametrarna i den direkta länken. Storage Explorer använder sedan dessa parametrar för att navigera till den länkade resursen i trädvyn.

> [!IMPORTANT]
> Du måste vara inloggad och ha de behörigheter som krävs för att få åtkomst till den länkade resursen för att en direkt länk ska fungera.

## <a name="parameters"></a>Parametrar

En Storage Explorer Direct-länk börjar alltid med protokollet `storageexplorer://` . I följande tabell förklaras var och en av de möjliga parametrarna i en direkt länk.

Parameter | Beskrivning
:---------| :---------
`v`         | Version av Direct Link-protokollet.
`accountid` | Azure Resource Manager resurs-ID för den länkade resursens lagrings konto. Om den länkade resursen är ett lagrings konto, är detta ID Azure Resource Manager resurs-ID för det lagrings kontot. Annars är detta ID Azure Resource Manager resurs-ID för det lagrings konto som den länkade resursen tillhör.
`resourcetype` | Valfritt. Används endast när den länkade resursen är en BLOB-behållare, en fil resurs, en kö eller en tabell. Måste vara antingen en av "Azure. BlobContainer", "Azure. FileShare", "Azure. Queue", "Azure. FileShare".
`resourcename` | Valfritt. Används endast när den länkade resursen är en BLOB-behållare, en fil resurs, en kö eller en tabell. Namnet på den länkade resursen.

Här är ett exempel på en direkt länk till en BLOB-behållare. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Hämta en direkt länk från Storage Explorer

Du kan använda Storage Explorer för att få en direkt länk till en resurs. Öppna snabb menyn för noden för resursen i trädvyn. Använd sedan åtgärden kopiera direkt länk för att kopiera dess direkt länk till Urklipp.

## <a name="direct-link-limitations"></a>Direkta länk begränsningar

Direkta länkar stöds bara för resurser under prenumerations noder. Dessutom stöds endast följande resurs typer:

- Lagringskonton
- Blobcontainer
- Köer
- Filresurser
- Tables
