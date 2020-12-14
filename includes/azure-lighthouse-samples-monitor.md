---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356283"
---
De här exemplen visar hur du använder Azure Monitor för att skapa aviseringar för prenumerationer som har registrerats för Azure-delegerad resurshantering.

| **Mall** | **Beskrivning** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Skickar frågor till den senaste 1 dagen av aktiviteten i en hanteringsklient och [rapporterar om eventuella tillagda eller borttagna delegeringar](../articles/lighthouse/how-to/monitor-delegation-changes.md) (eller försök som inte lyckades).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Den här mallen skapar en Azure-avisering och ansluter till en befintlig åtgärdsgrupp.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Skapar flera loggaviseringar baserat på Kusto-frågor.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Distribuerar en avisering i en klientorganisation när en användare delegerar en prenumeration till en hanteringsklient.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Visar Azure-aktivitetsloggar för olika prenumerationer med ett alternativ för att filtrera dem efter domännamn. |
