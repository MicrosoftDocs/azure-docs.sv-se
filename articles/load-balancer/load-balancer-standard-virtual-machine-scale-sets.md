---
title: Lägga till regler för Azure Standard Load Balancer och skalnings uppsättningar för virtuella datorer
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Med den här utbildnings vägen kommer du igång med Azure Standard Load Balancer och skalnings uppsättningar för virtuella datorer.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592287"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Lägga till regler för Azure Load Balancer med skalnings uppsättningar för virtuella datorer

Tänk på följande rikt linjer när du arbetar med skalnings uppsättningar för virtuella datorer och Azure Load Balancer:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Port vidarebefordring och inkommande NAT-regler

När skalnings uppsättningen har skapats kan backend-porten inte ändras för en belastnings Utjämnings regel som används av en hälso avsökning av belastningsutjämnaren. Om du vill ändra porten tar du bort hälso avsökningen genom att uppdatera skalnings uppsättningen för den virtuella datorn och uppdatera porten. Konfigurera sedan hälso avsökningen igen.

När du använder skalnings uppsättningen för den virtuella datorn i belastningsutjämnaren i belastningsutjämnaren skapas standard reglerna för inkommande NAT automatiskt.
  
## <a name="inbound-nat-pool"></a>Inkommande NAT-pool

Varje skalnings uppsättning för virtuella datorer måste ha minst en inkommande NAT-pool. En inkommande NAT-pool är en samling inkommande NAT-regler. En inkommande NAT-pool kan inte stödja flera skalnings uppsättningar för virtuella datorer.

## <a name="load-balancing-rules"></a>Belastnings Utjämnings regler

När du använder skalnings uppsättningen för den virtuella datorn i belastningsutjämnaren, skapas standard regeln för belastnings utjämning automatiskt.
  
## <a name="outbound-rules"></a>Regler för utgående trafik

Om du vill skapa en utgående regel för en backend-pool som redan refereras till av en belastnings Utjämnings regel väljer du **Nej** under **skapa implicit utgående regler** i Azure Portal när den inkommande belastnings Utjämnings regeln skapas.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Skärm bild som visar skapande av belastnings Utjämnings regel." border="true":::

Använd följande metoder för att distribuera en skalnings uppsättning för virtuella datorer med en befintlig instans av Load Balancer:

* [Konfigurera en skalnings uppsättning för virtuell dator med en befintlig instans av Azure Load Balancer med hjälp av Azure Portal](./configure-vm-scale-set-portal.md)
* [Konfigurera en skalnings uppsättning för virtuell dator med en befintlig instans av Azure Load Balancer med hjälp av Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Konfigurera en skalnings uppsättning för virtuella datorer med en befintlig instans av Azure Load Balancer med hjälp av Azure CLI](./configure-vm-scale-set-cli.md)
* [Uppdatera eller ta bort en befintlig instans av Azure Load Balancer som används av en skalnings uppsättning för virtuell dator](./update-load-balancer-with-vm-scale-set.md)
