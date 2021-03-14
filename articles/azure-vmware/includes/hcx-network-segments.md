---
title: Nätverks segment för VMware HCX
description: Det krävs fyra nätverk för VMware-HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 1f0fcc1ddeeb26702a297035e6ae2a2f73dd71d6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462374"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Fyra nätverk behövs för VMware-HCX:

- **Hanterings nätverk:** Vanligt vis är det samma hanterings nätverk som används av Azure VMware-lösnings kluster. Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.

   > [!NOTE]
   > Den rekommenderade metoden är att skapa ett/26-nätverk eftersom du kan använda upp till 10 service nät och 60 nätverks Extender (-1 per service nät). Du kan sträcka ut **åtta** nätverk per nätverks Extender med hjälp av privata moln i Azure VMware-lösningen.
   >
   
- **vMotion nätverk:** Vanligt vis är det samma nätverk som används för vMotion i Azure VMware-lösnings kluster.  Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.  

   VMotion-nätverket måste exponeras på en distribuerad virtuell växel eller vSwitch0. Om det inte är det ändrar du miljön.

   > [!NOTE]
   > Det här nätverket kan vara privat (inte dirigerat).

- **Överordnat nätverk:** Du vill skapa ett nytt nätverk för VMware HCX-överordnad länk och utöka det till ditt vSphere-kluster via en port grupp. Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.  

   > [!NOTE]
   > Den rekommenderade metoden är att skapa ett/26-nätverk eftersom du kan använda upp till 10 service nät och 60 nätverks Extender (-1 per service nät). Du kan sträcka ut **åtta** nätverk per nätverks Extender med hjälp av privata moln i Azure VMware-lösningen.
   >
   
- **Nätverk för replikering:** Detta är valfritt. Du vill skapa ett nytt nätverk för VMware HCX-replikering och utöka det nätverket till ditt vSphere-kluster via en port grupp. Identifiera minst **två** IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.

   > [!NOTE]
   > Den här konfigurationen är bara möjlig när lokala kluster värdar använder ett dedikerat VMkernel nätverk.  Om ditt lokala kluster inte har ett dedikerat VMkernel nätverk definierat, behöver du inte skapa det här nätverket.
