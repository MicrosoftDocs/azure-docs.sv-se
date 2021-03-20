---
title: Azure VMware-lösning av CloudSimple-eskalera CloudSimple-privilegier
description: Beskriver hur du eskalerar CloudSimple-behörigheter för att utföra administrativa funktioner i det privata molnet vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895735"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eskalera CloudSimple-privilegier för att utföra administrativa funktioner i privat moln vCenter

CloudSimple-behörigheterna är utformad för att ge vCenter-användare de behörigheter de behöver för att utföra normala åtgärder. I vissa fall kan en användare behöva ytterligare behörighet för att utföra en viss uppgift.  Du kan eskalera privilegier för en vCenter SSO-användare under en begränsad period.

Orsaker till att eskalerade privilegier kan vara följande:

* Konfiguration av identitets källor
* Användarhantering
* Borttagning av distribuerad port grupp
* Installera vCenter-lösningar (till exempel säkerhets kopierings program)
* Skapa tjänst konton

> [!WARNING]
> Åtgärder som vidtas i det eskalerade privilegierade läget kan påverka systemet negativt och kan göra att systemet blir otillgängligt. Utför endast de åtgärder som krävs under eskalering perioden.

Från CloudSimple-portalen [eskalerar du privilegier](escalate-private-cloud-privileges.md) för den lokala CloudOwner-användaren på vCenter SSO.  Du kan bara eskalera fjärran vändare behörighet om ytterligare identitetsprovider har kon figurer ATS på vCenter.  Eskalering av privilegier innebär att lägga till den valda användaren i den inbyggda gruppen vSphere-administratörer.  Endast en användare kan ha eskalerade privilegier.  Om du behöver eskalera en annan användares behörigheter måste du först ta bort privilegierna för de aktuella användarna.

Användare från ytterligare identitets källor måste läggas till som medlemmar i gruppen CloudOwner.

> [!CAUTION]
> Nya användare får endast läggas till i *moln-ägar-grupp*, *Cloud-global-Cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* eller, *Cloud-global-VM-admin-Group*.  Användare som lagts till i gruppen *Administratörer* tas då bort automatiskt.  Endast tjänst konton måste läggas till i *Administratörs* gruppen och tjänst kontona får inte användas för att logga in på vSphere-webbgränssnittet.

Under perioden för eskaleringen använder CloudSimple automatisk övervakning med tillhör ande aviseringar för att identifiera eventuella ändringar i miljön.
