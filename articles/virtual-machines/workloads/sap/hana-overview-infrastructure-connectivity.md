---
title: Infrastruktur och anslutning till SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Konfigurera nödvändig anslutnings infrastruktur för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27dc2f6c647308984a02f2cf52dbc0213abad088
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676835"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (stora instanser) distribution 

Den här artikeln förutsätter att du har slutfört köpet av SAP HANA på Azure (stora instanser) från Microsoft. Innan du läser den här artikeln kan du läsa mer i [Hana stora instanser vanliga termer](hana-know-terms.md) och [Hana stora instanser](hana-available-skus.md)i den allmänna bakgrunden.


Microsoft kräver följande information för att distribuera HANA-stora instans enheter:

- Kund namn.
- Företags kontakt information (inklusive e-postadress och telefonnummer).
- Teknisk kontakt information (inklusive e-postadress och telefonnummer).
- Teknisk nätverks kontakt information (inklusive e-postadress och telefonnummer).
- Azure-distributions region (till exempel västra USA, östra Australien eller Nord Europa).
- SAP HANA på Azure (stora instanser) SKU (konfiguration).
- För varje Azure-distributions region:
    - Ett/29 IP-adressintervall för ER-P2P-anslutningar som ansluter virtuella Azure-nätverk till HANA-stora instanser.
    - Ett/24 CIDR-block som används för IP-adresspoolen HANA Large instances Server.
    - Valfritt när du använder [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) för att aktivera dirigerad routning från lokal plats till Hana stora instans enheter eller routning mellan Hana-stora instans enheter i olika Azure-regioner, måste du reservera ett annat/29 IP-adressintervall. Detta specifika intervall får inte överlappa något av de andra IP-adressintervall som du definierade tidigare.
- De IP-adressintervall som används i det virtuella nätverkets adress utrymme-attribut för varje virtuellt Azure-nätverk som ansluter till de stora HANA-instanserna.
- Data för varje HANA-stor instans system:
  - Önskat värdnamn, helst med ett fullständigt kvalificerat domän namn.
  - Önskad IP-adress för den stora volymen HANA av en stor instans av serverns IP-adresspool. (De första 30 IP-adresserna i Server intervallet för IP-adresspoolen är reserverade för intern användning i HANA stora instanser.)
  - SAP HANA SID-namn för SAP HANA-instansen (krävs för att skapa nödvändiga SAP HANA-relaterade disk volymer). Microsoft behöver det HANA-SID som krävs för att skapa behörigheter för sidadm på NFS-volymerna. Dessa volymer ansluter till den stora volymen HANA. HANA SID används också som en av namn komponenterna för de disk volymer som monteras. Om du vill köra fler än en HANA-instans på enheten bör du lista flera HANA-sid. Var och en får en separat uppsättning tilldelade volymer.
  - I Linux OS har sidadm-användaren ett grupp-ID. Detta ID krävs för att skapa nödvändiga SAP HANA-relaterade disk volymer. SAP HANA-installationen skapar vanligt vis gruppen sapsys med grupp-ID 1001. Sidadm-användaren är en del av den gruppen.
  - I Linux OS har sidadm-användaren ett användar-ID. Detta ID krävs för att skapa nödvändiga SAP HANA-relaterade disk volymer. Om du kör flera HANA-instanser på enheten ska du Visa en lista över alla sidadm-användare. 
- ID för Azure-prenumerationen för den Azure-prenumeration som SAP HANA på Azure HANA stora instanser kommer att vara direkt anslutna. Det här prenumerations-ID: t refererar till Azure-prenumerationen, som kommer att debiteras med en stor instans enhet eller enheter i HANA.

När du har angett föregående information, etablerar Microsoft SAP HANA på Azure (stora instanser). Microsoft skickar information till att länka dina virtuella Azure-nätverk till HANA-stora instanser. Du kan också komma åt de stora instans enheterna i HANA.

Använd följande sekvens för att ansluta till de stora HANA-instanserna när Microsoft har distribuerat den:

1. [Ansluta virtuella Azure-datorer till HANA-stora instanser](hana-connect-azure-vm-large-instances.md)
2. [Ansluta ett VNet till HANA-stora instanser ExpressRoute](hana-connect-vnet-express-route.md)
3. [Ytterligare nätverks krav (valfritt)](hana-additional-network-requirements.md)
