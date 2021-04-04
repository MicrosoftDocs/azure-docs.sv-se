---
title: Flytta Azure Network Watcher-resurser | Microsoft Docs
description: Flytta Azure Network Watcher resurser mellan regioner
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 4853f485e4424c3c3263a18d27834d0f9ae94918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019660"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Flytta Azure Network Watcher resurser mellan regioner

## <a name="moving-the-network-watcher-resource"></a>Flytta Network Watcher resursen
Network Watcher resursen representerar backend-tjänsten för Network Watcher och hanteras helt av Azure. Kunderna behöver inte hantera den. Flytt åtgärden stöds inte på den här resursen.

## <a name="moving-child-resources-of-network-watcher"></a>Flytta underordnade resurser för Network Watcher
Det finns för närvarande inte stöd för att flytta resurser mellan regioner för en underordnad resurs av `*networkWatcher*` resurs typen.

## <a name="next-steps"></a>Nästa steg
* Läs [Network Watcher översikt](./network-watcher-monitoring-overview.md)
* Se [vanliga frågor och svar om Network Watcher](./frequently-asked-questions.md)