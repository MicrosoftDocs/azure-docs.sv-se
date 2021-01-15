---
title: Stöd för 32-bitars operativ system på virtuella Azure-datorer | Microsoft Docs
description: Information om operativ system som stöds på virtuella Azure-datorer
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210196"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Stöd för 32-bitars operativsystem i Azure Virtual Machines

Med Microsoft Azure kan du nu ta med sina 32-bitars Windows-operativsystem till Azure. Endast specialiserade virtuella hård diskar stöds och generaliserade avbildningar fungerar inte i Azure. Eftersom vissa av dessa operativ system redan har nått sitt slut för liv support avtal kan Microsoft inte erbjuda ytterligare support för dem. Support erbjuds inte heller för Linux-baserade operativ system som körs på en Microsoft Azure virtuell dator (VM).

> [!NOTE]
> Azure-plattformen har en begränsning för minnes adress utrymme på virtuella datorer som kör 32-bitars operativ system där endast 1 GB minne kan göras tillgängligt för den virtuella datorn (*särskilt på klient-SKU: er som Win7 eller Win10*) och resten av minnet för den virtuella datorn visas som reserverad i den virtuella gäst datorn. Detta är ett känt problem och vi har för närvarande ingen ETA för en åtgärd. Vi rekommenderar att du flyttar till 64-bitars versioner av operativ systemet.
> 

## <a name="more-information"></a>Mer information

Mer information om operativ system som stöds på virtuella Azure-datorer finns i följande artiklar i Microsoft Knowledge Base:

* [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Stöd för Linux och teknik med öppen källkod i Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Referenser

* [Läs mer om kostnads fria utökade säkerhets uppdateringar för Windows Server 2008/R2 i Azure](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Läs mer om stöd för Windows Server 2008 SP2 32-bitars specialiserade avbildningar i Azure](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Läs mer om stöd för migrering av Windows Server 2008-avbildningar till Azure med hjälp av Azure Site Recovery](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Lär dig mer om operativ system som stöds av Azure Extensions](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Läs mer om att köra Windows Server 2003 på Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/).

Du kan också fil en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
