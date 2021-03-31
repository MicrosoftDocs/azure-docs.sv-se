---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84317805"
---
### <a name="database-tier"></a>Databas nivå

Databas nivån innehåller databas instanserna för programmet. Databasen kan vara antingen en Oracle DB, Oracle RAC eller Oracle Exadata Database system. 

Om du väljer att använda Oracle DB kan databas instansen distribueras på Azure via Oracle DB avbildningar som är tillgängliga på Azure Marketplace. Alternativt kan du använda kopplingen mellan Azure och OCI för att distribuera Oracle DB i en PaaS-modell på OCI.

För Oracle RAC kan du använda OCI i PaaS-modellen. Vi rekommenderar att du använder ett RAC-system med två noder. Det går att distribuera Oracle RAC på Azure-CloudSimple i IaaS-modellen, men det är inte en konfiguration som stöds av Oracle. Se Oracle-program som är [berättigade till godkända moln miljöer](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf).

För Exadata-system använder du slutligen OCI-sammanlänkning och distribuerar Exadata-systemet i OCI. Föregående arkitektur diagram ovan visar ett Exadata-system som distribueras i OCI över två undernät.

För produktions scenarier distribuerar du flera instanser av databasen över två tillgänglighets zoner (om du distribuerar i Azure) eller två tillgänglighets domäner (i OCI). Använd Oracle Active data Guard för att synkronisera primära databaser och standby-databaser.

Databas nivån tar bara emot begär Anden från mellan nivån. Vi rekommenderar att du konfigurerar en nätverks säkerhets grupp (säkerhets lista om distributionen av databasen i OCI) till att endast tillåta begär Anden på port 1521 från mellan nivån och port 22 från skydds-servern av administrativa skäl.

För databaser som distribueras i OCI måste ett separat virtuellt moln nätverk konfigureras med en DRG (Dynamic routing Gateway) som är ansluten till din FastConnect-krets.