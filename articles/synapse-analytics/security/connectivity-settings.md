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
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473095"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Anslutnings inställningar för Azure Synapse Analytics

I den här artikeln förklaras anslutnings inställningarna i Azure Synapse Analytics och hur du konfigurerar dem i tillämpliga fall.


## <a name="connection-policy"></a>Anslutnings princip
Anslutnings principen för Synapse SQL i Azure Synapse Analytics är inställd på *default*. Du kan inte ändra detta i Azure Synapse Analytics. Du kan lära dig mer om hur det påverkar anslutningar till Synapse SQL i Azure Synapse Analytics [här](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy). 

## <a name="minimal-tls-version"></a>Lägsta TLS-version
Synapse SQL i Azure Synapse Analytics tillåter anslutningar med alla TLS-versioner. Du kan inte ange den lägsta TLS-versionen för Synapse SQL i Azure Synapse Analytics.

## <a name="next-steps"></a>Nästa steg

Skapa en [Azure DataSynapses-arbetsyta](./synapse-workspace-ip-firewall.md)
