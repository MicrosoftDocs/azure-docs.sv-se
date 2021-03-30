---
title: VMware-komponenter för privata moln
titleSuffix: Azure VMware Solution by CloudSimple
description: Lär dig hur CloudSimple-tjänsten tillåter att du distribuerar VMware internt på Azure-platser. Privata moln är integrerade med resten av Azure-molnet.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88142510"
---
# <a name="private-cloud-vmware-components"></a>VMware-komponenter för privata moln

Ett privat moln är en isolerad VMware-stack (ESXi-värdar, vCenter-, virtuellt San-och NSX)-miljö som hanteras av en vCenter-Server i en hanterings domän.  Med CloudSimple-tjänsten kan du distribuera VMware i Azure Bare Metal-infrastruktur på Azure-platser.  Privata moln är integrerade med resten av Azure-molnet.  Ett privat moln distribueras med följande VMware stack-komponenter:

* **VMware ESXi-** Hypervisor på Azure-dedikerade noder
* **VMware vCenter –** Utrustning för centraliserad hantering av vSphere-miljö för privata moln
* **VMware-virtuellt San –** Lösning för Hyper-konvergerad infrastruktur
* **VMware NSX Data Center –** Nätverksvirtualisering och säkerhets program vara  

## <a name="vmware-component-versions"></a>Versioner av VMware-komponenter

En VMware-stack för privata moln distribueras med följande program varu version.

| Komponent | Version | Licensierad version |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise plus |
| vCenter | 6.7 U2 | vCenter standard |
| Virtuellt San | 6.7 | Stora företag |
| NSX Data Center | 2.4.1 | Avancerat |

## <a name="esxi"></a>ESXi

VMware ESXi installeras på etablerade CloudSimple-noder när du skapar ett privat moln.  ESXi tillhandahåller hypervisorn för distribution av virtuella arbets belastnings datorer (VM).  Noder tillhandahåller en aktiv infrastruktur (beräkning och lagring) i ditt privata moln.  Noderna ingår i vSphere-klustret i det privata molnet.  Varje nod har fyra fysiska nätverks gränssnitt som är anslutna till Underlay-nätverket.  Två fysiska nätverks gränssnitt används för att skapa en **vSphere-distribuerad växel (VDS)** på vCenter och två används för att skapa en **NSX-hanterad virtuell DFS-växel (N-VDS)**.  Nätverks gränssnitt har kon figurer ATS i aktivt-aktivt läge för hög tillgänglighet.

Läs mer på VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter Server-apparat

vCenter Server-installation (VCSA) tillhandahåller autentiserings-, hanterings-och Orchestration-funktioner för VMware-lösning av CloudSimple. VCSA med Embedded Platform Services Controller (PSC) distribueras när du skapar ett privat moln.  VCSA distribueras i vSphere-klustret som skapas när du distribuerar ditt privata moln.  Varje privat moln har sin egen VCSA.  Vid expansion av ett privat moln läggs noderna till i VCSA i det privata molnet.

### <a name="vcenter-single-sign-on"></a>enkel inloggning för vCenter

