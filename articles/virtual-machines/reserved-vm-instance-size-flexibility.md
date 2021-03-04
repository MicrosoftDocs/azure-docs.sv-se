---
title: Storleks flexibilitet för virtuella datorer – Azure Reserved VM Instances
description: Lär dig vilken storleks serie en reservations rabatt gäller när du använder en reserverad VM-instans.
author: yashesvi
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/01/2021
ms.author: yashar
ms.openlocfilehash: 9270b7fb135eddd78a7aeb30cf88af4d704e176e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030716"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibel storlek för virtuella datorer med reserverade VM-instanser

När du köper en reserverad VM-instans kan du välja att optimera storleks flexibiliteten för instans storleken eller kapacitets prioriteten. Mer information om hur du ställer in eller ändrar optimerings inställningen för reserverade VM-instanser finns i [ändra optimerings inställningen för reserverade VM-instanser](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Med en reserverad virtuell dator instans som är optimerad för flexibilitet i instans storleken kan den reservation du köper tillämpa på storlekarna för virtuella datorer (VM) i samma instans storlek flexibilitet grupp. Om du till exempel köper en reservation för en VM-storlek som anges i DSv2-serien, t. ex. Standard_DS3_v2, kan reservations rabatten gälla för de andra storlekarna som anges i samma instans storlek flexibilitet grupp:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Men reservations rabatten gäller inte för VM-storlekar som listas i olika storleks flexibla grupper, t. ex. SKU: er i DSv2-seriens höga minne: Standard_DS11_v2, Standard_DS12_v2 och så vidare.

I flexibilitets gruppen för instans storlek gäller antalet virtuella datorer som reservations rabatten gäller beroende på storleken på den virtuella dator som du väljer när du köper en reservation. Det beror också på storleken på de virtuella datorer som du kör. I kolumnen förhållande jämförs de relativa utrymmena för varje VM-storlek i den instans storlekens flexibilitet grupp. Använd ratio-värdet för att beräkna hur reservations rabatten gäller för de virtuella datorer som du kör.

## <a name="examples"></a>Exempel

I följande exempel används storlekarna och förhållandena i DSv2-seriens tabell.

Du köper en reserverad VM-instans med storleken Standard_DS4_v2 där förhållandet eller det relativa värdet jämfört med de andra storlekarna i serien är 8.

- Scenario 1: kör åtta Standard_DS1_v2 storlekar av virtuella datorer med förhållandet 1. Reservations rabatten gäller för alla åtta av de virtuella datorerna.
- Scenario 2: kör två Standard_DS2_v2 stora virtuella datorer med förhållandet 2 var. Kör också en virtuell dator med Standard_DS3_v2 storlek med förhållandet 4. Det totala utrymmet är 2 + 2 + 4 = 8. Reservations rabatten gäller för alla tre virtuella datorer.
- Scenario 3: kör ett Standard_DS5_v2 med förhållandet 16. Reservations rabatten gäller för hälften av den virtuella datorns beräknings kostnad.

I följande avsnitt visas vilka storlekar som finns i samma storleks serie grupp när du köper en reserverad VM-instans som är optimerad för storleks flexibilitet i en instans.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Flexibilitet för instans storlek för virtuella datorer 

CSV nedan har grupper som är flexibla för instans storlek, ArmSkuName och förhållandet.  

[Flexibilitet för instans storlek](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Azure håller länken och schemat uppdaterat så att du kan använda filen program mässigt.

## <a name="view-vm-size-recommendations"></a>Visa rekommendationer för VM-storlek

Azure visar rekommendationer för VM-storlek i inköps upplevelsen. Om du vill visa de minsta storleks rekommendationerna väljer du **Gruppera efter minsta storlek**.

:::image type="content" source="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" alt-text="Skärm bild som visar rekommenderade kvantiteter." lightbox="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" :::

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Vad är Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md).
