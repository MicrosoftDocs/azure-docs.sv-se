---
title: Hantera watchlists i Azure Sentinel med REST API | Microsoft Docs
description: I den här artikeln beskrivs hur Azure Sentinel watchlists kan hanteras med hjälp av Log Analytics "REST API för att skapa, ändra och ta bort watchlists och deras objekt.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798500"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Hantera watchlists i Azure Sentinel med REST API

> [!IMPORTANT]
> Funktionen watchlists är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Azure Sentinel, som bygger delvis på Azure Monitor Log Analytics, gör att du kan använda Log Analytics REST API för att hantera watchlists. Det här dokumentet visar hur du skapar, ändrar och tar bort watchlists och deras objekt med hjälp av REST API.  Watchlists som skapas på det här sättet kommer att visas i användar gränssnittet för Azure Sentinel.

## <a name="common-uri-parameters"></a>Vanliga URI-parametrar

Följande är de vanliga URI-parametrarna för alla visnings lista API-kommandon:

| Name | I | Krävs | Typ | Description |
|-|-|-|-|-|
| **SubscriptionId** | path | ja | GUID | ID för Azure-prenumeration |
| **ResourceGroupName** | path | ja | sträng | namnet på resurs gruppen i prenumerationen |
| **WorkspaceName** | path | ja | sträng | namnet på Log Analytics arbets ytan |
| **{watchlistAlias}** | path | Ja | sträng | namnet på en specifik visnings lista<br>\* Krävs inte vid hämtning av alla watchlists |
| **{watchlistItemId}** | path | Ja * * | GUID | ID för det objekt som ska skapas i, läggas till eller tas bort från visnings lista<br>\*\* Krävs endast för kommandon för visnings lista-objekt |
| **{API-version}** | DocumentDB | ja | sträng | den version av protokollet som används för att utföra den här begäran. Från och med den 29 oktober 2020 är API-versionen för visnings lista *2019-01-01 – för hands* version |
| **{bearerToken}** | auktorisering | ja | token | token för auktorisering av innehavare |
|  

## <a name="retrieve-all-watchlists"></a>Hämta alla watchlists

Det här kommandot hämtar alla watchlists som är kopplade till en arbets yta, utan deras objekt.

### <a name="request-uri"></a>URI för förfrågan
(URI är en enskild rad, uppdelad för enkel läsbarhet)

| Metod | URI för förfrågan |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Svar

| Statuskod | Själva svaret | Description |
|-|-|-|
| 200/OK | Lista över befintliga watchlists eller tomt om inga visnings lista hittades |  |
| 400/Felaktig begäran |  | Felaktig syntax för begäran, ogiltig parameter för begäran... |
|

## <a name="look-up-a-watchlist-by-name"></a>Leta upp en visnings lista efter namn

Det här kommandot hämtar en speciell visnings lista som är associerad med en arbets yta, utan dess objekt.

### <a name="request-uri"></a>URI för förfrågan
(URI är en enskild rad, uppdelad för enkel läsbarhet)

| Metod | URI för förfrågan |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Svar

| Statuskod | Själva svaret | Description |
|-|-|-|
| 200/OK | Den begärda visnings lista |  |
| 400/Felaktig begäran |  | Felaktig syntax för begäran, ogiltig parameter för begäran... |
| 404/hittades inte |  | Ingen visnings lista hittades med det begärda namnet |
|

## <a name="create-a-watchlist"></a>Skapa en visnings lista

Det här kommandot skapar en visnings lista och lägger till objekt i den. Den tar två anrop till den här slut punkten för att skapa visnings lista och dess objekt: den första skapar visnings lista (överordnad) och den andra kommer att lägga till objekten.

### <a name="request-uri"></a>URI för förfrågan
(URI är en enskild rad, uppdelad för enkel läsbarhet)

| Metod | URI för förfrågan |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Begärandetext

Här är ett exempel på en begär ande text för en visnings lista Create-begäran:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Svar

| Statuskod | Själva svaret | Description |
|-|-|-|
| 200/OK | Visnings lista som skapats av begäran, utan objekt |  |
| 400/Felaktig begäran |  | Felaktig syntax för begäran, ogiltig parameter för begäran... |
| 409/konflikt |  | Åtgärden misslyckades, det finns en befintlig visnings lista med samma alias |
|

## <a name="delete-a-watchlist"></a>Ta bort en visnings lista

Det här kommandot tar bort en visnings lista och dess objekt.

### <a name="request-uri"></a>URI för förfrågan
(URI är en enskild rad, uppdelad för enkel läsbarhet)

| Metod | URI för förfrågan |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Svar

| Statuskod | Själva svaret | Description |
|-|-|-|
| 200/OK | Tomt svars text |  |
| 204/inget innehåll | Tomt svars text | Inget borttaget |
| 400/Felaktig begäran |  | Felaktig syntax för begäran, ogiltig parameter för begäran... |
|

## <a name="add-or-update-a-watchlist-item"></a>Lägga till eller uppdatera ett visnings lista-objekt

När det här kommandot körs på en befintlig *watchlisItemId* (ett GUID), kommer det att uppdatera objektet med de data som anges i begär ande texten. Annars skapas ett nytt objekt.

### <a name="request-uri"></a>URI för förfrågan
(URI är en enskild rad, uppdelad för enkel läsbarhet)

| Metod | URI för förfrågan |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Begärandetext

Här är ett exempel på en begär ande text för en visnings lista-artikel begäran om tillägg/uppdatering:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Svar

| Statuskod | Själva svaret | Description |
|-|-|-|
| 200/OK | Visnings lista-objektet som skapades eller uppdaterades av begäran |  |
| 400/Felaktig begäran |  | Felaktig syntax för begäran, ogiltig parameter för begäran... |
| 409/konflikt |  | Åtgärden misslyckades, det finns en befintlig visnings lista med samma alias |
|

## <a name="delete-a-watchlist-item"></a>Ta bort ett visnings lista-objekt

Det här kommandot tar bort ett befintligt visnings lista-objekt.

### <a name="request-uri"></a>URI för förfrågan
(URI är en enskild rad, uppdelad för enkel läsbarhet)

| Metod | URI för förfrågan |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Svar

| Statuskod | Själva svaret | Description |
|-|-|-|
| 200/OK | Tomt svars text |  |
| 204/inget innehåll | Tomt svars text | Inget borttaget |
| 400/Felaktig begäran |  | Felaktig syntax för begäran, ogiltig parameter för begäran... |
|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar watchlists och deras objekt i Azure Sentinel med hjälp av Log Analytics API. Mer information om Azure Sentinel finns i följande artiklar:

- Läs mer om [watchlists](watchlists.md)
- Utforska andra användnings områden för [Azure SENTINEL API](/rest/api/securityinsights/)