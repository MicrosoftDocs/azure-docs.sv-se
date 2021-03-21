---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006889"
---
Behållaren har följande konfigurations inställningar:

|Obligatorisk|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Spårar fakturerings information.|
|Inga|[ApplicationInsights](#applicationinsights-setting)|Aktiverar tillägg av stöd för [Azure Application insikter](/azure/application-insights) -telemetri till din behållare.|
|Ja|[Billing](#billing-configuration-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Ja|[Villkoren](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Inga|[Fluent](#fluentd-settings)|Skriver logg och, om du vill, Metric-data till en Fluent-Server.|
|Inga|HTTP-proxy|Konfigurerar en HTTP-proxy för att göra utgående begär Anden.|
|Inga|[Loggning](#logging-settings)|Tillhandahåller ASP.NET Core loggnings stöd för din behållare. |
|Inga|[Monterar](#mount-settings)|Läser och skriver data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|