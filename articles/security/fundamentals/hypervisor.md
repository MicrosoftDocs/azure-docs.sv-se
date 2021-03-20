---
title: Hypervisor-säkerhet i Azure-flottan – Azure-säkerhet
description: Teknisk översikt över hypervisor-säkerhet på Azure-flottan.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696124"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Hypervisor-säkerhet på Azure-flottan

Azure hypervisor-systemet är baserat på Windows Hyper-V. Hypervisor-systemet gör det möjligt för dator administratören att ange gäst partitioner som har separata adress utrymmen. Med de separata adress utrymmena kan du läsa in ett operativ system och program som körs parallellt med operativ systemet (värd) som körs i datorns rotdomän. Värd operativ systemet (även kallat Privileged root partition) har direkt åtkomst till alla fysiska enheter och kring utrustning i systemet (lagrings styrenheter, nätverks anpassningar). Värd operativ systemet tillåter att gäst partitioner delar användningen av dessa fysiska enheter genom att exponera "virtuella enheter" för varje gäst partition. Därför har ett operativ system som körs i en gäst-partition åtkomst till virtualiserade kring utrustnings enheter som tillhandahålls av Virtualization Services som körs i rotnoden.

Azure-hypervisorn bygger på följande säkerhets mål i åtanke:

| Mål | Källa |
|--|--|
| Isolering | En säkerhets princip bestämmer ingen informations överföring mellan virtuella datorer. Den här begränsningen kräver funktioner i Virtual Machine Manager (VMM) och maskin vara för isolering av minne, enheter, nätverk och hanterade resurser, till exempel sparade data. |
| VMM-integritet | För att uppnå den övergripande system integriteten upprättas och underhålls integriteten för enskilda hypervisor-komponenter. |
| Plattforms integritet | Integriteten för hypervisorn beror på integriteten hos den maskin vara och program vara som den förlitar sig på. Även om hypervisorn inte har direkt kontroll över plattformens integritet, är Azure beroende av maskinvaru-och firmware-mekanismer som [Cerberus](project-cerberus.md) -kretsen för att skydda och identifiera den underliggande plattforms integriteten. VMM och gäster hindras från att köras om plattforms integriteten är komprometterad. |
| Begränsad åtkomst | Hanterings funktioner utövas endast av auktoriserade administratörer som är anslutna via säkra anslutningar. En princip med minsta behörighet tillämpas av Azure RBAC-mekanismer (rollbaserad åtkomst kontroll). |
| Granska | Med Azure kan gransknings funktioner fånga och skydda data om vad som händer i ett system, så att det kan granskas senare. |

Microsofts metod för att skärpa Azure-hypervisorn och under systemet för virtualisering kan delas upp i följande tre kategorier.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Starkt definierade säkerhets gränser som framtvingas av hypervisorn

Azure-hypervisorn tillämpar flera säkerhets gränser mellan:

- Virtualiserade "gäst" partitioner och privilegie rad partition ("värd")
- Flera gäster
- Sig själv och värden
- Sig själv och alla gäster

Konfidentialitet, integritet och tillgänglighet är garanterat för säkerhets gränserna i hypervisor-skyddet. Gränserna skyddar mot en mängd attacker, inklusive information om sido kanals information, Denial-of-service och höjning av privilegier.

Hypervisor-säkerhetsgränser tillhandahåller också segmentering mellan klienter för nätverks trafik, virtuella enheter, lagring, beräknings resurser och alla andra VM-resurser.

## <a name="defense-in-depth-exploit-mitigations"></a>Skydd mot djupgående sårbarhet

I det osannolika fallet en säkerhets begränsning har en säkerhets risk, innehåller Azure-hypervisorn flera lager av lösningar, inklusive:

- Isolering av värdbaserade processer som är värdar för olika VM-komponenter
- Virtualiseringsbaserad säkerhet (VBS) för att säkerställa integriteten för användar-och kernel-läges komponenter från en säker värld
- Flera nivåer av sårbarhets minskning. Åtgärder är bland annat layout för adress utrymmes slumpering (ASLR), dataexekveringsskydd (DEP), skadlig kod Guard, kontroll flödes integritet och förhindrande av datafel
- Automatisk initiering av stack-variabler på kompilator nivån
- Kernel-API: er som automatiskt nollställer kernel heap-allokeringar som görs av Hyper-V

De här lösningarna är utformade för att utveckla en sårbarhet för en sårbarhet som är möjlig mellan virtuella datorer.

## <a name="strong-security-assurance-processes"></a>Starka säkerhets säkrings processer

Angrepps ytan som är relaterad till hypervisor-programmet innefattar program varu nätverk, virtuella enheter och alla platser för flera virtuella datorer. Angrepps ytan spåras via automatiserad build-integrering, som utlöser regelbundna säkerhets granskningar.

Alla virtuella dator attacker är Hot modellerade, kod granskad, suddig och testad av vårt RED-team för säkerhets gränser. Microsoft har ett [fel Bounty-program](https://www.microsoft.com/msrc/bounty-hyper-v) som ger en utmärkelse för relevanta sårbarheter i kvalificerade produkt versioner för Microsoft Hyper-V.

> [!NOTE]
> Läs mer om [starka säkerhets garanti processer](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) i Hyper-V.

## <a name="next-steps"></a>Nästa steg
Mer information om vad vi gör för att driva plattforms integritet och säkerhet finns i:

- [Säkerhet för inbyggd programvara](firmware.md)
- [Säker start](secure-boot.md)
- [Uppmätt start-och värd attestering](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Kryptering i vila](encryption-atrest.md)