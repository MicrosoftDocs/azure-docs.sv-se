---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98187214"
---
## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|**Azure Defender för behållar register** faktureras enligt [pris sidan](../articles/security-center/security-center-pricing.md)|
|Register och avbildningar som stöds:|Linux-avbildningar i ACR-register som är tillgängliga från det offentliga Internet med shell-åtkomst|
|Register och avbildningar som inte stöds:|Windows-avbildningar<br>Privata register<br>Register med begränsad åtkomst med en brand vägg, en tjänst slut punkt eller privata slut punkter som Azure Private-länk<br>Super-minimalist bilder, till exempel [Docker Scratch](https://hub.docker.com/_/scratch/) images, eller "Distroless"-avbildningar som bara innehåller ett program och dess körnings beroenden utan paket hanteraren, Shell eller OS|
|Nödvändiga roller och behörigheter:|**Säkerhets läsare** och [Azure Container Registry roller och behörigheter](../articles/container-registry/container-registry-roles.md)|
|Moln|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Kommersiella moln<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov-och Kina – gov stöds för närvarande inte. Läs mer i [när skannas bilder?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||