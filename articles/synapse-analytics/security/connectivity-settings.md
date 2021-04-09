---
title: Inställningar för Azure Synapse-anslutning
description: En artikel som lär dig att konfigurera anslutnings inställningar i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587941"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Anslutnings inställningar för Azure Synapse Analytics

I den här artikeln förklaras anslutnings inställningarna i Azure Synapse Analytics och hur du konfigurerar dem i tillämpliga fall.


## <a name="connection-policy"></a>Anslutnings princip
Anslutnings principen för Synapse SQL i Azure Synapse Analytics är inställd på *default*. Du kan inte ändra detta i Azure Synapse Analytics. Du kan lära dig mer om hur det påverkar anslutningar till Synapse SQL i Azure Synapse Analytics [här](../../azure-sql/database/connectivity-architecture.md#connection-policy). 

## <a name="minimal-tls-version"></a>Lägsta TLS-version
Synapse SQL i Azure Synapse Analytics tillåter anslutningar med alla TLS-versioner. Du kan inte ange den lägsta TLS-versionen för Synapse SQL i Azure Synapse Analytics.

## <a name="next-steps"></a>Nästa steg

Skapa en [Azure DataSynapses-arbetsyta](./synapse-workspace-ip-firewall.md)