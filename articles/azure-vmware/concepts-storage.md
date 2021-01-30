---
title: Koncept – lagring
description: Lär dig mer om nyckel lagrings funktionerna i privata moln i Azure VMware-lösningar.
ms.topic: conceptual
ms.date: 11/03/2020
ms.openlocfilehash: 8ad1fc3ff2e514330a9ffc680494b5564b1184e4
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062803"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Lagrings koncept för Azure VMware-lösningar

Privata moln i Azure VMware-lösningar tillhandahåller intern lagring med VMware virtuellt San. All lokal lagring från varje värd i ett kluster används i ett virtuellt San-datalager och kryptering av data på rest är tillgänglig och aktive ras som standard. Du kan använda Azure Storage resurser för att utöka lagrings funktionerna i dina privata moln.

## <a name="vsan-clusters"></a>Virtuellt San-kluster

Lokal lagring i varje kluster värd används som en del av ett virtuellt San-datalager. Alla diskgroups använder en NVMe-cachenivå på 1,6 TB med RAW, per värd, SSD-baserad kapacitet på 15,4 TB. Storleken på nivån för ett klusters RAW-kapacitet är antalet värdar per värd gånger antalet värdar. Ett fyra värd kluster ger till exempel 61,6 TB RAW-kapacitet på kapacitets nivån virtuellt San.

Lokal lagring i kluster värdar används i virtuellt San data lager för hela klustret. Alla data lager skapas som en del av en distribution av privata moln och är tillgängliga för användning omedelbart. Cloudadmin-användaren och alla användare i gruppen CloudAdmin kan hantera data lager med följande virtuellt San-behörigheter:
- Data lager. AllocateSpace
- Datastore.Browse
- Datastore.Config
- Data lager. DeleteFile
- Data lager. FileManagement
- Data lager. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Kryptering av data i vila

Virtuellt San-datalager använder kryptering av data i vila som standard. Krypterings lösningen är KMS-baserad och stöder vCenter-åtgärder för nyckel hantering. Nycklar lagras krypterade, omslutna av en Azure Key Vault huvud nyckel. När en värd tas bort från ett kluster kan data på SSD ogiltig förklaras omedelbart.

## <a name="scaling"></a>Skalning

Intern kluster lagrings kapacitet skalas genom att lägga till värdar i ett kluster. För kluster som använder våra värdar ökas kapaciteten för den obearbetade klustret med 15,4 TB med varje ytterligare värd. Kluster som har skapats med GP-värdar har sin RAW-kapacitet ökad med 7,7 TB med varje ytterligare värd. I båda typerna av kluster tar värdar cirka 10 minuter att lägga till i ett kluster. Instruktioner för hur du skalar kluster finns i [själv studie kursen för att skala privat moln][tutorial-scale-private-cloud].

## <a name="azure-storage-integration"></a>Integrering av Azure Storage

Du kan använda Azure Storage-tjänster på arbets belastningar som körs i ditt privata moln. Azure Storage-tjänsterna omfattar lagrings konton, Table Storage och Blob Storage. Anslutningen av arbets belastningar till Azure Storage-tjänster passerar inte Internet. Den här anslutningen ger ytterligare säkerhet och gör att du kan använda SLA-baserade Azure Storage-tjänster i dina privata moln arbets belastningar.

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig om [identitets koncept för privata moln][concepts-identity].

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
