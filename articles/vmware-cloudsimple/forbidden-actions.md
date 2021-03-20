---
title: Förbjudna åtgärder under utökad åtkomst
description: VMware-motorn återställer ändringarna för att säkerställa att tjänsten förblir oavbruten när VMware-motorn identifierar någon av följande förbjudna åtgärder.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92915453"
---
# <a name="forbidden-actions-during-elevated-access"></a>Förbjudna åtgärder under utökad åtkomst

Under upphöjnings perioden är vissa åtgärder förbjudna. När en VMware-motor identifierar någon av följande förbjudna åtgärder återställer VMware-motorn ändringarna för att säkerställa att tjänsten förblir oavbruten.

## <a name="cluster-actions"></a>Kluster åtgärder

- Tar bort ett kluster från vCenter.
- Ändrar hög tillgänglighet för vSphere (HA) i ett kluster.
- Lägger till en värd i klustret från vCenter.
- Tar bort en värd från klustret från vCenter.

## <a name="host-actions"></a>Värd åtgärder

- Tar bort data lager på en ESXi-värd.
- Avinstallerar vCenter-agenten från värden.
- Ändra värd konfigurationen.
- Göra ändringar i värd profilerna.
- Placera en värd i underhålls läge.

## <a name="network-actions"></a>Nätverks åtgärder

- Ta bort standard DVS (Distributed Virtual Switch) i ett privat moln.
- Tar bort en värd från standard-DVS.
- Importerar alla DVS-inställningar.
- Konfigurerar om alla DVS-inställningar.
- Uppgraderar eventuella DVS.
- Tar bort portgrupp för hantering.
- Redigera hanterings portgrupp.

## <a name="roles-and-permissions-actions"></a>Roller och behörighets åtgärder

- Skapar en global roll.
- Ändra eller ta bort behörighet till alla hanterings objekt.
- Ändra eller ta bort standard roller.
- Öka behörigheterna för en roll till högre än moln ägar rollen.

## <a name="other-actions"></a>Andra åtgärder

- Tar bort alla standard licenser:
  - vCenter Server
  - ESXi-noder
  - NSX-T
  - HCX
- Ändra eller ta bort hanterings-resurspoolen.
- Klona hantering av virtuella datorer.


## <a name="next-steps"></a>Nästa steg
[Underhåll och uppdateringar av CloudSimple](cloudsimple-maintenance-updates.md) 
