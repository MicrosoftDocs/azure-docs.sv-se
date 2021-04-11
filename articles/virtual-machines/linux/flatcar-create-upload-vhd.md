---
title: Skapa och ladda upp en Flatcar-behållare Linux VHD för användning i Azure
description: Lär dig att skapa och ladda upp en virtuell hård disk som innehåller ett operativ system för Flatcar container Linux.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565299"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Använda en fördefinierad Flatcar-avbildning för Azure

Du kan ladda ned inbyggda Azure Virtual Hard Disk-avbildningar av Flatcar container Linux för var och en av de Flatcar-kanaler som stöds:

- [strömbrytare](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alfa](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [marginal](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Avbildningen är redan helt konfigurerad och optimerad för att köras på Azure. Du behöver bara expandera den.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Skapa en egen Flatcar-baserad virtuell dator för Azure

Alternativt kan du välja att bygga din egen Linux-avbildning för Flatcar-behållare.

Följ instruktionerna som beskrivs i [Flatcar container Linux Developer SDK guide](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)på alla Linux-baserade datorer. När du kör `image_to_vm.sh` skriptet måste du se till att du har `--format=azure` skapat en virtuell Azure-hårddisk.

## <a name="next-steps"></a>Nästa steg

När du har VHD-filen kan du använda den resulterande filen för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp en VHD-fil till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).
