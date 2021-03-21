---
title: Azure VMware-lösning av CloudSimple-åtkomst vSphere-klient
description: Beskriver hur du får åtkomst till vCenter i ditt privata moln.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899124"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Få åtkomst till ditt privata moln vCenter-Portal

Du kan starta din privata moln vCenter-portal från Azure Portal eller CloudSimple-portalen.  med vCenter-portalen kan du hantera VMware-infrastruktur i ditt privata moln.

## <a name="before-you-begin"></a>Innan du börjar

Nätverks anslutning måste upprättas och DNS-namnmatchning måste aktive ras för åtkomst till vCenter-portalen.  Du kan upprätta en nätverks anslutning till ditt privata moln med något av alternativen nedan.

* [Ansluta från den lokala platsen till CloudSimple med ExpressRoute](on-premises-connection.md)
* [Konfigurera en VPN-anslutning till ditt CloudSimple privata moln](set-up-vpn.md)

Om du vill konfigurera DNS-namnmatchning för ditt privata molns VMware-infrastruktur komponenter, se [Konfigurera DNS för namn matchning för privat moln vCenter-åtkomst från lokala arbets stationer](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Anslut till vCenter från Azure Portal

Du kan starta vCenter-portalen för ditt privata moln från Azure Portal.

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple-tjänster**.

3. Välj den CloudSimple-tjänst för ditt privata moln som du vill ansluta till.

4. På sidan **Översikt** klickar du på **Visa privata VMware-moln**

    ![Översikt över CloudSimple-tjänsten](media/cloudsimple-service-overview.png)

5. Välj det privata molnet i listan över privata moln och klicka på **Starta vSphere-klient**.

    ![Starta vSphere-klienten](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Åtkomst till vCenter från CloudSimple-portalen

Du kan starta vCenter-portalen för ditt privata moln från CloudSimple-portalen.

1. Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Från **resurserna** väljer du det privata molnet som du vill komma åt och klickar på **Starta vSphere-klient**.

    ![Starta vSphere-klient – resurser](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Du kan också starta vCenter-portalen från sammanfattnings skärmen i ditt privata moln.

    ![Starta vSphere-klient – Sammanfattning](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera VLAN/undernät för privata moln](create-vlan-subnet.md)
* [CloudSimple privata moln behörighets modell för VMware vCenter](learn-private-cloud-permissions.md)