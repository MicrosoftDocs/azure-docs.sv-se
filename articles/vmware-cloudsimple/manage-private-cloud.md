---
title: Hantera Azure VMware-lösningen via CloudSimple privat moln
description: Beskriver de funktioner som är tillgängliga för hantering av dina CloudSimple privata moln resurser och aktivitet
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895214"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Hantera resurser och aktivitet för privata moln

Privata moln hanteras från CloudSimple-portalen.  Kontrol lera status, tillgängliga resurser, aktivitet i det privata molnet och andra inställningar från CloudSimple-portalen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Visa listan över privata moln

Fliken **privata moln** på sidan **resurser** visar alla privata moln i din prenumeration. Information inkluderar namn, antal vSphere-kluster, plats, nuvarande tillstånd för det privata molnet och resursinformation.

![Sidan privat moln](media/manage-private-cloud.png)

Välj ett privat moln för ytterligare information och åtgärder.

## <a name="private-cloud-summary"></a>Översikt över privat moln

Visa en omfattande sammanfattning av det valda privata molnet.  Sammanfattnings sidan innehåller de DNS-servrar som distribueras i det privata molnet.  Du kan konfigurera DNS-vidarebefordran från lokala DNS-servrar till dina privata moln DNS-servrar.  Mer information om DNS-vidarebefordran finns i [Konfigurera DNS för namn matchning för privata moln vCenter från lokalt](./on-premises-dns-setup.md).

![Översikt över privat moln](media/private-cloud-summary.png)

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Starta vSphere-klienten](./vcenter-access.md). Få åtkomst till vCenter för det här privata molnet.
* [Köpa noder](create-nodes.md). Lägg till noder i det här privata molnet.
* [Expandera](expand-private-cloud.md). Lägg till noder i det här privata molnet.
* **Uppdatera**. Uppdatera informationen på den här sidan.
* **Delete** (Ta bort). Du kan när som helst ta bort det privata molnet. **Innan du tar bort kontrollerar du att du har säkerhetskopierat alla system och data.** Om du tar bort ett privat moln tas alla virtuella datorer, vCenter-konfiguration och data bort. Klicka på **ta bort** i sammanfattnings avsnittet för det valda privata molnet. Efter borttagning raderas alla privata moln data i en säker och mycket kompatibel radering-process.
* [Ändra vSphere-privilegier](escalate-private-cloud-privileges.md).  Eskalera dina privilegier i det här privata molnet.

## <a name="private-cloud-vlanssubnets"></a>VLAN/undernät för privata moln

Visa listan över definierade VLAN/undernät för det valda privata molnet.  Listan innehåller de hanterings-VLAN/-undernät som skapats när det privata molnet skapades.

![Privat moln – VLAN/undernät](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Lägg till VLAN/undernät](./create-vlan-subnet.md). Lägg till ett VLAN/en delmängd i det här privata molnet.

Välj ett VLAN/undernät för följande åtgärder
* [Koppla brand Väggs tabell](./firewall.md). Koppla en brand Väggs tabell till det här privata molnet.
* **Redigera**
* **Ta bort** (endast användardefinierade VLAN/undernät)

## <a name="private-cloud-activity"></a>Aktivitet för privat moln

Visa följande information för det valda privata molnet.  Aktivitets informationen är en filtrerad lista över alla aktiviteter för det valda privata molnet.  Den här sidan visar upp till 25 senaste aktiviteter.

* Senaste aviseringar
* Senaste händelser
* Senaste aktiviteter
* Senaste granskning

![Privat moln – aktivitet](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Moln räcken

Moln räcken är Bygg stenarna i ditt privata moln. Varje rack tillhandahåller en kapacitets enhet. CloudSimple konfigurerar automatiskt moln räcken utifrån dina val när du skapar eller expanderar ett privat moln.  Visa en fullständig lista över moln räcken, inklusive det privata moln som varje är tilldelad till.

![Privat moln – moln räcken](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

Lista över VMware-hanterings resurser och virtuella datorer som för närvarande är konfigurerade i det privata molnet. Informationen omfattar program varu versionen, det fullständigt kvalificerade domän namnet (FQDN) och IP-adressen för resurserna.

![Privat moln – vSphere hanterings nätverk](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)
