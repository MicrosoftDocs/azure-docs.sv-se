---
title: Integritet för inbyggd program vara – Azure-säkerhet
description: Läs om kryptografiska mått för att säkerställa den inbyggda program varans integritet.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557851"
---
# <a name="project-cerberus"></a>Project Cerberus

Cerberus är en NIST 800-193-kompatibel maskin varu rot med en identitet som inte kan klonas. Cerberus är utformat för att ytterligare öka säkerheten position i Azure-infrastrukturen genom att tillhandahålla ett starkt förtroende för den inbyggda program varans integritet.

## <a name="enabling-an-anchor-of-trust"></a>Aktivera ett förtroende ankare
Varje Cerberus-chip har en unik kryptografisk identitet som upprättas med hjälp av en signerad certifikat kedja som är rotad till en Microsoft-certifikatutfärdare (CA). Mätningar som erhålls från Cerberus kan användas för att verifiera integriteten för komponenter som:

- Värd
- Styrenhet för huvudkortshantering (BMC)
- All kring utrustning, inklusive nätverkskort och [System-on-a-chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SOC)

Den här fäst punkten för förtroende hjälper till att skydda plattformens inbyggda program vara från:

- Komprometterade binärfiler för inbyggd program vara som körs på plattformen
- Skadlig kod och hackare som utnyttjar buggar i operativ systemet, programmet eller hypervisor-programmet
- Vissa typer av leverans kedje attacker (tillverkning, sammansättning, överföring)
- Skadlig insides med administratörs behörighet eller åtkomst till maskin vara

## <a name="cerberus-attestation"></a>Cerberus-attestering
Cerberus autentiserar den inbyggda program varans integritet för Server komponenter med hjälp av en plattform för inbyggd program vara (PFM). PFM definierar en lista över tillåtna versioner av inbyggd program vara och tillhandahåller en plattforms mätning för [tjänsten Azure Host attestering](measured-boot-host-attestation.md). Tjänsten värd attestering verifierar mätningarna och gör en bestämning för att bara tillåta att betrodda värdar ansluter till Azure-flottan och värd kund arbets belastningar.

Tillsammans med tjänsten för värd attestering har Cerberus funktioner förbättra och befordrar en hög säker Azure-produktions infrastruktur.

> [!NOTE]
> Mer information finns i informationen om [Project Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) på GitHub.

## <a name="next-steps"></a>Nästa steg
Mer information om vad vi gör för att driva plattforms integritet och säkerhet finns i:

- [Säkerhet för inbyggd programvara](firmware.md)
- [Säker start](secure-boot.md)
- [Uppmätt start-och värd attestering](measured-boot-host-attestation.md)
- [Kryptering i vila](encryption-atrest.md)
- [Hypervisor-säkerhet](hypervisor.md)