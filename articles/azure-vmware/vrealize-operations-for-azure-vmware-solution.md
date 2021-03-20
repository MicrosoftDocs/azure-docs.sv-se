---
title: Konfigurera vRealize-åtgärder för Azure VMware-lösning
description: Lär dig hur du konfigurerar vRealize-åtgärder för ditt privata moln i Azure VMware-lösningen.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 3d2617f893c34fde6a154f9f93b5bda520c5a91a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738437"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Konfigurera vRealize-åtgärder för Azure VMware-lösning


vRealize Operations Manager är en plattform för drift hantering som gör att VMware Infrastructure-administratörer kan övervaka system resurser. De här system resurserna kan vara program nivå eller infrastruktur nivå (både fysiska och virtuella) objekt. De flesta VMware-administratörer har använt vRealize-åtgärder för att övervaka och hantera VMwares privata moln komponenter – vCenter, ESXi, NSX-T, virtuellt San och VMware HCX.  Varje privat moln i Azure VMware-lösningen innehåller en dedikerad vCenter-, NSX-, virtuellt San-och HCX-distribution. 

Granska noga [innan du börjar](#before-you-begin) och [förutsättningarna](#prerequisites) först. Sedan vägleder vi dig genom de två typiska distributions topologierna:

> [!div class="checklist"]
> * [Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [vRealize-åtgärder som körs på Azure VMware-distribution av VMware-lösningar](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Innan du börjar
* Läs mer om hur du distribuerar vRealize-åtgärder i [vRealize Operations Manager Product Documentation](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 
* Läs igenom [självstudien](tutorial-network-checklist.md)om Azure VMware Solution Software-Defined Data Center (SDDC).
* Du kan också gå igenom produkt dokumentationen för [Fjärrkontrollanten för vRealize](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) för den lokala vRealize driften hanterings alternativ för Azure VMware-lösningen. 


## <a name="prerequisites"></a>Förutsättningar
* [vRealize-Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) installerad.
* En VPN-eller Azure-ExpressRoute som kon figurer ATS mellan lokala och Azure VMware-lösningar SDDC.
* Ett privat moln i Azure VMware-lösningen har distribuerats i Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment
De flesta kunder har en befintlig lokal distribution av vRealize-åtgärder för att hantera en eller flera lokala vCenter-domäner. När de etablerar ett privat moln i Azure VMware-lösningen ansluter de sin lokala miljö med sitt privata moln med hjälp av en Azure-ExpressRoute eller en Layer 3 VPN-lösning.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment" border="false":::

För att utöka vRealize-funktionerna till Azures privata moln för VMware-lösningar skapar du en kort [instans för de privata moln resurserna](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Den samlar in data från det privata molnet i Azure VMware-lösningen och tar den till lokala vRealize-åtgärder. Den lokala vRealize Operations Manager-instansen kan ansluta direkt till vCenter-och NSX-T-hanteraren på Azure VMware-lösningen. Alternativt kan du distribuera en vRealize-fjärrcollector i Azure VMware-lösningen privat moln. Insamlaren komprimerar och krypterar data som samlas in från det privata molnet innan de skickas över ExpressRoute eller VPN-nätverket till vRealize-Operations Manager som körs lokalt. 

> [!TIP]
> I VMware- [dokumentationen](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) hittar du steg-för-steg-instruktioner för att installera vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>vRealize-åtgärder som körs på Azure VMware-distribution av VMware-lösningar

Ett annat alternativ är att distribuera en instans av vRealize Operations Manager i ett vSphere-kluster i det privata molnet. 

>[!IMPORTANT]
>Det här alternativet stöds inte för närvarande av VMware.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize-åtgärder som körs på Azure VMware-lösning" border="false":::

När instansen har distribuerats kan du konfigurera vRealize-åtgärder för att samla in data från vCenter, ESXi, NSX-T, virtuellt San och HCX. 



## <a name="known-limitations"></a>Kända begränsningar

- **Cloudadmin \@ vSphere. local** User i Azure VMware-lösningen har [begränsade privilegier](concepts-role-based-access-control.md).  Virtual Machines (VM) i Azure VMware-lösningen har inte stöd för insamling av gäst minne med VMware-verktyg.  Aktiv och förbrukad minnes användning fortsätter att fungera i det här fallet.
- Arbets belastnings optimering för värdbaserade affärs syften fungerar inte eftersom Azure VMware-lösningar hanterar klusterkonfigurationer, inklusive DRS-inställningar.
- Arbets belastnings optimering för kors kluster placering i SDDC med hjälp av kluster-baserad affärs avsikt stöds fullt ut med vRealize Operations Manager 8,0 och senare. Men arbets belastnings optimering känner inte till resurspooler och placerar de virtuella datorerna på kluster nivå. En användare kan korrigera den manuellt i Azure VMware-lösningen vCenter Server gränssnittet.
- Det går inte att logga in på vRealize Operations Manager att använda din Azure VMware-lösning vCenter Server autentiseringsuppgifter. 
- Azure VMware-lösningen stöder inte plugin-programmet vRealize Operations Manager.

När du ansluter Azure VMware-lösningen vCenter till vRealize Operations Manager med ett vCenter Server moln konto visas en varning:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Varnings korts instansen har skapats":::

Varningen beror på att **cloudadmin \@ vSphere. local** User i Azure VMware-lösningen inte har tillräcklig behörighet för att utföra alla vCenter Server åtgärder som krävs för registrering. Behörigheterna är dock tillräckliga för att kort instansen ska kunna utföra data insamling, som visas nedan:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Kort instans för att utföra insamling av data":::

Mer information finns i [behörigheter som krävs för att konfigurera en instans av vCenter-kort](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




