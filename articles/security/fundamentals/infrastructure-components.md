---
title: Komponenter och gränser för Azure information system
description: Den här artikeln innehåller en allmän beskrivning av Microsoft Azure arkitektur och hantering.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "68727198"
---
# <a name="azure-information-system-components-and-boundaries"></a>Komponenter och gränser för Azure information system
Den här artikeln innehåller en allmän beskrivning av Azure-arkitekturen och-hanteringen. Azures system miljö består av följande nätverk:

- Microsoft Azure produktions nätverk (Azure-nätverk)
- Microsoft företags nätverk (Corpnet)

Separata IT-team ansvarar för drift och underhåll av dessa nätverk.

## <a name="azure-architecture"></a>Azure-arkitektur
Azure är en plattform för molnbaserad data behandling och infrastruktur för att skapa, distribuera och hantera program och tjänster via ett nätverk av data Center. Microsoft hanterar dessa data Center. Baserat på antalet resurser som du anger, skapar Azure virtuella datorer (VM) baserat på resurs behov. De här virtuella datorerna körs på en Azure-hypervisor, som är avsedd att användas i molnet och inte är tillgänglig för allmänheten.

På varje fysisk Azure-servernod finns en hypervisor som körs direkt över maskin varan. Hypervisorn delar upp en nod i ett variabel antal virtuella gäst datorer. Varje nod har också en virtuell rot-VM, som kör värd operativ systemet. Windows-brandväggen är aktive rad på varje virtuell dator. Du definierar vilka portar som kan adresseras genom att konfigurera tjänst definitions filen. Dessa portar är de enda som är öppna och adresser bara internt eller externt. All trafik och åtkomst till disken och nätverket har åtgärd ATS av hypervisorn och rot operativ systemet.

På värdnivå kör virtuella Azure-datorer en anpassad och skärpd version av den senaste Windows-servern. Azure använder en version av Windows Server som endast innehåller de komponenter som krävs för att vara värdar för virtuella datorer. Detta förbättrar prestanda och minskar angrepps ytan. Dator gränserna tillämpas av hypervisorn, som inte är beroende av operativ systemets säkerhet.

### <a name="azure-management-by-fabric-controllers"></a>Azure-hantering av infrastruktur styrenheter

I Azure grupperas virtuella datorer som körs på fysiska servrar (blad/noder) i kluster om 1000. De virtuella datorerna hanteras oberoende av en skalbar och redundant plattforms program varu komponent som kallas Fabric Controller (FC).

Varje FC hanterar livs cykeln för program som körs i sitt kluster och etablerar och övervakar hälso tillståndet för maskin varan under dess kontroll. Den kör Autonomic-åtgärder, till exempel reincarnating VM-instanser på felfria servrar när det fastställer att en server har misslyckats. FC utför även program hanterings åtgärder, till exempel att distribuera, uppdatera och skala ut program.

Data centret är indelat i kluster. Kluster isolerar fel på FC-nivå och förhindrar vissa fel klasser från att påverka servrar utöver klustret där de inträffar. FCs som hanterar ett visst Azure-kluster grupperas i ett FC-kluster.

### <a name="hardware-inventory"></a>Maskinvaruinventering

FC förbereder en inventering av Azures maskinvaru-och nätverks enheter under start konfigurations processen. Alla nya maskin-och nätverks komponenter som du anger i Azures produktions miljö måste följa start konfigurations processen. FC ansvarar för att hantera hela inventeringen som anges i datacenter.xml konfigurations filen.

### <a name="fc-managed-operating-system-images"></a>FC-hanterade operativ Systems avbildningar

Operativ system teamet tillhandahåller avbildningar i form av virtuella hård diskar som distribueras på alla virtuella värd-och gäst datorer i produktions miljön i Azure. Gruppen konstruerar de här bas avbildningarna genom en automatiserad process för skapande av offline. Bas avbildningen är en version av operativ systemet där kernel och andra kärn komponenter har ändrats och optimerats för att stödja Azure-miljön.

Det finns tre typer av Fabric-hanterade operativ Systems avbildningar:

