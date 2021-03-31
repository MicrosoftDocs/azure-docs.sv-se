---
title: Hantera Azure-API Management med Azure Automation
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure-API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: c808d4659b5987b099dd96d73bb8c18c08fe3c99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86249403"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Hantera Azure API Management med hjälp av Azure Automation
Den här guiden beskriver hur du Azure Automation tjänsten och hur den kan användas för att förenkla hanteringen av Azure API Management.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla moln hantering genom process automatisering. Att använda Azure Automation, manuella, återkommande, långvariga och fel känsliga uppgifter kan automatiseras för att öka tillförlitligheten, effektiviteten och tiden för din organisation.

Azure Automation ger en mycket tillförlitlig motor för körning av arbets flöden med hög tillgänglighet som kan skalas efter dina behov. I Azure Automation kan processer startas manuellt, av system från tredje part eller med schemalagda intervall så att aktiviteterna sker exakt vid behov.

Minska drifts kostnader och frigör IT-avdelningen och DevOps personal för att fokusera på arbete som lägger till ett affärs värde genom att flytta dina moln hanterings uppgifter så att de körs automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hur kan Azure Automation hjälpa till att hantera Azure API Management?
API Management kan hanteras i Azure Automation med hjälp av [Windows PowerShell-cmdlets för Azure API Management API](/powershell/module/az.apimanagement). I Azure Automation kan du skriva PowerShell Workflow-skript för att utföra många av dina API Management uppgifter med hjälp av cmdletarna. Du kan också para ihop dessa cmdlets i Azure Automation med cmdletar för andra Azure-tjänster, för att automatisera komplexa aktiviteter i Azure-tjänster och system från tredje part.

Här följer några exempel på hur du använder API Management med PowerShell:

* [Azure PowerShell-exempel för API Management](./powershell-samples.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Azure Automation och hur det kan användas för att hantera Azure API Management, kan du följa dessa länkar om du vill veta mer.

* Se [självstudierna Azure Automation kom igång](../automation/learn/automation-tutorial-runbook-graphical.md).
