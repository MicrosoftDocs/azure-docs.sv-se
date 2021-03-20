---
title: Säkerhet för inbyggd program vara – Azure-säkerhet
description: Lär dig hur Microsoft skyddar maskin vara och inbyggd program vara i Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94558044"
---
# <a name="firmware-security"></a>Säkerhet för inbyggd programvara
I den här artikeln beskrivs hur Microsoft skyddar moln maskin varu eko systemet och tillhandahålla kedjor.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Skydda moln maskin varans eko system
Microsoft aktivt partners inom moln maskin varu eko systemet för att driva fort löp ande säkerhets förbättringar genom att:

- Samar beta med Azures maskinvaru-och firmware-partner (till exempel komponent tillverkare och system integrerare) för att uppfylla Azures krav på maskin vara och inbyggd program vara.

- Att göra det möjligt för partner att utföra kontinuerlig utvärdering och förbättring av sina produkters säkerhets position med Microsofts definierade krav i områden som:

  - Säker start för inbyggd program vara
  - Säker återställning av inbyggd program vara
  - Säker uppdatering av inbyggd program vara
  - Kryptografi för inbyggd program vara
  - Låst maskin vara
  - Detaljerad fel sökning av telemetri
  - System stöd för TPM 2,0-maskinvara för att aktivera uppmätt start

- Att engagera i och bidra till säkerhets projektet [Open Compute Project (OCP)](https://www.opencompute.org/wiki/Security) genom utveckling av specifikationer. Specifikationerna främjar konsekvens och klarhet för säker design och arkitektur i eko systemet.

   > [!NOTE]
   > Ett exempel på vårt bidrag till OCP Security-projektet är [maskin varans säkra start](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) specifikation.

## <a name="securing-hardware-and-firmware-supply-chains"></a>Säkra kedjor för maskin vara och tillhandahållare av inbyggd program vara
Moln maskin varu leverantörer och leverantörer för Azure krävs också för att uppfylla säkerhets processer och krav för att tillhandahålla kedja som utvecklats av Microsoft. Utvecklings-och distributions processer för maskin vara och inbyggd program vara krävs för att följa sdl-processerna (Microsoft [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl) ), till exempel:

- Hotmodellering
- Säkra design granskningar
- Utvärdering av inbyggd program vara och inträngande testning
- Säkra bygg-och test miljöer
- Hantering av säkerhets risker och incident svar

## <a name="next-steps"></a>Nästa steg
Mer information om vad vi gör för att driva plattforms integritet och säkerhet finns i:

- [Säker start](secure-boot.md)
- [Uppmätt start-och värd attestering](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Kryptering i vila](encryption-atrest.md)
- [Hypervisor-säkerhet](hypervisor.md)