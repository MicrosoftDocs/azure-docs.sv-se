---
title: Uppdaterings frekvens för VMware-program
description: Uppdaterings frekvens för VMware-program som stöds för Azure VMware-lösningen.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869871"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Microsoft ansvarar för livs cykel hanteringen av VMware-programvara (ESXi, vCenter, PSC och NXS) i det privata molnet för Azure VMware-lösningen.

Program varan för det privata molnet uppgraderas enligt ett schema som spårar program varu paketets version från VMware. Det privata molnet kräver ingen stillestånds tid för uppgraderingar.

Den privata moln program varu paketets uppgraderingar behåller program varan i en version av den senaste versionen av program varu paket från VMware. Program varu versionerna för det privata molnet kan skilja sig från de senaste versionerna av de enskilda program varu komponenterna (ESXi, NSX-T, vCenter, virtuellt SAN).

Program uppdateringar omfattar:

- **Korrigeringar** – säkerhets korrigeringar eller fel korrigeringar som publicerats av VMware
- **Uppdateringar** -lägre versions ändring av en VMware stack-komponent
- **Uppgraderingar** – huvud versions ändring av en VMware stack-komponent

Microsoft testar en kritisk säkerhets korrigering så snart den blir tillgänglig från VMware.

Dokumenterade VMware-lösningar implementeras i stället för att installera en motsvarande korrigering tills nästa schemalagda uppdateringar har distribuerats. 
