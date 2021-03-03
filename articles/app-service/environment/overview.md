---
title: Översikt över App Service-miljön
description: Översikt över App Service-miljön
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 03/02/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 23b23340550ded3642d19500270f06cfb6faf8cb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735105"
---
# <a name="app-service-environment-overview"></a>Översikt över App Service-miljön 

> [!NOTE]
> Den här artikeln gäller App Service-miljön v3 (för hands version)
> 

Azure App Service-miljön är en funktion i Azure App Service som ger en helt isolerad och dedikerad miljö där du kan köra App Service-appar säkert på hög nivå. Den här funktionen kan vara värd för:

- Windows-webbappar
- Linux-webbappar
- Docker-containrar
- Functions

App Service-miljöer (ASE) är lämpliga för programarbetsbelastningar som kräver:

- Hög skala.
- Isolering och säker nätverksåtkomst.
- Hög minnesanvändning.
- Högsta antal begär Anden per sekund (RPS). Du kan göra flera ASE i en enda Azure-region eller i flera Azure-regioner. Den här flexibiliteten gör ASE perfekt för horisontell skalning av tillstånds lösa program med ett högt RPS-krav.

ASE är värd program från endast en kund och gör det i någon av deras virtuella nätverk. Kunderna har detaljerad kontroll över inkommande och utgående programnätverkstrafik. Programmen kan upprätta säkra anslutningar med hög hastighet över VPN till lokala företagsresurser.

ASEv3 levereras med en egen pris nivå, isolerad v2.
App Service miljöer v3 ger en omgivande miljö för att skydda dina appar i ett undernät i nätverket och ger din egen privata distribution av Azure App Service.
Flera ASE-miljöer kan användas för att skala vågrätt. Appar som körs i ASE kan ha sin egen åtkomst begränsad av överordnade enheter, t.ex. brandväggar för webbaserade program (WAF). Mer information finns i Brandvägg för webbaserade program (WAF).

## <a name="usage-scenarios"></a>Användningsscenarier

App Service-miljön har många användnings fall, inklusive:

- Interna affärs program
- Program som behöver fler än 30 ASP-instanser
- Ett enda klient system för att uppfylla interna krav på efterlevnad eller säkerhet
- Nätverks isolerad program värd
- Program på flera nivåer

Det finns ett antal nätverksfunktioner som gör det möjligt för appar i flera klient organisationer App Service att komma åt isolerade nätverks resurser eller bli isolerade i nätverket. Dessa funktioner är aktiverade på program nivå.  Med en ASE finns det ingen ytterligare konfiguration av apparna för dem i VNet. Apparna distribueras till en isolerad nätverks miljö som redan finns i ett VNet. På den ASE som är värd för isolerade isolerade appar är det också ett system med en enda klient. Det finns inga andra kunder som använder ASE. Om du verkligen behöver en fullständig isolerings berättelse kan du också få din ASE distribuerad till dedikerad maskin vara. Mellan isolerade nätverks program värd, enda innehav och möjlighet 

## <a name="dedicated-environment"></a>Dedikerad miljö
En ASE är uteslutande avsedd för en enda prenumeration och kan vara värd för 200 total App Service plans instanser över flera App Services planer. Ordet "instance" refererar till App Service plan horisontell skalning. Varje instans motsvarar en arbets roll. En ASE kan ha 200 totalt antal instanser, men en enda isolerad v2-App Service plan kan innehålla 100-instanser. ASE kan innehålla två App Service planer med 100 instanser i varje, 200 instans App Services planer eller allt mellan.

En ASE-miljö består av klientdelar och arbetare. Klientdelarna ansvarar för HTTP/HTTPS-avslutning och automatisk belastningsutjämning av appförfrågningar i en ASE-miljö. Klientdelarna läggs till automatiskt när App Service-planerna i ASE skalas ut.

Arbetare är roller som är värdar för kundappar. Arbetare finns i tre fasta storlekar:

- Två vCPU/8 GB RAM
- Fyra vCPU/16 GB RAM-minne
- Åtta vCPU/32 GB RAM

Kunderna behöver inte hantera klient delar och arbetare. All infrastruktur sker automatiskt. Vartefter App Service-planer skapas eller skalas i en ASE-miljö kommer den infrastruktur som krävs att läggas till eller tas bort efter behov.

Det finns en avgift för isolerade v2 App Service plan-instanser. Om du inte har några App Service-planer alls i din ASE debiteras du som om du hade en App Service plan med en instans av de två kärn arbetarna.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk
ASE-funktionen är en distribution av Azure App Service direkt till kundens Azure Resource Manager virtuella nätverk. En ASE finns alltid i ett undernät för ett virtuellt nätverk. Du kan använda säkerhetsfunktionerna för virtuella nätverk för att styra inkommande och utgående nätverkskommunikation för apparna.

Nätverkssäkerhetsgrupper begränsar inkommande kommunikation till undernätet där en ASE-miljö finns. Du kan använda nätverkssäkerhetsgrupper för att köra appar bakom överordnade enheter och tjänster, t.ex. WAF och SaaS-nätverksleverantörer.

Apparna måste ofta även komma åt företagsresurser, t.ex. interna databaser och webbtjänster. Om du distribuerar ASE-miljön i ett virtuellt nätverk som har en VPN-anslutning till det lokala nätverket kan apparna i ASE-miljön komma åt de lokala resurserna. Den här funktionen gäller oavsett om VPN är en VPN för plats-till-plats eller Azure ExpressRoute.

## <a name="preview"></a>Förhandsgranskning
App Service-miljön v3 är i offentlig för hands version.  Vissa funktioner läggs till under för hands versions förloppet. De aktuella begränsningarna i ASEv3 är:

- Det går inte att skala en App Service plan bortom 50 instanser
- Det går inte att hämta en behållare från ett privat register
- Oförmåga för för tillfället App Service funktioner som inte stöds för att gå via kundens VNet
- Ingen extern distributions modell med en tillgänglig slut punkt på Internet
- Inget kommando rads stöd (AZ CLI och PowerShell)
- Ingen uppgraderings kapacitet från ASEv2 till ASEv3
- Inget stöd för FTP
- Inget stöd för vissa App Service funktioner som går via kundens VNet. Säkerhets kopiering/återställning, Key Vault referenser i appinställningar, med hjälp av ett privat behållar register och diagnostisk loggning till lagring fungerar inte med tjänstens slut punkter eller privata slut punkter
    
### <a name="asev3-preview-architecture"></a>ASEv3 Preview-arkitektur
I ASEv3 för hands version kommer ASE att använda privata slut punkter för att stödja inkommande trafik. Den privata slut punkten kommer att ersättas med belastningsutjämnare enligt GA. I för hands versionen har ASE inte inbyggt stöd för en tillgänglig slut punkt för Internet. Du kan lägga till en Application Gateway för detta ändamål. ASE behöver resurser i två undernät.  Inkommande trafik flödar genom en privat slut punkt. Den privata slut punkten kan placeras i ett undernät, så länge den har en tillgänglig adress som kan användas av privata slut punkter.  Det utgående under nätet måste vara tomt och delegerat till Microsoft. Web/hostingEnvironments. När det används av ASE kan inte det utgående under nätet användas för något annat.

Med ASEv3 finns det inga inkommande eller utgående nätverks krav i ASE-undernätet. Du kan styra trafiken med nätverks säkerhets grupper och routningstabeller och den påverkar bara din program trafik. Ta inte bort den privata slut punkten som är associerad med din ASE eftersom det inte går att ångra åtgärden. Den privata slut punkt som används för ASE används för alla appar i ASE. 
