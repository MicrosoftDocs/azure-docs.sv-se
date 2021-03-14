---
title: Koncept – lagring
description: Lär dig mer om nyckel lagrings funktionerna i privata moln i Azure VMware-lösningar.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: 2551b11ada8b6118fb7524d9fe36f8b4b568aca1
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462481"
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

Virtuellt San-datalager använder kryptering av data i vila som standard. Krypterings lösningen är KMS-baserad och stöder vCenter-åtgärder för nyckel hantering. Nycklar lagras krypterade, omslutna av en Azure Key Vault huvud nyckel. När en värd tas bort från ett kluster kommer data på SSD inte att verifieras omedelbart.

## <a name="scaling"></a>Skalning

Intern kluster lagrings kapacitet skalas genom att lägga till värdar i ett kluster. För kluster som använder AVS36-värdar ökas kapaciteten för den obearbetade klustret med 15,4 TB med varje tillagd värd. Det tar cirka 10 minuter att lägga till värdar i ett kluster.  Instruktioner för hur du skalar kluster finns i [själv studie kursen för att skala privat moln][tutorial-scale-private-cloud].

## <a name="azure-storage-integration"></a>Integrering av Azure Storage

Du kan använda Azure Storage-tjänster i arbets belastningar som körs i ditt privata moln. Azure Storage-tjänsterna omfattar lagrings konton, Table Storage och Blob Storage. Anslutningen av arbets belastningar till Azure Storage-tjänster passerar inte Internet. Den här anslutningen ger högre säkerhet och gör att du kan använda SLA-baserade Azure Storage-tjänster i dina privata moln arbets belastningar.

## <a name="next-steps"></a>Nästa steg

Nu när du har täckt Azure VMware lösning lagrings koncept kan du vilja lära dig mer om:

- [Identitets koncept för privata moln](concepts-identity.md).
- [vSphere-rollbaserad åtkomst kontroll för Azure VMware-lösning](concepts-role-based-access-control.md).
- [Så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md).
- [Azure NetApp Files med Azure VMware-lösning](netapp-files-with-azure-vmware-solution.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