- Värd: ett anpassat operativ system som körs på virtuella värddatorer.
- Inbyggt: ett inbyggt operativ system som körs på klient organisationer (till exempel Azure Storage). Det här operativ systemet har ingen hypervisor.
- Gäst: ett gäst operativ system som körs på virtuella gäst datorer.

Värden och de virtuella FC-hanterade operativ systemen är utformade för användning i molnet och är inte offentligt tillgängliga.

#### <a name="host-and-native-operating-systems"></a>Värd och inbyggda operativ system

Värd och inbyggd är härdade operativ system avbildningar som är värdar för Fabric-agenterna och körs på en Compute-nod (körs som den första virtuella datorn på noden) och lagringsnoder. Fördelen med att använda optimerade bas avbildningar av värd och inbyggd är att den minskar ytan som exponeras av API: er eller oanvända komponenter. Detta kan innebära höga säkerhets risker och öka operativ systemets operativ system. Operativ system med minskad storlek innehåller bara de komponenter som krävs för Azure.

#### <a name="guest-operating-system"></a>Gästoperativsystem

Interna Azure-komponenter som körs på gäst operativ systemets virtuella datorer har ingen möjlighet att köra Remote Desktop Protocol. Ändringar av inställningarna för bas linje konfiguration måste gå igenom processen för hantering av ändringar och versioner.

## <a name="azure-datacenters"></a>Azure-datacenter
MCIO-teamet (Microsoft Cloud Infrastructure and Operations) hanterar den fysiska infrastrukturen och data Center funktionerna för alla Microsoft-onlinetjänster. MCIO är primärt ansvarig för att hantera de fysiska och miljömässiga kontrollerna i data centren, samt hantera och stödja yttre perimeternätverk (till exempel yttre routrar och datacenter-routrar). MCIO ansvarar också för att ställa in den minimala server maskin varan på rack i data centret. Kunder har ingen direkt interaktion med Azure.

## <a name="service-management-and-service-teams"></a>Tjänst hantering och tjänst team
Olika teknik grupper, kända som service team, hanterar supporten för Azure-tjänsten. Varje tjänst team ansvarar för ett support utrymme för Azure. Varje tjänst team måste göra en tekniker tillgänglig dygnet runt för att undersöka och lösa problem i tjänsten. Tjänst teamen har som standard inte fysisk åtkomst till maskin varan i Azure.

Tjänst teamen är:

- Program plattform
- Azure Active Directory
- Azure Compute
- Azure net
- Moln teknik tjänster
- ISSD: säkerhet
- Multifaktorautentisering
- SQL Database
- Storage

## <a name="types-of-users"></a>Typer av användare
Anställda (eller entreprenörer) av Microsoft anses vara interna användare. Alla andra användare anses vara externa användare. Alla interna Azure-användare har sin medarbetar status Kategoriserad med en känslighets nivå som definierar deras åtkomst till kund information (åtkomst eller ingen åtkomst). Användar behörigheter till Azure (behörigheten behörighet efter autentisering) beskrivs i följande tabell:

