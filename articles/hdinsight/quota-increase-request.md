---
title: CPU Core-kvot, öka förfrågan – Azure HDInsight
description: Lär dig processen att begära en ökning av de processor kärnor som allokerats till din prenumeration.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: b62e41f280d02664b3df631c3413960f1265356f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863181"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Begäran om kvot ökar för Azure HDInsight

CPU Core-kvoter hjälper till att se till att resursanvändningen distribueras relativt mellan alla kunder i en viss Azure-region. Men i vissa fall kan ditt företags krav kräva fler kluster resurser än den aktuella kvoten kommer att tillåta. I sådana fall kan du begära en ökning av CPU Core-kvoten så att du kan distribuera kluster som matchar dina krav på data bearbetning.

När du når en kvot gräns kan du inte distribuera nya kluster eller skala ut befintliga kluster genom att lägga till fler arbetsnoder. Den enda kvot gränsen är den processor kärnors kvot som finns på region nivå för varje prenumeration. Din prenumeration kan till exempel ha en 30-PROCESSORs kärn gräns i regionen USA, östra, med ytterligare 30 CPU-kärnor som tillåts i USA, östra.

## <a name="gather-required-information"></a>Samla in nödvändig information

Om du har fått ett fel som indikerar att du har nått en kvot gräns kan du använda processen som beskrivs i det här avsnittet för att samla in viktig information och skicka en begäran om kvot höjning.

1. Fastställ önskad storlek, skalning och typ för klustrets virtuella dator.
1. Kontrol lera den aktuella kvot kapacitets gränsen för din prenumeration. Gör så här för att kontrol lera dina tillgängliga kärnor:

    1. Logga in på [Azure-portalen](https://portal.azure.com/).
    1. Gå till **översikts** sidan för HDInsight-klustret.
    1. På den vänstra menyn väljer du **kvot gränser**. Sidan visar det antal kärnor som används, antalet tillgängliga kärnor samt totalt antal kärnor.

Utför följande steg för att begära en kvot ökning:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Hjälp + Support** längst ned till vänster på sidan.

    :::image type="content" source="./media/quota-increase-request/help-support-button.png" alt-text="hjälp och support-knappen" border="true":::

1. Välj **Ny supportbegäran**.
1. På sidan **Ny supportbegäran** går du till fliken **Grunder** och väljer följande alternativ:

   - **Typ av problem**: **begränsningar för tjänsten och prenumerationen (kvoter)**
   - **Prenumeration**: den prenumeration du vill ändra
   - **Typ av kvot**: **HDInsight**

     :::image type="content" source="./media/quota-increase-request/hdinsight-quota-support-request.png" alt-text="Skapa en support förfrågan för att öka HDInsight Core-kvoten" border="true":::

1. Välj **Nästa: lösningar >>**.
1. På sidan **information** anger du en beskrivning av problemet, väljer allvarlighets graden för problemet, önskad kontakt metod och andra obligatoriska fält. Använd mallen som anges nedan för att säkerställa att du anger nödvändig information. Kvot öknings begär Anden utvärderas av Azures kapacitets team och inte av produkt teamet för HDInsight. Den mer fullständiga informationen som du anger, desto mer sannolik kommer din begäran att godkännas.

   ```text
   I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
   
   My current quota on this subscription is [CURRENT QUOTA AMOUNT].
   
   I would like to use the extra cores for [DETAIL REASON].
   ```

   :::image type="content" source="./media/quota-increase-request/problem-details.png" alt-text="problem information" border="true":::

1. Välj **Nästa: granska + skapa >>**.
1. På fliken **Granska och skapa** väljer du **skapa**.

> [!NOTE]  
> Om du behöver öka HDInsight Core-kvoten i en privat region [skickar du en godkänd list förfrågan](https://aka.ms/canaryintwhitelist).

Du kan [kontakta supporten för att begära en kvot ökning](../azure-portal/supportability/resource-manager-core-quotas-request.md).

Det finns vissa fasta kvot gränser. En enda Azure-prenumeration kan till exempel ha högst 10 000 kärnor. Mer information om dessa begränsningar finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera kluster i HDInsight med Apache Hadoop, Spark, Kafka och mycket mer](hdinsight-hadoop-provision-linux-clusters.md): Lär dig hur du konfigurerar och konfigurerar kluster i HDInsight.
* [Övervaka kluster prestanda](hdinsight-key-scenarios-to-monitor.md): Lär dig mer om viktiga scenarier för att övervaka ditt HDInsight-kluster som kan påverka klustrets kapacitet.