---
title: Felsök vanliga distributionsfel
titleSuffix: Azure Load Balancer
description: Beskriver hur du löser vanliga fel när du distribuerar Azure Load Balancer
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94695562"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Felsök vanliga problem med Azure-distribution med Azure Load Balancer

I den här artikeln beskrivs några vanliga Azure Load Balancer distributions fel och information om hur du kan lösa felen. Om du letar efter information om en felkod och informationen inte finns i den här artikeln kan du berätta för oss. Längst ned på den här sidan kan du lämna feedback. Feedbacken spåras med GitHub-problem.

## <a name="error-codes"></a>Felkoder

| Felkod | Information och minskning |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Både offentlig IP SKU och Load Balancer SKU måste matcha. Se till att Azure Load Balancer och offentliga IP-SKU: er matchar. Standard-SKU rekommenderas för produktions arbets belastningar. Läs mer om [skillnaderna i SKU: er](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Skalnings uppsättningar för virtuella datorer är standard till grundläggande belastningsutjämnare när SKU: n har angetts eller distribuerats utan offentliga standard-IP-adresser. Distribuera om skalnings uppsättningen för virtuella datorer med standard offentliga IP: er på de enskilda instanserna för att säkerställa att Standard Load Balancer är markerat eller Välj en standard LB när du distribuerar den virtuella datorns skalnings uppsättning från Azure Portal. |
|MaxAvailabilitySetsInLoadBalancerReached | Backend-poolen för en Load Balancer kan innehålla högst 150 tillgänglighets uppsättningar. Om du inte har de tillgänglighets uppsättningar som uttryckligen definierats för dina virtuella datorer i backend-poolen, hamnar varje enskild virtuell dator i sin egen tillgänglighets uppsättning. Därför skulle distribution av 150 fristående virtuella datorer innebära att den skulle ha 150 tillgänglighets uppsättningar, så att gränsen uppnåddes. Du kan distribuera en tillgänglighets uppsättning och lägga till ytterligare virtuella datorer i den som en lösning. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | För Basic SKU-belastningsutjämnare måste nätverks gränssnittet och belastningsutjämnaren finnas i samma tillgänglighets uppsättning. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Du kan inte ha mer än en regel för en angiven belastningsutjämnare (intern, offentlig) med samma server dels port och protokoll som refereras till av samma skalnings uppsättning för den virtuella datorn. Uppdatera regeln om du vill ändra den här regeln för att skapa dubbletter. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Du kan inte ha mer än en regel för en angiven belastningsutjämnare (intern, offentlig) med samma server dels port och protokoll som refereras till av samma skalnings uppsättning för den virtuella datorn. Uppdatera dina regel parametrar om du vill ändra den här dubbletten av regeln. |
|AnotherInternalLoadBalancerExists| Du kan bara ha en Load Balancer av typen intern referens till samma uppsättning virtuella datorer/nätverks gränssnitt i Server delen för Load Balancer. Uppdatera din distribution så att du bara skapar en Load Balancer av samma typ. |
|CannotUseInactiveHealthProbe| Du kan inte ha en avsökning som inte används av någon regel som kon figurer ATS för skalnings uppsättnings hälsa för virtuella datorer. Se till att avsökningen som har kon figurer ATS används aktivt. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Du kan inte ha flera belastningsutjämnare av samma typ (intern, offentlig). Du kan ha högst en intern och en offentlig Load Balancer. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Basic-Load Balancer stöds inte för grupper av virtuella datorer med flera placerings grupper eller skalnings uppsättning för virtuella datorer i olika zoner. Använd Standard Load Balancer i stället. |
|MarketplacePurchaseEligibilityFailed | Växla till rätt administratörs konto för att aktivera köp på grund av att prenumerationen är en EA-prenumeration. Mer information finns [här](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase). |
|ResourceDeploymentFailure| Om lastbalanseraren har ett feltillstånd följer du de här stegen för att återställa den från felläget:<ol><li>Gå till https://resources.azure.com och logga in med dina Azure Portal autentiseringsuppgifter.</li><li>Välj **Läsa/skriva**.</li><li>Expandera **Prenumerationer** till vänster och expandera sedan prenumerationen med den lastbalanserare som behöver uppdateras.</li><li>Expandera **ResourceGroups** och expandera sedan resursgruppen med den lastbalanserare som behöver uppdateras.</li><li>Välj **Microsoft. Network**  >  **belastningsutjämnare** och välj sedan Load Balancer att uppdatera **LoadBalancer_1**.</li><li>På visnings sidan för **LoadBalancer_1** väljer du **Hämta**  >  **redigering**.</li><li>Det **gick inte** att uppdatera **ProvisioningState** - **värdet.**</li><li>Välj **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Nästa steg

* Titta igenom [jämförelse tabellen](./skus.md) för Azure Load Balancer SKU
* Läs mer om [Azure Load Balancer gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)