---
title: Hantera Azure Cloud Services (klassisk) med hjälp av Azure Automation | Microsoft Docs
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure Cloud Services i stor skala.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743499"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Hantera Azure Cloud Services (klassisk) med Azure Automation

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).
I den här guiden får du lära dig Azure Automation tjänsten och hur den kan användas för att förenkla hanteringen av dina Azure Cloud Services.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla moln hantering genom process automatisering. Att använda Azure Automation, långvariga, manuella, fel känsliga och ofta återkommande uppgifter kan automatiseras för att öka tillförlitligheten, effektiviteten och tiden för din organisation.

Azure Automation ger en mycket tillförlitlig och hög tillgänglig arbets flödes körnings motor som kan skalas för att uppfylla dina behov när din organisation växer. I Azure Automation kan processer startas manuellt, av system från tredje part eller med schemalagda intervall så att aktiviteterna sker exakt vid behov.

Sänk drifts kostnader och frigör IT/DevOps-personalen och fokusera på arbete som lägger till affärs värde genom att flytta dina moln hanterings uppgifter så att de körs automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hur kan Azure Automation hjälpa till att hantera Azure Cloud Services?
Azure Cloud Services kan hanteras i Azure Automation med hjälp av PowerShell-cmdletar som är tillgängliga i [Azure PowerShell-verktygen](/powershell/). Azure Automation har följande PowerShell-cmdlets för moln tjänster tillgängliga i rutan så att du kan utföra alla dina moln tjänst hanterings uppgifter i tjänsten. Du kan också para ihop dessa cmdlets i Azure Automation med cmdletar för andra Azure-tjänster, för att automatisera komplexa aktiviteter i Azure-tjänster och system från tredje part.

Några exempel på användning av Azure Automation för att hantera Azure Cloud Services är:

* [Kontinuerlig distribution av en moln tjänst när cscfg eller cspkg uppdateras i Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Starta om moln tjänst instanser parallellt, en uppgraderings domän i taget](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Azure Automation och hur det kan användas för att hantera Azure Cloud Services, kan du följa dessa länkar om du vill veta mer om Azure Automation.

* [Översikt över Azure Automation](../automation/automation-intro.md)
* [Min första Runbook](../automation/learn/automation-tutorial-runbook-graphical.md)