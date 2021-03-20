---
title: Azures plattforms integritet och säkerhet – Azure-säkerhet
description: Teknisk översikt över Azures plattforms integritet och säkerhet.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94558052"
---
# <a name="platform-integrity-and-security-overview"></a>Plattforms integritet och säkerhets översikt
Azure-flottan består av miljon tals servrar (värdar) med tusentals ytterligare tillagda per dag. Tusentals värdar genomgår också underhåll dagligen genom omstarter, uppdateringar av operativ system eller reparationer. Innan en värd kan ansluta till flottan och börja acceptera kund arbets belastningar, verifierar Microsoft att värden är i ett säkert och säkert tillstånd. Den här verifieringen säkerställer att skadliga eller oavsiktliga ändringar inte har inträffat i komponenter för startsekvens under en leverans kedja eller underhålls arbets flöden.

## <a name="securing-azure-hardware-and-firmware"></a>Skydda Azure-maskinvara och inbyggd program vara
I den här artikel serien beskrivs hur Microsoft garanterar integritet och säkerhet för värdar genom olika stadier i livs cykeln, från tillverkning till solnedgång. Artikel adressen:
 
- [Säkerhet för inbyggd programvara](firmware.md)
- [UEFI säker start](secure-boot.md)
- [Uppmätt start-och värd attestering](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Kryptering i vila](encryption-atrest.md)
- [Hypervisor-säkerhet](hypervisor.md)
 
## <a name="next-steps"></a>Nästa steg

- Lär dig hur Microsoft aktivt samarbetar inom moln maskin varu eko systemet för att driva kontinuerliga [förbättringar av inbyggd program vara](firmware.md).

- Förstå ditt [delade ansvar i molnet](shared-responsibility.md).