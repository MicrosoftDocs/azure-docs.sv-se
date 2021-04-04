---
title: Redundans och haveriberedskap för Video Indexer
titleSuffix: Azure Media Services
description: Lär dig hur du växlar till ett sekundärt Video Indexer konto om ett fel eller haveri inträffar i ett regionalt Data Center.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87065404"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Redundans och haveriberedskap för Video Indexer

Azure Media Services Video Indexer tillhandahåller inte någon direkt redundansväxling för tjänsten om det skulle uppstå ett avbrott eller fel i ett regionalt datacenter. Den här artikeln förklarar hur du konfigurerar din miljö för redundans för att säkerställa optimal tillgänglighet för appar och minimerad återställnings tid om en katastrof inträffar.

Vi rekommenderar att du konfigurerar affärskontinuitet och haveriberedskap (BCDR) i regionala par för att dra nytta av Azures isolerings- och tillgänglighetsprinciper. Mer information finns i [Azure-kopplade regioner](../../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration. Om du ännu inte har en Azure-prenumeration kan du registrera dig för en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Redundansväxla till ett sekundärt konto

Om du vill implementera BCDR måste du ha två Video Indexer-konton för att kunna hantera redundans.

1. Skapa två Video Indexer konton som är anslutna till Azure (se [skapa ett video Indexer konto](connect-to-azure.md)). Skapa ett konto för din primära region och det andra till den kopplade Azure-regionen.
1. Om det uppstår ett problem i din primära region växlar du till indexering med det sekundära kontot.

> [!TIP]
> Du kan automatisera BCDR genom att ställa in aktivitets logg aviseringar för meddelanden om tjänst hälsa enligt [skapa aktivitets logg aviseringar för tjänst meddelanden](../../service-health/alerts-activity-log-service-notifications-portal.md).

Information om hur du använder flera klienter finns i [Hantera flera klienter](manage-multiple-tenants.md). Om du vill implementera BCDR väljer du något av följande två alternativ: [video Indexer konto per klient](manage-multiple-tenants.md#video-indexer-account-per-tenant) eller [Azure-prenumeration per klient](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Nästa steg

[Hantera ett video Indexer-konto som är anslutet till Azure](manage-account-connected-to-azure.md).
