---
title: Hög tillgänglighet – Azure Dedicated HSM | Microsoft Docs
description: Lär dig mer om grundläggande överväganden för Azure Dedicated HSM med hög tillgänglighet. Den här artikeln innehåller ett exempel.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c46ccbda35936ed94079a21e3e9c72405875b961
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608875"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedicated HSM med hög tillgänglighet

Azure Dedicated HSM är underfixerat av Microsofts Data Center med hög tillgänglighet. Ett Data Center med hög tillgänglighet är dock sårbart för lokaliserade haverier och i extrema fall, regional nivå problem. Microsoft distribuerar HSM-enheter i olika data Center inom en region för att säkerställa att flera enheter inte leder till att de delar en enda rack-enhet. Ytterligare en hög tillgänglighets nivå kan uppnås genom att para ihop dessa HSM: er över data Center i en region med hjälp av grupp funktionen Thales HA. Det är också möjligt att para ihop enheter i regioner för att hantera regional redundans vid haveri beredskap. Med den här konfigurationen med hög tillgänglighet för flera lager, kommer alla enhets problem att åtgärdas automatiskt för att hålla program att fungera. Alla data Center har också reserv enheter och komponenter på plats så att eventuella felande enheter kan ersättas i rätt tid.

## <a name="high-availability-example"></a>Exempel på hög tillgänglighet

Information om hur du konfigurerar HSM-enheter för hög tillgänglighet på program nivå finns i administrations hand boken för Thales Luna 7 HSM. Det här dokumentet är tillgängligt på  [HSM-sidan för Thales](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms).

Följande diagram visar en arkitektur med hög tillgänglighet. Den använder flera enheter i region och flera enheter länkas i en separat region. Den här arkitekturen använder minst fyra HSM-enheter och virtuella nätverks komponenter.

![Diagram med hög tillgänglighet](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet, är väl förförståde före enhets etablering och program design eller distribution.
Avsnitt om ytterligare begrepps nivå:

* [Distributions arkitektur](deployment-architecture.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)

Detaljerad information om hur du konfigurerar HSM-enheter för hög tillgänglighet finns i [Thales kund support Portal](https://supportportal.thalesgroup.com/csm) för administratörs guider och se avsnitt 6.
