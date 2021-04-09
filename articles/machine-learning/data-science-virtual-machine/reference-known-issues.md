---
title: 'Referens: kända problem & fel sökning'
titleSuffix: Azure Data Science Virtual  Machine
description: Hämta en lista över kända problem, lösningar och fel sökning för Azure Data Science Virtual Machine
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101656864"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Kända problem och fel sökning av Azure-Data Science Virtual Machine

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som du kan stöta på när du använder Azure-Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Installations problem för python-paket

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Om du installerar paket med pip bryts beroenden på Linux

Använd `sudo pip install` i stället för `pip install` när du installerar paket.

## <a name="disk-encryption-issues"></a>Problem med diskkryptering

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Disk kryptering Miss lyckas på Ubuntu-DSVM

Azure Disk Encryption (ADE) stöds inte för närvarande på Ubuntu-DSVM. Som en lösning bör du överväga att konfigurera [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Verktyget visas inaktiverat

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V fungerar inte på Windows-DSVM

Att Hyper-V ursprungligen inte fungerar i Windows är förväntat beteende. Vi har inaktiverat vissa tjänster för start prestanda. Så här aktiverar du Hyper-V:

1. Öppna Sök fältet på Windows-DSVM
1. Skriv i "tjänster"
1. Ställ in alla Hyper-V-tjänster på "Manuell"
1. Ange "automatisk" hantering av virtuella Hyper-V-datorer

Den slutliga skärmen bör se ut så här:

   ![Aktivera Hyper-V](./media/workaround/hyperv-enable-dsvm.png)