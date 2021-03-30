---
title: Fysisk säkerhet för Azure-datacenter – Microsoft Azure | Microsoft Docs
description: I artikeln beskrivs vad Microsoft gör för att skydda Azure-datacenter, inklusive funktioner för fysisk infrastruktur, säkerhet och efterlevnad.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 3eac3c9abda74f0414fc97512f011d235d3991f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076317"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure-anläggningar, lokal och fysisk säkerhet
I den här artikeln beskrivs vad Microsoft gör för att skydda Azure-infrastrukturen.

## <a name="datacenter-infrastructure"></a>Data Center infrastruktur
Azure består av en [globalt distribuerad Data Center infrastruktur](https://azure.microsoft.com/global-infrastructure/), stöd för tusentals onlinetjänster och fler än 100 hög säkra funktioner över hela världen.

Infrastrukturen är utformad för att ge program närmare användare runtom i världen, bevara data placering och erbjuder omfattande alternativ för efterlevnad och återhämtning för kunder. Azure har 58 regioner i hela världen och är tillgänglig i 140 länder/regioner.

En region är en uppsättning data Center som är sammankopplade via ett massivt och elastiskt nätverk. Nätverket omfattar kryptering av innehålls distribution, belastnings utjämning, redundans och [data länks skikt som standard](encryption-overview.md#encryption-of-data-in-transit) för all Azure-trafik inom en region eller resa mellan regioner. Med fler globala regioner än någon annan moln leverantör ger Azure dig flexibiliteten att distribuera program där du behöver dem.

Azure-regioner är indelade i geografiska områden. En Azure-geografi säkerställer att data placering, suveränitet, efterlevnad och återhämtnings krav uppfylls inom geografiska gränser.

Geografiska områden hjälper kunder med specifika behov kring dataplacering och regelefterlevnad att hålla sina data och program nära. De geografiska områdena är feltoleranta för att motstå ett fullständigt region haveri, via anslutningen till den dedikerade nätverks infrastrukturen med hög kapacitet.

Tillgänglighets zoner är fysiskt separata platser inom en Azure-region. Varje tillgänglighetszon består av ett eller fler datacenter som är utrustade med oberoende ström, kylning och nätverkstjänster. Med tillgänglighets zoner kan du köra verksamhets kritiska program med hög tillgänglighet och replikering med låg latens.

Följande bild visar hur Azures globala infrastruktur par region och tillgänglighets zoner inom samma data placering-gränser för hög tillgänglighet, haveri beredskap och säkerhets kopiering.

![Diagram över data placering-gränser](./media/physical-security/data-residency-boundary.png)

Geografiskt distribuerade Data Center gör att Microsoft kan vara nära kunder, för att minska nätverks fördröjningen och tillåta Geo-redundant säkerhets kopiering och redundans.

## <a name="physical-security"></a>Fysisk säkerhet
Microsoft utformar, skapar och arbetar med data Center på ett sätt som strikt styr fysisk åtkomst till de områden där dina data lagras. Microsoft förstår vikten av att skydda dina data och är engagerade i att hjälpa till att skydda data Center som innehåller dina data. Vi har en hel avdelning på Microsoft som satsat på att utforma, skapa och driva fysiska anläggningar som stöder Azure. Det här teamet investeras i att underhålla den avancerade fysiska säkerheten.

Microsoft tar en lager metod för fysisk säkerhet, för att minska risken för obehöriga användare som får fysisk åtkomst till data och data Center resurser. Data Center som hanteras av Microsoft har omfattande skydds lager: åtkomst till godkännande på anläggningens perimeter, i byggnadens perimeter, inuti byggnaden och på Data Center golv. Lager med fysisk säkerhet är:

- **Åtkomstbegäran och godkännande.** Du måste begära åtkomst innan du kommer till data centret. Du måste ange en giltig affärs justering för ditt besök, till exempel efterlevnad eller gransknings syfte. Alla begär Anden godkänns på grund av att Microsoft-anställda behöver till gång till åtkomst. En förutsättning för att komma åt åtkomsten hjälper till att hålla antalet personer som behövs för att slutföra en aktivitet i data centret till minimalt minimum. När Microsoft har beviljat behörighet har en individ endast till gång till det diskreta området i data centret som krävs, baserat på den godkända affärs justeringen. Behörigheter är begränsade till en viss tids period och upphör sedan att gälla.

- **Funktionens perimeter.** När du kommer till ett Data Center är du tvungen att gå igenom en väldefinierad åtkomst punkt. Normalt sett utgörs höga avgränsningar av stål och betong i varje tum av perimeternätverket. Det finns kameror runt data centren, med ett säkerhets team som övervakar sina videor hela tiden.

- **Skapar ingång.** Data Center ingången bemannas med professionella säkerhets ansvariga som har genomgått rigorös utbildning och bakgrunds kontroller. Dessa säkerhets personal patrullerar även data centret och övervakar videor med kameror i data centret hela tiden.

- **Inuti byggnaden.** När du har angett byggnaden måste du skicka tvåfaktorautentisering med biometrik för att fortsätta flytta genom data centret. Om din identitet är verifierad kan du bara ange den del av data centret som du har godkänt åtkomst till. Du kan stanna bara under den tid som godkänns.

- **Data Center golv.** Du tillåts bara på den plan som du har godkänt att ange. Du måste skicka en fullständig kontroll av kroppens metall identifiering. För att minska risken för att obehöriga data matas in eller lämnar data centret utan vår kunskap kan endast godkända enheter göra sitt innehåll till data Center golv. Dessutom övervakar video kameror fram och tillbaka på varje server rack. När du avslutar Data Center golv måste du återigen gå igenom den fullständiga kontrollen av karosseriets identifiering. Om du vill lämna data centret måste du gå igenom en extra säkerhets sökning.

Microsoft kräver att besökarna har överlämnat skyltar vid avresa från någon av Microsofts anläggningar.

## <a name="physical-security-reviews"></a>Fysiska säkerhets granskningar
Med jämna mellanrum genomför vi fysiska säkerhets granskningar av anläggningarna för att säkerställa att data Center på rätt sätt hanterar Azures säkerhets krav. Data centret som är värd för tjänst leverantören tillhandahåller inte Azure Service Management. Personalen kan inte logga in på Azure-system och har inte fysisk åtkomst till Azure samplacering-rummet och burar.

## <a name="data-bearing-devices"></a>Data bär ande enheter
Microsoft använder metoder för bästa praxis och en rensnings lösning som är [NIST 800-88-kompatibel](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). För hård diskar som inte kan rensas använder vi en destruktions process som förstör den och gör det omöjligt att återställa information. Den här förstörelse processen kan vara disintegrate, Shred, pulverize eller förbränning. Vi fastställer hur avyttringen ska utföras enligt till gångs typen. Vi behåller registren över förstöringen.  

## <a name="equipment-disposal"></a>Kasse ring av utrustning
På ett Systems livs längd följer Microsofts operativa personal rigorösa data hanterings-och avlopps metoder för att säkerställa att maskin varan som innehåller dina data inte görs tillgänglig för ej betrodda parter. Vi använder en säker borttagnings metod för hård diskar som stöder det. För hård diskar som inte kan rensas använder vi en förstörelse process som förstör enheten och gör det omöjligt att återställa information. Den här förstörelse processen kan vara disintegrate, Shred, pulverize eller förbränning. Vi fastställer hur avyttringen ska utföras enligt till gångs typen. Vi behåller registren över förstöringen. Alla Azure-tjänster använder godkända medier för lagring och hantering av tjänster.

## <a name="compliance"></a>Efterlevnad
Vi utformar och hanterar Azure-infrastrukturen för att uppfylla en rad olika internationella och branschspecifika efterlevnadsprinciper, till exempel ISO 27001, HIPAA, FedRAMP, SOC 1 och SOC 2. Vi uppfyller även lands-eller landsspecifika standarder, inklusive Australien IRAP, Storbritannien G-Cloud och Singapore MTCS. Rigorösa revisioner från tredje part, till exempel de som gjorts av brittiska standard Institutet, kontrollerar att de strikta säkerhets kontrollerna efterlever dessa standarder.

En fullständig lista över efterlevnads standarder som Azure följer finns i [Compliance-erbjudandena](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)


