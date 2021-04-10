---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734052"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Azure App-push | 10 Azure App-åtgärder per åtgärds grupp. | Samma som standard |
| E-post | 1 000 e-poståtgärder i en åtgärds grupp.<br>Högst 100 e-postmeddelanden på en timme.<br>Se även [frekvensen för att begränsa informationen](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Samma som standard |
| ITSM | 10 ITSM-åtgärder i en åtgärds grupp. | Samma som standard | 
| Logikapp | 10 Logic app-åtgärder i en åtgärds grupp. | Samma som standard |
| Runbook | 10 Runbook-åtgärder i en åtgärds grupp. | Samma som standard |
| SMS | 10 SMS-åtgärder i en åtgärds grupp.<br>Högst ett SMS-meddelande var 5: e minut.<br>Se även [frekvensen för att begränsa informationen](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Samma som standard |
| Röst | 10 röst åtgärder i en åtgärds grupp.<br>Högst 1 röst samtal var 5: e minut.<br>Se även [frekvensen för att begränsa informationen](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Samma som standard |
| Webhook | 10 webhook-åtgärder i en åtgärds grupp.  Maximalt antal webhook-anrop är 1500 per minut per prenumeration. Andra gränser är tillgängliga med [Åtgärds-speciell information](../articles/azure-monitor/alerts/action-groups.md#action-specific-information).  | Samma som standard |