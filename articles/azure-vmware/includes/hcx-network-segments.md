---
title: Nätverks segment för VMware HCX
description: Det krävs fyra nätverk för VMware-HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251366"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Det finns olika sätt att konfigurera VMware HCX Network-segment lokalt. I följande avsnitt beskrivs en enkel konfiguration som stöder ett pilot-eller litet produktions användnings fall.  När du designar för hundratals eller tusentals arbets belastningar kan den här konfigurationen behöva ändras, beroende på migreringens behov.  

Identifiera följande för att förbereda VMware HCX-distributionen till att stödja pilot-eller Small produktionens användnings fall:

- **Hanterings nätverk:** När du distribuerar VMware HCX lokalt måste du identifiera ett hanterings nätverk för VMware HCX.  Vanligt vis är det samma hanterings nätverk som används av ditt lokala VMware-kluster.  Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på hur stor distributionen är, utöver pilot eller litet användnings fall.

  > [!NOTE]
  > Förbered för stora miljöer, i stället för att använda det hanterings nätverk som används för det lokala VMware-klustret, skapar du ett nytt/26-nätverk och visar nätverket som en port grupp till ditt lokala VMware-kluster.  Du kan sedan skapa upp till 10 service nät och 60 nätverks Extender (-1 per service nät). Du kan sträcka ut **åtta** nätverk per nätverks Extender med hjälp av privata moln i Azure VMware-lösningen.
  >

- **Överordnat nätverk:** När du distribuerar VMware HCX lokalt måste du identifiera ett nätverk för överordnad länk för VMware HCX. Använd samma nätverk som du kommer att använda för hanterings nätverket. 

- **vMotion nätverk:** När du distribuerar VMware HCX lokalt måste du identifiera ett vMotion-nätverk för VMware HCX.  Normalt är det samma nätverk som används för vMotion av ditt lokala VMware-kluster.  Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på hur stor distributionen är, utöver pilot eller litet användnings fall.

   VMotion-nätverket måste exponeras på en distribuerad virtuell växel eller vSwitch0. Om den inte gör det ändrar du miljön så att den passar.

   > [!NOTE]
   > Många VMware-miljöer använder icke-dirigerade nätverks segment för vMotion, vilket inte innebär några problem.
  
- **Nätverk för replikering:** När du distribuerar VMware HCX lokalt måste du definiera ett replikeringsalternativ. Använd samma nätverk som du använder för dina hanterings-och överordnande nätverk.  Om de lokala kluster värdarna använder ett dedikerat VMkernel nätverk, reservera **två** IP-adresser i det här nätverks segmentet och Använd VMkernel nätverk för replikering.
