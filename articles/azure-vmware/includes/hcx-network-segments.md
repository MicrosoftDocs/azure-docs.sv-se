---
title: Nätverks segment för VMware HCX
description: Det krävs fyra nätverk för VMware-HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622162"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Det finns olika sätt att konfigurera VMware HCX Network-segment lokalt. I följande avsnitt beskrivs en enkel konfiguration som stöder ett pilot-eller litet produktions användnings fall.  När du designar för hundratals eller tusentals arbets belastningar kan den här konfigurationen behöva ändras, beroende på migreringens behov.  

Identifiera följande för att förbereda VMware HCX-distributionen till att stödja pilot-eller Small produktionens användnings fall:

- **Hanterings nätverk:** När du distribuerar VMware HCX lokalt måste du definiera ett hanterings nätverk.  Vanligt vis är det samma hanterings nätverk som används av ditt lokala VMware-kluster.  Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på hur stor distributionen är, utöver pilot eller litet användnings fall.

> [!NOTE]
   > Den rekommenderade metoden är att skapa ett/26-nätverk eftersom du kan använda upp till 10 service nät och 60 nätverks Extender (-1 per service nät). Du kan sträcka ut **åtta** nätverk per nätverks Extender med hjälp av privata moln i Azure VMware-lösningen.
   >
   
- **vMotion nätverk:** När du distribuerar VMware HCX lokalt måste du definiera ett vMotion-nätverk.  Normalt är det samma nätverk som används för vMotion av ditt lokala VMware-kluster.  Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på hur stor distributionen är, utöver pilot eller litet användnings fall.

   VMotion-nätverket måste exponeras på en distribuerad virtuell växel eller vSwitch0. Om den inte gör det ändrar du miljön så att den passar.

   > [!NOTE]
   > Många VMware-miljöer använder icke-dirigerade nätverks segment för vMotion, vilket inte innebär några problem.

- **Överordnat nätverk:** När du distribuerar VMware HCX lokalt måste du definiera ett nätverk för överordnad länk. Använd det hanterings nätverk som definierats som överordnat nätverk.
   
- **Nätverk för replikering:** När du distribuerar VMware HCX lokalt måste du definiera ett replikeringsalternativ. Använd det hanterings nätverk som definierats som replikeringsläget för nätverket.  Om de lokala kluster värdarna använder ett dedikerat VMkernel nätverk, reservera **två** IP-adresser i det här nätverks segmentet och Använd VMkernel nätverk för replikering.
