---
title: Skapa VLAN/undernät – Azure VMware-lösning av CloudSimple
description: Azure VMware-lösningar av CloudSimple – beskriver hur du skapar och hanterar VLAN/undernät för dina privata moln och sedan använder brand Väggs regler.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06bebcb7369f6604fc79c1d3d0a4a6afa8b0a1da
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896319"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Skapa och hantera VLAN/undernät för privata moln

Öppna fliken VLAN/undernät på sidan nätverk för att skapa och hantera VLAN/undernät för privata moln. När du har skapat ett VLAN/undernät kan du använda brand Väggs regler.

## <a name="create-a-vlansubnet"></a>Skapa ett VLAN/undernät

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **nätverk** på sido menyn.
2. Välj **VLAN/undernät**.
3. Klicka på **skapa VLAN/undernät**.

    ![Sidan VLAN/undernät](media/vlan-subnet-page.png)

4. Välj det privata molnet för det nya VLAN/undernät.
5. Ange ett VLAN-ID.
6. Ange under nätets namn.
7. Om du vill aktivera routning på VLAN (undernät) anger du CIDR-intervallet för under nätet. Se till att CIDR-intervallet inte överlappar något av dina lokala undernät, Azure-undernät eller gateway-undernät.
8. Klicka på **Skicka**.

    ![Skapa VLAN/undernät](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Det finns en kvot på 30 VLAN per privat moln. Du kan öka gränserna genom att [kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Använd VLAN-information för att konfigurera en distribuerad port grupp i vSphere

Om du vill skapa en distribuerad port grupp i vSphere följer du anvisningarna i avsnittet VMware Lägg till en distribuerad port grupp i <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">nätverks guiden för vSphere</a>. När du konfigurerar den distribuerade port gruppen anger du VLAN-informationen från CloudSimple-konfigurationen.

![Distribuerad port grupp](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Välj en brand Väggs tabell

Brand Väggs tabeller och associerade regler definieras på sidan **nätverks > brand Väggs tabeller** . Välj den brand Väggs tabell som ska användas för VLAN/undernät för ett privat moln genom att välja VLAN/undernät Klicka på **brand Väggs tabell bilaga** på sidan **VLAN/undernät** . Se [brand Väggs tabeller](firewall.md) för instruktioner om hur du konfigurerar brand Väggs tabeller och definierar regler.

![Länk till brand Väggs tabell](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Ett undernät kan associeras med en brand Väggs tabell. En brand Väggs tabell kan associeras med flera undernät.

## <a name="edit-a-vlansubnet"></a>Redigera ett VLAN/undernät

Om du vill redigera inställningarna för ett VLAN/undernät väljer du det på sidan **VLAN/undernät** och klickar på ikonen **Redigera** . Gör ändringar och klicka på **submet**.

## <a name="delete-a-vlansubnet"></a>Ta bort ett VLAN/undernät

Om du vill ta bort ett VLAN/undernät markerar du det på sidan **VLAN/undernät** och klickar på ikonen **ta bort** . Bekräfta genom att klicka på **ta bort** .
