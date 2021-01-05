---
title: Expandera Azure VMware-lösning via CloudSimple privat moln
description: Beskriver hur du expanderar ett befintligt privat CloudSimple-moln för att lägga till kapacitet i ett befintligt eller nytt kluster
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c469ea3f104e71eb80010d4a5cd421eab6184fcf
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895866"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expandera ett privat CloudSimple-moln

CloudSimple ger flexibiliteten att dynamiskt expandera ett privat moln. Du kan börja med en mindre konfiguration och sedan expandera när du behöver större kapacitet. Eller så kan du skapa ett privat moln baserat på aktuella behov och sedan expandera som förbrukning växer.

Ett privat moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder.  När du expanderar ett privat moln lägger du till noder i det befintliga klustret eller skapar ett nytt kluster. Om du vill expandera ett befintligt kluster måste ytterligare noder vara av samma typ (SKU) som de befintliga noderna. Noderna kan vara av en annan typ för att skapa ett nytt kluster. Mer information om begränsningar för privata moln finns i avsnittet begränsningar i [översikts artikeln om CloudSimple privat moln](cloudsimple-private-cloud.md) .

Ett privat moln skapas med ett standard **Data Center** på vCenter.  Varje data Center fungerar som en hanterings enhet på den översta nivån.  För ett nytt kluster ger CloudSimple möjlighet att lägga till i det befintliga data centret eller skapa ett nytt Data Center.

Som en del av den nya kluster konfigurationen konfigurerar CloudSimple VMware-infrastrukturen.  Inställningarna omfattar lagrings inställningar för virtuellt San disk grupper, VMware hög tillgänglighet och Distributed Resource Scheduler (DRS).

Ett privat moln kan expanderas flera gånger. Expansion kan bara utföras när du ligger inom gränserna för den totala noden. Varje gången du expanderar ett privat moln som du lägger till i det befintliga klustret eller skapar ett nytt.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste vara etablerade innan du kan expandera ditt privata moln.  Mer information om hur du etablerar noder finns i [etablera noder för VMware-lösning av CloudSimple-Azure-](create-nodes.md) artikeln.  För att skapa ett nytt kluster måste du ha minst tre tillgängliga noder av samma SKU.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expandera ett privat moln

1. [Få åtkomst till CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** och välj det privata moln som du vill expandera.

3. I avsnittet Sammanfattning klickar du på **expandera**.

    ![Expandera privat moln](media/resources-expand-private-cloud.png)

4. Välj om du vill utöka det befintliga klustret eller skapa ett nytt vSphere-kluster. När du gör ändringar uppdateras sammanfattnings informationen på sidan.

    * Klicka på **Visa befintligt kluster** för att expandera det befintliga klustret. Välj det kluster som du vill expandera och ange antalet noder som ska läggas till. Varje kluster kan ha högst 16 noder.
    * Om du vill lägga till ett nytt kluster klickar du på **Skapa nytt kluster**. Ange ett namn för klustret. Välj ett befintligt Data Center eller ange ett namn för att skapa ett nytt Data Center. Välj nodtyp. Du kan välja en annan nodtyp när du skapar ett nytt vSphere-kluster, men inte när du expanderar ett befintligt vSphere-kluster. Välj antalet noder. Varje nytt kluster måste ha minst tre noder.

    ![Expandera privat moln – Lägg till noder](media/resources-expand-private-cloud-add-nodes.png)

5. Klicka på **Skicka** för att expandera det privata molnet.

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)