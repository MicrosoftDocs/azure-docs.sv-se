---
title: InvalidNetworkSecurityGroupSecurityRules-fel – Azure HDInsight
description: Det går inte att skapa kluster med felkoden InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 7e0b984b3ec4a203f8a1118c0e6a166c5a9e1125
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944377"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules-kluster kan inte skapas i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får en felkod `InvalidNetworkSecurityGroupSecurityRules` med en beskrivning som liknar "säkerhets reglerna i den nätverks säkerhets grupp som kon figurer ATS med undernät tillåter inte obligatorisk inkommande och/eller utgående anslutning."

## <a name="cause"></a>Orsak

Troligen ett problem med de regler för inkommande [nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) som kon figurer ATS för klustret.

## <a name="resolution"></a>Lösning

Gå till Azure Portal och identifiera den NSG som är associerad med under nätet där klustret distribueras. I avsnittet **inkommande säkerhets regler** kontrollerar du att reglerna tillåter inkommande åtkomst till port 443 för de IP-adresser som anges [här](../control-network-traffic.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]