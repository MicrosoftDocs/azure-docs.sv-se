---
title: Infrastruktur-FQDN för Azure-brandvägg
description: Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74168970"
---
# <a name="infrastructure-fqdns"></a>Infrastruktur-FQDN

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. 

Följande tjänster ingår i den inbyggda regel samlingen:

- Beräkna åtkomst till lagrings plattformens avbildnings lagring (PIR)
- Managed disks status för lagrings åtkomst
- Azure-diagnostik och loggning (MDS)

## <a name="overriding"></a>Åsidosätta 

Du kan åsidosätta den här inbyggda infrastruktur regel samlingen genom att skapa en neka all program regel samling som bearbetas sist. Den bearbetas alltid före regelsamlingen för infrastrukturen. Allt som inte ingår i regelsamlingen för infrastrukturen nekas som standard.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).