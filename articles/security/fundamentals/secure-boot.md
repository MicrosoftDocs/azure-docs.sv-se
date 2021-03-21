---
title: Inbyggd säker start för Azure – Azure-säkerhet
description: Teknisk översikt över säker start i Azure firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557850"
---
# <a name="secure-boot"></a>Säker start

Säker start är en funktion i [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) som kräver att alla inbyggda program vara och program varu komponenter på låg nivå verifieras innan de läses in. Vid start kontrollerar UEFI säker start signaturen för varje start program vara, inklusive UEFI-drivrutiner (även kallade alternativ Rom), Extensible Firmware Interface (EFI) och operativ system driv rutiner och binärfiler. Om signaturerna är giltiga eller betrodda av OEM-tillverkaren (Original Equipment Manufacturer) startar datorn och den inbyggda program varan ger kontroll över operativ systemet.

## <a name="components-and-process"></a>Komponenter och process

Säker start är beroende av dessa kritiska komponenter:

- Plattforms nyckel (PK) – etablerar förtroende mellan plattformens ägare (Microsoft) och den inbyggda program varan. Den offentliga halvan är PKpub och den privata halvan är PKpriv.
- Nyckel databas för nyckel registrering (KEK) – etablerar förtroende mellan operativ systemet och plattformens inbyggda program vara. Den offentliga halvan är KEKpub och den privata halvan är KEKpriv.
- Signature Database (dB) – innehåller sammanfattningar för betrodda undertecknare (offentliga nycklar och certifikat) för de inbyggda program varan och program kod moduler som har behörighet att interagera med plattformens inbyggda program vara.
- DBX (återkallat signaturer) – innehåller återkallade sammandrag av kodmoduler som har identifierats för att vara skadliga, sårbara, komprometterade eller ej betrodda. Om en hash finns i signaturen DB och den återkallade signaturen, så tar databasen med återkallade signaturer över överordnade.

Följande bild och process förklarar hur dessa komponenter uppdateras:

![Diagram som visar säkra start komponenter.](./media/secure-boot/secure-boot.png)

OEM-tillverkaren lagrar säkra startsammandrag på datorns icke-flyktiga RAM-minne (NV-RAM) under tillverknings tillfället.

1. Signature-databasen (dB) är fylld med undertecknarna eller avbildnings-hashar för UEFI-program, operativ systemets belastningsutjämnare (till exempel Microsoft-operativsystemet eller Boot Manager) och UEFI-drivrutiner som är betrodda.
2. Databasen för återkallade signaturer (DBX) är fylld med sammandrag av moduler som inte längre är betrodda.
3. KEK-databasen (Key Enrollment Key) fylls med signerings nycklar som kan användas för att uppdatera databasen för signaturen och den återkallade signaturen. Databaserna kan redige ras via uppdateringar som är signerade med rätt nyckel eller via uppdateringar av en fysiskt tillgänglig auktoriserad användare med hjälp av de inbyggda program varans menyer.
4. När databas-, DBX-och KEK-databaserna har lagts till och den slutliga valideringen av den inbyggda program varan har slutförts, låser OEM den inbyggda program varan från att redigera och genererar en plattforms nyckel (PK). PK kan användas för att signera uppdateringar till KEK eller inaktivera säker start.

Under varje steg i Start processen beräknas sammanfattningarna av den inbyggda program varan, Start programmet, operativ systemet, kernel-drivrutinerna och andra start kedjas artefakter och jämförs med acceptabla värden. Den inbyggda program varan och program vara som identifieras som ej betrodda tillåts inte att läsas in. Därför kan inmatningar av skadlig kod på låg nivå eller attacker mot skadlig kod blockeras.

## <a name="secure-boot-on-the-azure-fleet"></a>Säker start på Azure-flottan
Idag kommer varje dator som är inbyggd och distribuerad till Azure Compute-flottan som värd för kund arbets belastningar från fabriks golv med säker start aktiverat. Mål verktyg och-processer placeras i varje steg i pipeline för maskin varu utveckling och integrering för att säkerställa att säker start inte återställs antingen av misstag eller av skadligt syfte.

Verifierar att databas-och DBX-sammandrag är korrekta säkerställer:

- Start programmet finns i en av DB-posterna
- Start programmets signatur är giltig
- Värden startar med betrodd program vara

 Genom att validera signaturerna för KEKpub och PKpub kan vi bekräfta att endast betrodda parter har behörighet att ändra definitionerna för vilken program vara som anses vara betrodd. För att säkerställa att säker start är aktive rad kan vi till sist verifiera att dessa definitioner tillämpas.

## <a name="next-steps"></a>Nästa steg
Mer information om vad vi gör för att driva plattforms integritet och säkerhet finns i:

- [Säkerhet för inbyggd programvara](firmware.md)
- [Uppmätt start-och värd attestering](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Kryptering i vila](encryption-atrest.md)
- [Hypervisor-säkerhet](hypervisor.md)