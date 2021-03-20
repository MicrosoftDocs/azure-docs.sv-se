---
title: Kör SQL Server VM på en dedikerad Azure-värd
description: Lär dig hur du kör en SQL Server VM på en dedikerad Azure-värd.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 957e458bf21eb4a95b1faba1daf0459271648310
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358698"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Kör SQL Server VM på en dedikerad Azure-värd 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller information om hur du använder en SQL Server virtuell dator (VM) med en [dedikerad Azure-värd](../../../virtual-machines/dedicated-hosts.md). Ytterligare information om den dedikerade Azure-värden finns i blogg inlägget [Introduktion till Azure-dedikerad värd](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Översikt
Den [dedikerade Azure-värden](../../../virtual-machines/dedicated-hosts.md) är en tjänst som tillhandahåller fysiska servrar som kan vara värd för en eller flera virtuella datorer som är dedikerade till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i Microsofts Data Center, som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighets zon och fel domän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som passar bäst för dina behov.

## <a name="limitations"></a>Begränsningar

- Alla VM-serier stöds inte på dedikerade värdar och tillgänglighet för VM-serien varierar beroende på region. Mer information finns i [Översikt över Azure-dedikerade värdar](../../../virtual-machines/dedicated-hosts.md).

## <a name="licensing"></a>Licensiering

Du kan välja mellan två olika licensierings alternativ när du placerar SQL Server VM på en dedikerad Azure-värd. 

  - **SQL VM-licensiering**: Detta är det befintliga licensierings alternativet, där du betalar för varje SQL Server VM-licens individuellt. 
  - **Dedikerad värd licensiering**: den nya licensierings modellen som är tillgänglig för den dedikerade Azure-värden, där SQL Server licenser paketeras och betalas för på värdnivå. 


Alternativ på värdnivå för att använda befintliga SQL Server-licenser: 
  - SQL Server Enterprise Edition Azure Hybrid-förmån (AHB)
    - Tillgängligt för kunder med SA eller prenumeration.
    - Licensiera alla tillgängliga fysiska kärnor och njut av obegränsad virtualisering (upp till max virtuella processorer som stöds av värden).
        - Mer information om hur du tillämpar AHB på den dedikerade Azure-värden finns i [Azure Hybrid-förmån vanliga frågor och svar](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - SQL Server licenser som förvärv ATS före 1 oktober
      - SQL Server Enterprise Edition har licens alternativen värd och per virtuell dator. 
      - SQL Server Standard versionen har bara en licens för alternativet per virtuell dator tillgänglig. 
          - Mer information finns i [Microsofts produkt villkor](https://www.microsoft.com/licensing/product-licensing/products). 
  - Om inget SQL Server dedikerat alternativ på värdnivå är markerat kan du välja SQL Server AHB på nivån för enskilda virtuella datorer, precis som du skulle göra med virtuella datorer med flera innehavare.



## <a name="provisioning"></a>Etablering  
Att tillhandahålla en SQL Server VM till den dedikerade värden är inte något annat än någon annan virtuell Azure-dator. Du kan göra det med hjälp av [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), [Azure Portal](../../../virtual-machines/dedicated-hosts-portal.md)och [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md).

Processen för att lägga till en befintlig SQL Server VM på den dedikerade värden kräver stillestånds tid, men påverkar inte data och kommer inte att ha någon data förlust. Alla databaser, inklusive system databaser, bör dock säkerhets kopie ras före flytten.

## <a name="virtualization"></a>Virtualisering 

En av fördelarna med en dedikerad värd är obegränsad virtualisering. Du kan t. ex. ha licenser för 64 virtuella kärnor, men du kan konfigurera värden så att den har 128 virtuella kärnor, så du får dubbla virtuella kärnor men bara betala hälften av vad du vill för SQL Server licenser. 

Eftersom eftersom det är din värd, är du berättigad att ställa in virtualiseringen med 1:2-förhållandet. 

## <a name="faq"></a>Vanliga frågor

**F: Hur fungerar Azure Hybrid-förmån för Windows Server/SQL Server-licenser på en särskild Azure-värd?**

A: kunderna kan använda värdet för sin befintliga Windows Server och SQL Server licenser med Software Assurance, eller kvalificera prenumerations licenser, för att betala en reducerad taxa på den Azure-dedikerade värd som använder Azure Hybrid-förmån. Kunder med Windows Server Data Center och SQL Server Enterprise Edition får obegränsad virtualisering (distribuera så många virtuella Windows Server-datorer som möjligt på värden som är beroende av den underliggande serverns fysiska kapacitet) när de licensierar hela värden och använder Azure Hybrid-förmån.  Alla Windows Server-och SQL Server-arbetsbelastningar i den dedikerade Azure-värden är också kvalificerade för utökade säkerhets uppdateringar för Windows Server och SQL Server 2008/R2 utan extra kostnad. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](doc-changes-updates-release-notes.md)