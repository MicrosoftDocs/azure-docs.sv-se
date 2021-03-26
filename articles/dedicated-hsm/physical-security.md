---
title: HSM fysisk säkerhet – Azure Dedicated HSM | Microsoft Docs
description: Information om Azure Dedicated HSM-enheters fysiska säkerhet i Data Center
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
ms.openlocfilehash: 8437d12075a148a3e8062f62e195bc019d89a85b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606971"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Fysisk säkerhet för Azure Dedicated HSM

Azure Dedicated HSM hjälper dig att uppfylla avancerade säkerhets krav för nyckel lagring. Den hanteras enligt strikta säkerhets metoder i hela livs cykeln för att möta kundernas behov.

## <a name="security-through-procurement"></a>Säkerhet genom inköp

Microsoft följer en säker upphandlings process. Vi hanterar en kedja av vårdnaden och ser till att den enhet som beställs och levereras är enheten som kommer till våra data Center. Enheterna är i serialiserade plast säckar och behållare för manipulering av manipulering. De lagras i ett säkert lagrings utrymme tills de har beställts i data galleriet i data centret.  Racken som innehåller HSM-enheterna betraktas som hög inverkan på företaget (HBI). Enheterna är låsta och under video övervakningen alltid fram och tillbaka.

## <a name="security-through-deployment"></a>Säkerhet via distribution

HSM: er installeras i rack tillsammans med tillhör ande nätverks komponenter. När de har installerats måste de konfigureras innan de görs tillgängliga som en del av Azure Dedicated HSM-tjänsten. Den här konfigurations aktiviteten utförs av Microsoft-anställda som har genomgått en bakgrunds kontroll. JIT-administration (just in Time) används för att begränsa åtkomsten till endast de anställda och endast för den tid som åtkomst krävs. De procedurer och system som används ser också till att all aktivitet som är relaterad till HSM-enheterna loggas.

## <a name="security-in-operations"></a>Säkerhet i åtgärder

HSM: er är maskin varu installationer (den faktiska HSM är ett PCI-kort i installationen), så det är möjligt att problem med komponent nivån kan uppstå. Potentiella problem är, men är inte begränsade till fläkt-och ström källor. Den här typen av händelse kräver underhålls-eller åtgärds-/åtgärds aktiviteter för att ersätta eventuella växlings bara komponenter.

### <a name="component-replacement"></a>Komponent utbyte

När en enhet har tillhandahållits och under kund hantering är den utbytbara strömförsörjningen den enda komponenten som skulle ersättas. Den här komponenten ligger utanför säkerhets gränserna och orsakar ingen manipulering. Ett biljett system används för att ge en Microsoft-tekniker åtkomst till bak sidan av HBI-racket. När biljetten bearbetas utfärdas en tillfällig fysisk nyckel. Den här nyckeln ger teknikern åtkomst till enheten och gör det möjligt för dem att byta ut den berörda komponenten. All annan åtkomst (det vill säga manipulering av manipulering) görs när en enhet inte allokeras till en kund, vilket minimerar säkerhets-och tillgänglighets risken.  

### <a name="device-replacement"></a>Enhets ersättning

I händelse av totalt enhets haveri följs en process som liknar den som användes vid komponent haveri. Om en kund inte kan zeroize enheten, eller om enheten är i ett okänt tillstånd, tas data som bär ande enheter bort och placeras i en beställnings plats för destruktion. Enheter som placeras på lager platsen kommer att förstöras på ett kontrollerat och säkert sätt. Inga data som bär enheter från ett HBI-rack lämnar ett Microsoft-datacenter.

### <a name="other-rack-access-activities"></a>Andra rack åtkomst aktiviteter

Om en Microsoft-tekniker måste komma åt racket som används av HSM-enheter (t. ex. nätverks enhets underhåll) kommer standard säkerhets procedurer att användas för att få åtkomst till HBI Secure rack. All åtkomst kommer under video övervakning. HSM-enheterna verifieras till [FIPS 140-2 nivå 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) , så all obehörig åtkomst till HSM-enheterna kommer att signaleras till kunden och data kommer att nollställas.

## <a name="logical-level-security-considerations"></a>Säkerhets överväganden för logiska nivåer

HSM: er är etablerade i ett virtuellt nätverk som har skapats av kunden inom kundens privata IP-adressutrymme.  Den här konfigurationen ger en värdefull isolering på logiskt nätverk och garanterar endast åtkomst av kunden. Det innebär att alla säkerhets kontroller på logisk nivå är kundens ansvar.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet och support, till exempel är bra förstå före enhets etablering, program design eller distribution.

* [Hög tillgänglighet](high-availability.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
* [Distributions arkitektur](deployment-architecture.md)
