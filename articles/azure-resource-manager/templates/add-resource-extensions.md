---
title: Konfiguration efter distribution med hjälp av tillägg
description: Lär dig hur du använder tillägg för Azure Resource Manager mall (ARM-mall) för att tillhandahålla konfigurationer efter distribution.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: 23d5a9aaa546542218a6f839ab415184ff309928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934330"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Ange konfigurationer efter distribution med hjälp av tillägg

Azure Resource Manager mall (ARM-mall) tillägg är små program som tillhandahåller konfiguration och automatiserings uppgifter efter distributionen på Azure-resurser. Det mest populära är att tillägg för virtuella datorer. Se [tillägg och funktioner för virtuella datorer för Windows](../../virtual-machines/extensions/features-windows.md)och [tillägg och funktioner för virtuella datorer för Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Tillägg

De befintliga tilläggen är:

- [Microsoft. Compute/virtualMachines/tillägg](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. Compute virtualMachineScaleSets/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft. HDInsight-kluster/-tillägg](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft. SQL-servrar/-databaser/-tillägg](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)
- [Microsoft. Web/Sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Om du vill ta reda på vilka tillägg som är tillgängliga bläddrar du till [mal len referens](/azure/templates/). I **Filtrera efter rubrik**, ange **tillägg**.

Information om hur du använder dessa tillägg finns i:

- [Självstudie: distribuera tillägg för virtuella datorer med ARM-mallar](template-tutorial-deploy-vm-extensions.md).
- [Självstudie: importera SQL BACPAC-filer med ARM-mallar](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: distribuera tillägg för virtuella datorer med ARM-mallar](template-tutorial-deploy-vm-extensions.md)
