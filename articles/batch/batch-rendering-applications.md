---
title: Återge program
description: Det är möjligt att använda alla åter givnings program med Azure Batch. VIRTUELLA Azure Marketplace-avbildningar är dock tillgängliga med vanliga program för installation.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 18fc142d2fa9c819b9872c64ef7aae4c73cc2428
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635344"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Förinstallerade program på VM-avbildningar för batch-rendering

Det är möjligt att använda alla åter givnings program med Azure Batch. VIRTUELLA Azure Marketplace-avbildningar är dock tillgängliga med vanliga program för installation.

I tillämpliga fall är licens för betalning per användning tillgängligt för de förinstallerade åter givnings programmen. När en batch-pool skapas kan de nödvändiga programmen anges och både kostnaden för VM och program debiteras per minut. Program priserna visas på [sidan Azure Batch priser](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Vissa program stöder bara Windows, men de flesta stöds i både Windows och Linux.

## <a name="applications-on-latest-centos-7-rendering-image"></a>Program på den senaste CentOS 7-åter givnings avbildningen

Följande lista gäller CentOS rendering-avbildningen, version 1.2.0.

* Autodesk Maya I/O 2020 uppdatering 4,6
* Autodesk Arnold för Maya 2020 (Arnold version 6.2.0.0) MtoA-4.2.0-2020
* Kaos Group V-Ray för Maya 2020 (version 5.00.21)
* Över gång (2,80)
* AZ 10

## <a name="applications-on-latest-windows-server-rendering-image"></a>Program på den senaste Windows Server rendering-avbildningen

Följande lista gäller Windows Server rendering-avbildningen, version 1.5.0.

* Autodesk Maya I/O 2020 uppdatering 4,4
* Autodesk 3ds Max I/O 2021 uppdatering 3
* Autodesk Arnold för Maya 2020 (Arnold version 6.1.0.1) MtoA-4.1.1.1-2020
* Autodesk Arnold för 3ds Max 2021 (Arnold version 6.1.0.1) MAXtoA-4.2.2.20-2021
* Kaos Group V-Ray för Maya 2020 (version 5.00.21)
* Kaos Group V-Ray för 3ds Max 2021 (version 5.00.05)
* Blender (2.79)
* Över gång (2,80)
* AZ 10

> [!IMPORTANT]
> Om du vill köra V-Ray med Maya utanför [Azure Batch tilläggs mallar](https://github.com/Azure/batch-extension-templates)startar `vrayses.exe` du innan du kör renderingen. För att starta vrayses.exe utanför mallarna kan du använda följande kommando `%MAYA_2020%\vray\bin\vrayses.exe"` .
>
> Ett exempel finns i Start uppgiften för [Maya-och V-Ray-mallen](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) på GitHub.

## <a name="applications-on-previous-windows-server-rendering-images"></a>Program på tidigare Windows Server-återgivnings avbildningar

Följande lista gäller för Windows Server 2016, version 1.3.8 rendering images.

* Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459)
* Autodesk Maya I/O 2018 uppdatering 6 (version 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 uppdatering 1 (version 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 uppdatering 2
* Autodesk Arnold för Maya 2017 (Arnold version 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold för Maya 2019 (Arnold version 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold för 3ds Max 2018 (Arnold version 5.3.0.2) (version 1.2.926)
* Autodesk Arnold för 3ds Max 2019 (Arnold version 5.3.0.2) (version 1.2.926)
* Autodesk Arnold för 3ds Max 2020 (Arnold version 5.3.0.2) (version 1.2.926)
* Kaos Group V-Ray för Maya 2017 (version 4.12.01)
* Kaos Group V-Ray för Maya 2018 (version 4.12.01)
* Kaos Group V-Ray för Maya 2019 (version 4.04.03)
* Kaos Group V-Ray för 3ds Max 2018 (version 4.20.01)
* Kaos Group V-Ray för 3ds Max 2019 (version 4.20.01)
* Kaos Group V-Ray för 3ds Max 2020 (version 4.20.01)
* Blender (2.79)
* Över gång (2,80)
* AZ 10

Följande lista gäller för Windows Server 2016, version 1.3.7 rendering images.

* Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459)
* Autodesk Maya I/O 2018 uppdatering 4 (version 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 uppdatering 1 (version 21.2.0.2219)
* Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254)
* Autodesk Arnold för Maya 2017 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold för 3ds Max 2018 (Arnold version 5.0.2.4) (version 1.2.926)
* Autodesk Arnold för 3ds Max 2019 (Arnold version 5.0.2.4) (version 1.2.926)
* Kaos Group V-Ray för Maya 2018 (version 3.52.03)
* Kaos Group V-Ray för 3ds Max 2018 (version 3.60.02)
* Kaos Group V-Ray för Maya 2019 (version 3.52.03)
* Kaos Group V-Ray för 3ds Max 2019 (version 4.10.01)
* Blender (2.79)

> [!NOTE]
> Kaos Group V-Ray för 3ds Max 2019 (version 4.10.01) introducerar brytande ändringar i V-Ray. Använd den tidigare versionen (version 3.60.02) genom att använda Windows Server 2016, version 1.3.2 rendering Nodes.

## <a name="applications-on-previous-centos-rendering-images"></a>Program i tidigare CentOS åter givnings bilder

Följande lista gäller för CentOS 7,6, version 1.1.6 rendering images.

* Autodesk Maya I/O 2017 uppdatering 5 (version 201708032230)
* Autodesk Maya I/O 2018 uppdatering 2 (klipp ut 201711281015)
* Autodesk Maya I/O 2019 uppdatering 1
* Autodesk Arnold för Maya 2017 (Arnold version 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold för Maya 2019 (Arnold version 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray för Maya 2017 (version 3.60.04)
* Chaos Group V-Ray för Maya 2018 (version 3.60.04)
* Blender (2.68)
* Över gång (2,8)

## <a name="next-steps"></a>Nästa steg

Om du vill använda avbildningarna för rendering av virtuella datorer måste de anges i konfiguration av poolen när en pool skapas. Se [funktioner för batch-pool för rendering](./batch-rendering-functionality.md).