Embedded Platform service Controller på VCSA är kopplad till en **vCenter Single Sign-On-domän**.  Domän namnet är **cloudsimple. local**.  En standard användare **CloudOwner@cloudsimple.com** skapas för åtkomst till vCenter.  Du kan lägga till dina lokala/Azure Active Directory [Identity sources för vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>Virtuellt SAN-lagring

Privata moln skapas med fullständigt konfigurerade virtuellt SAN-lagring för hela Flash, lokalt till klustret.  Minst tre noder av samma SKU krävs för att skapa ett vSphere-kluster med virtuellt San data lager.  Avduplicering och komprimering är aktiverat i virtuellt San-datalagret som standard.  Två disk grupper skapas på varje nod i vSphere-klustret. Varje disk grupp innehåller en cache-disk och tre kapacitets diskar.

En standard lagrings princip för virtuellt San skapas i vSphere-klustret och tillämpas på virtuellt San-datalagret.  Den här principen avgör hur VM-lagrings objekt ska tillhandahållas och allokeras i data lagret för att garantera den tjänst nivå som krävs.  Lagrings principen definierar de **problem som ska tolereras (FTT)** och **metoden för feltolerans**.  Du kan skapa nya lagrings principer och tillämpa dem på de virtuella datorerna. För att upprätthålla SLA måste den 25% lediga kapaciteten vara kvar på virtuellt San-datalagret.  

### <a name="default-vsan-storage-policy"></a>Standard lagrings princip för virtuellt San

I tabellen nedan visas standard parametrarna för virtuellt San-lagrings principer.

| Antal noder i vSphere-kluster | FTT | Metod för feltolerans |
|------------------------------------|-----|--------------------------|
| 3-och 4 noder | 1 | RAID 1 (spegling) – skapar 2 kopior |
| 5 till 16 noder | 2 | RAID 1 (spegling) – skapar 3 kopior |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center tillhandahåller nätverksvirtualisering, mikrosegmentering och nätverks säkerhets funktioner i ditt privata moln.  Du kan konfigurera alla tjänster som stöds av NSX Data Center i ditt privata moln genom NSX.  När du skapar ett privat moln installeras och konfigureras följande NSX-komponenter.

* NSXT Manager
* Transport zoner
* Profil för värd och Edge-överordnad länk
* Logisk växel för Edge-Transport, Ext1 och ext2
* IP-pool för ESXi transport Node
* Noden IP-pool för Edge-Transport
* Edge-noder
* DRS för Controller-och Edge-VM: ar
* Nivå 0-router
* Aktivera BGP på Tier0-routern

## <a name="vsphere-cluster"></a>vSphere-kluster

ESXi-värdar konfigureras som ett kluster för att säkerställa hög tillgänglighet för det privata molnet.  När du skapar ett privat moln distribueras hanterings komponenter i vSphere i det första klustret.  En resurspool skapas för hanterings komponenter och alla virtuella hanterings datorer distribueras i den här resurspoolen. Det går inte att ta bort det första klustret för att minska det privata molnet.  vSphere-kluster ger hög tillgänglighet för virtuella datorer som använder **VSPHERE ha**.  Fel som tolereras baseras på antalet tillgängliga noder i klustret.  Du kan använda formeln ```Number of nodes = 2N+1``` där ```N``` är antalet felaktiga försök att tolerera.

### <a name="vsphere-cluster-limits"></a>vSphere kluster gränser

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat moln (första vSphere-kluster) | 3 |
| Maximalt antal noder i ett vSphere-kluster i ett privat moln | 16 |
| Maximalt antal noder i ett privat moln | 64 |
| Maximalt antal vSphere-kluster i ett privat moln | 21 |
| Minsta antal noder i ett nytt vSphere-kluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Underhåll av VMware-infrastruktur

Ibland är det nödvändigt att göra ändringar i konfigurationen av VMware-infrastrukturen. För närvarande kan dessa intervall inträffa var 1-2 månad, men frekvensen förväntas avböjas över tid. Den här typen av underhåll kan vanligt vis utföras utan att den normala förbrukningen av CloudSimple-tjänsterna avbryts. Under ett underhålls intervall för VMware fortsätter följande tjänster att fungera utan påverkan:

* Hanterings plan och program för VMware
* vCenter-åtkomst
* Alla nätverk och lagring
* All Azure-trafik

## <a name="updates-and-upgrades"></a>Uppdateringar och uppgraderingar

CloudSimple ansvarar för livs cykel hantering av VMware-programvara (ESXi, vCenter, PSC och NSX) i det privata molnet.

Program uppdateringar omfattar:

* **Korrigeringar**. Säkerhets korrigeringar eller fel korrigeringar som publicerats av VMware.
* **Uppdateringar**. Lägre versions ändring av en VMware stack-komponent.
* **Uppgraderingar**. Huvud versions ändring av en VMware stack-komponent.

CloudSimple testar en kritisk säkerhets korrigering så snart den blir tillgänglig från VMware. Enligt SLA samlar CloudSimple ut säkerhets korrigeringen till privata moln miljöer inom en vecka.

CloudSimple tillhandahåller kvartals Visa uppdateringar av VMware-programkomponenter. När en ny huvud version av VMware-programvaran är tillgänglig fungerar CloudSimple med kunder för att samordna en lämplig underhålls period för uppgradering.  

## <a name="next-steps"></a>Nästa steg

* [Underhåll och uppdateringar av CloudSimple](cloudsimple-maintenance-updates.md)
