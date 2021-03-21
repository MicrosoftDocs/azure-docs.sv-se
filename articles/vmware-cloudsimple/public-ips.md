---
title: Azure VMware-lösning av CloudSimple-allokerade offentliga IP-adresser
description: Beskriver hur du allokerar offentliga IP-adresser för virtuella datorer i den privata moln miljön
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bb46ad726cd3b99324e9bb96b998ed1b4da885de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899192"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Allokera offentliga IP-adresser för privat moln miljö

Öppna fliken offentliga IP-adresser på sidan nätverk för att allokera offentliga IP-adresser för virtuella datorer i din privata moln miljö.

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **nätverk** på sido menyn.
2. Välj **offentliga IP-adresser**.
3. Klicka på **ny offentlig IP-adress**.

    ![Sidan offentliga IP-adresser](media/public-ips-page.png)

4. Ange ett namn för att identifiera IP-adress posten.
5. Behåll standard platsen.
6. Använd skjutreglaget för att ändra tids gränsen för inaktivitet, om det behövs.
7. Ange den lokala IP-adress som du vill tilldela en offentlig IP-adress för.
8. Ange ett associerat DNS-namn.
9. Klicka på **Skicka**.

![Allokera offentliga IP-adresser](media/network-public-ip-allocate.png)

Uppgiften att allokera den offentliga IP-adressen börjar. Du kan kontrol lera status för aktiviteten på sidan **aktiviteter > uppgifter** . När allokeringen är klar visas den nya posten på sidan offentliga IP-adresser.
