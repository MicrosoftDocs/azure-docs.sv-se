---
title: Övervakning av SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Övervaka SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 246b9398824597ec337ee9e9ea3dc24267311f60
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669586"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Övervaka SAP HANA (stora instanser) i Azure

SAP HANA på Azure (stora instanser) skiljer sig inte från någon annan IaaS-distribution – du måste övervaka vad operativ systemet och programmet gör och hur programmen använder följande resurser:

- Processor
- Minne
- Nätverksbandbredd
- Diskutrymme

Med Azure Virtual Machines måste du ta reda på om resurs klasserna som heter ovan räcker till eller om de blir uttömda. Här är mer information om var och en av de olika klasserna:

**Förbrukning av processor resurser:** Förhållandet att SAP som definierats för viss arbets belastning mot HANA upprätthålls för att säkerställa att det finns tillräckligt med tillgängliga processor resurser för att arbeta genom de data som lagras i minnet. Det kan dock finnas fall där HANA förbrukar många processorer som kör frågor på grund av saknade index eller liknande problem. Det innebär att du bör övervaka användningen av CPU-resurser för den stora instans enheten HANA och CPU-resurser som förbrukas av de aktuella HANA-tjänsterna.

**Minnes förbrukning:** Är viktigt att övervaka inifrån HANA, samt utanför HANA på enheten. I HANA kan du övervaka hur data använder HANA-allokerat minne för att ligga inom de nödvändiga storleks rikt linjerna för SAP. Du vill också övervaka minnes användningen på den stora instansen för att se till att ytterligare installerad icke-HANA-programvara inte förbrukar för mycket minne och därmed konkurrerar med HANA för minne.

**Nätverks bandbredd:** Azure VNet-gatewayen är begränsad till data bandbredden som flyttas till Azure VNet, så det är bra att övervaka de data som tas emot av alla virtuella Azure-datorer i ett VNet för att ta reda på hur nära du är i gränserna för den Azure Gateway-SKU som du har valt. På den stora volymen HANA är det viktigt att övervaka inkommande och utgående nätverks trafik även och att hålla koll på de volymer som hanteras över tid.

**Disk utrymme:** Användningen av disk utrymme ökar ofta över tid. De vanligaste orsakerna är: data volym ökar, körning av transaktions logg säkerhets kopior, lagring av spårningsfiler och lagring av ögonblicks bilder. Därför är det viktigt att övervaka användningen av disk utrymme och hantera disk utrymmet som är associerat med den stora volymen HANA-instans.

För **typ II-SKU: er** i de stora instanserna av Hana, levereras servern med de förinstallerade system diagnos verktygen. Du kan använda dessa diagnostiska verktyg för att utföra system hälso kontrollen. Kör följande kommando för att generera hälso kontroll logg filen på/var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
När du arbetar med Microsoft Support-teamet för att felsöka ett problem kan du också uppmanas att tillhandahålla loggfilerna med hjälp av dessa diagnostikverktyg. Du kan zippa filen med hjälp av följande kommando.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Nästa steg**

- Se [hur du övervakar SAP HANA (stora instanser) i Azure](./hana-monitor-troubleshoot.md).