| Roll | Intern eller extern | Känslighets nivå | Auktoriserade behörigheter och funktioner som utförs | Åtkomst typ
| --- | --- | --- | --- | --- |
| Azure Data Center-tekniker | Intern | Ingen åtkomst till kund information | Hantera den fysiska säkerheten för lokalen. Utför patruller i och ut ur data centret och övervaka alla start punkter. Escort in och ut ur data centret viss icke-rensad personal som tillhandahåller allmänna tjänster (till exempel matsal eller rengöring) eller fungerar i data centret. Genomför rutin övervakning och underhåll av nätverks maskin vara. Utföra incident hantering och bryta åtgärds arbete med hjälp av en rad olika verktyg. Utför rutinmässig övervakning och underhåll av den fysiska maskin varan i data centren. Åtkomst till miljön på begäran från egenskaps ägare. Kan utföra kriminal tekniska-undersökningar, logga incident rapporter och kräva obligatorisk säkerhets utbildning och princip krav. Drifts ägande och underhåll av kritiska säkerhets verktyg, till exempel skannrar och logg insamling. | Beständig åtkomst till miljön. |
| Azure incident prioritering (Rapid Response Engineers) | Intern | Åtkomst till kund information | Hantera kommunikation mellan MCIO, support och teknik team. Prioritering plattforms incidenter, distributions problem och tjänst begär Anden. | Just-in-Time-åtkomst till miljön med begränsad beständiga åtkomst till icke-kund system. |
| Azure-distributions tekniker | Intern | Åtkomst till kund information | Distribuera och uppgradera plattforms komponenter, program vara och schemalagda konfigurations ändringar i stöd för Azure. | Just-in-Time-åtkomst till miljön med begränsad beständiga åtkomst till icke-kund system. |
| Support för Azure-kund avbrott (klient organisation) | Intern | Åtkomst till kund information | Felsök och diagnostisera plattforms avbrott och fel för enskilda Compute-klienter och Azure-konton. Analysera fel. Enhets kritiska korrigeringar till plattformen eller kunden, samt för att driva tekniska förbättringar av supporten. | Just-in-Time-åtkomst till miljön med begränsad beständiga åtkomst till icke-kund system. |
| Azure Live site Engineers (övervaknings tekniker) och incident | Intern | Åtkomst till kund information | Diagnostisera och minska plattformens hälsa med hjälp av diagnostikverktyg. Enhets korrigeringar för volym driv rutiner, reparera objekt som uppstår på grund av avbrott och hjälp vid återställnings åtgärder. | Just-in-Time-åtkomst till miljön med begränsad beständiga åtkomst till icke-kund system. |
|Azure-kunder | Extern | Saknas | Saknas | Saknas |

Azure använder unika identifierare för att autentisera organisations användare och kunder (eller processer som agerar på uppdrag av organisations användare). Detta gäller för alla till gångar och enheter som ingår i Azure-miljön.

### <a name="azure-internal-authentication"></a>Intern Azure-autentisering

Kommunikation mellan interna Azure-komponenter skyddas med TLS-kryptering. I de flesta fall är X. 509-certifikaten självsignerade. Certifikat med anslutningar som kan nås från utanför Azure-nätverket är ett undantag, som är certifikat för FCs. FCs har certifikat som utfärdats av ett Microsoft-certifikatutfärdare (CA) som backas upp av en betrodd rot certifikat utfärdare. På så sätt kan du enkelt distribuera offentliga nycklar i FC. Microsoft Developer Tools använder dessutom FC-offentliga nycklar. När utvecklare skickar nya program avbildningar krypteras avbildningarna med en offentlig FC-nyckel för att skydda eventuella inbäddade hemligheter.

### <a name="azure-hardware-device-authentication"></a>Autentisering av Azure-maskinvarubaserad enhet

FC upprätthåller en uppsättning autentiseringsuppgifter (nycklar och/eller lösen ord) som används för att autentisera sig på olika maskin varu enheter under dess kontroll. Microsoft använder ett system för att förhindra åtkomst till dessa autentiseringsuppgifter. Mer specifikt är transport, persistens och användning av dessa autentiseringsuppgifter utformade för att förhindra Azure-utvecklare,-administratörer och säkerhets kopierings tjänster och personal åtkomst till känslig, konfidentiell eller privat information.

Microsoft använder kryptering baserat på FC: s huvudsakliga identitet offentliga nyckel. Detta inträffar vid FC-installationen och i FC-omkonfigurations tider för att överföra de autentiseringsuppgifter som används för att komma åt nätverks maskin varu enheter. När FC behöver autentiseringsuppgifterna hämtar och dekrypterar FC dem.

### <a name="network-devices"></a>Nätverks enheter

Azure Networking-teamet konfigurerar nätverks tjänst konton så att en Azure-klient kan autentisera till nätverks enheter (routrar, växlar och belastningsutjämnare).

## <a name="secure-service-administration"></a>Säker tjänst administration
Azure Operations-personal krävs för att använda en säker administratörs arbets Station (sågar). Kunder kan implementera liknande kontroller med hjälp av arbets stationer med privilegie rad åtkomst. Med sågar använder administrativ personal ett individuellt tilldelat administrativt konto som är skilt från användarens standard användar konto. De såg versioner som bygger på konto avgränsnings praxis genom att tillhandahålla en tillförlitlig arbets station för dessa känsliga konton.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
