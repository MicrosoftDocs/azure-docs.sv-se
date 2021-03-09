---
title: Underhåll och uppdateringar
description: Översikt över underhåll och uppdateringar för virtuella datorer som körs i Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 51df72e31acaadc83f4c094b99fa938377e5f023
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500010"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Underhåll för virtuella datorer i Azure

Azure uppdaterar regelbundet sin plattform för att förbättra tillförlitligheten, prestanda och säkerheten i värd infrastrukturen för virtuella datorer. Syftet med dessa uppdateringar är att uppdatera programvarukomponenter i värdmiljön för att uppgradera nätverkskomponenter eller inaktivera maskinvara. 

Uppdateringar påverkar sällan de värdar som är värdar för virtuella datorer. När uppdateringar har en effekt väljer Azure den minsta påverkan metoden för uppdateringar:

- Om uppdateringen inte kräver en omstart pausas den virtuella datorn medan värden uppdateras, eller så är den virtuella datorn Live-migrerad till en redan uppdaterad värd. 
- Om underhåll kräver en omstart får du ett meddelande om planerat underhåll. Azure tillhandahåller också ett tids fönster där du kan starta underhållet själv, vid en tidpunkt som passar dig. Fönstret för själv underhåll är vanligt vis 35 dagar om inte underhållet är brådskande. Azure investerar i teknikerna för att minska antalet fall där det planerade plattforms underhållet kräver att de virtuella datorerna startas om. Anvisningar om hur du hanterar planerat underhåll finns i hantera planerade underhålls aviseringar med hjälp av Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) eller [Portal](maintenance-notifications-portal.md).

Den här sidan beskriver hur Azure utför båda typerna av underhåll. Mer information om oplanerade händelser (avbrott) finns i [Hantera tillgängligheten för virtuella datorer för Windows](./availability.md) eller motsvarande artikel för [Linux](./availability.md).

I en virtuell dator kan du få aviseringar om kommande underhåll genom [att använda schemalagda händelser för Windows](./windows/scheduled-events.md) eller [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Underhåll som inte kräver en omstart

De flesta plattforms uppdateringar påverkar inte kundens virtuella datorer. När en uppdatering utan effekt inte är möjlig väljer Azure den uppdaterings metod som minst påverkar kundens virtuella datorer. 

De flesta underhålls effekter som inte är noll pausar den virtuella datorn i mindre än 10 sekunder. I vissa fall använder Azure minnes konserverings metoder för underhåll. Dessa mekanismer pausar den virtuella datorn i upp till 30 sekunder och bevarar minnet i RAM-minnet. Den virtuella datorn återupptas sedan och klockan synkroniseras automatiskt. 

Underhåll av minnes underhåll fungerar i mer än 90 procent av virtuella Azure-datorer. Den fungerar inte för G-, M-, N-och H-serien. Azure använder i allt större utsträckning teknik för Live-migrering och förbättrar underhålls metoder för att minska paus tiderna.  

De underhålls åtgärder som inte kräver en omstart tillämpas en fel domän i taget. De stoppas om de får varnings hälso signaler från plattforms övervaknings verktyg. 

De här typerna av uppdateringar kan påverka vissa program. När den virtuella datorn är Live-migrerad till en annan värd kan vissa känsliga arbets belastningar Visa en försämrad prestanda försämring under några minuter, vilket leder till att den virtuella datorn pausas. Om du vill förbereda för underhåll av virtuella datorer och minska påverkan under Azure-underhållet kan du försöka [använda schemalagda händelser för Windows](./windows/scheduled-events.md) eller [Linux](./linux/scheduled-events.md) för sådana program. 

För bättre kontroll av alla underhålls aktiviteter, inklusive noll effekter och omstarter av uppdateringar, kan du använda funktionen underhålls kontroll. Du måste använda antingen [Azure-dedikerade värdar](./dedicated-hosts.md) eller en [isolerad virtuell dator](../security/fundamentals/isolation-choices.md). Med underhålls kontrollen får du möjlighet att hoppa över alla plattforms uppdateringar och tillämpa uppdateringarna vid ditt val av tid inom en rullande 35-dagars period. Mer information finns i [styra uppdateringar med underhålls kontroll och Azure CLI](maintenance-control.md).


### <a name="live-migration"></a>Direktmigrering

Direktmigrering är en åtgärd som inte kräver en omstart och som bevarar minnet för den virtuella datorn. Det orsakar en paus eller frysning, vanligt vis inte mer än 5 sekunder. Förutom G-, M-, N-och H-serien är alla virtuella IaaS-datorer (Infrastructure as a Service) tillgängliga för direktmigrering. Kvalificerade virtuella datorer representerar över 90 procent av de virtuella IaaS-datorer som distribueras till Azure-flottan. 

Azure-plattformen startar Direktmigrering i följande scenarier:
- Planerat underhåll
- Maskin varu problem
- Fördelnings optimeringar

Vissa planerade underhålls scenarier använder direktmigrering och du kan använda Schemalagda händelser för att känna i förväg när direktmigrering ska starta.

Direktmigrering kan också användas för att flytta virtuella datorer när Azure Machine Learning algoritmer förutsäger ett förestående maskin varu haveri eller när du vill optimera VM-allokeringar. Mer information om förutsägelse modellering som identifierar instanser av försämrad maskin vara finns i [förbättra Azure VM-återhämtning med förutsägande Machine Learning och Direktmigrering](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Meddelanden om Live-migrering visas i Azure Portal i övervakaren och Service Health loggar samt i Schemalagda händelser om du använder dessa tjänster.

## <a name="maintenance-that-requires-a-reboot"></a>Underhåll som kräver en omstart

I sällsynta fall där virtuella datorer måste startas om för planerat underhåll får du ett meddelande i förväg. Planerat underhåll har två faser: självbetjänings fasen och en schemalagd underhålls fas.

Under *självbetjänings fasen*, som vanligt vis varar fyra veckor, startar du underhållet på dina virtuella datorer. Som en del av självbetjäningen kan du fråga varje virtuell dator för att se dess status och resultatet av din senaste underhålls förfrågan.

När du startar självbetjänings underhållet distribueras den virtuella datorn om till en redan uppdaterad nod. Eftersom den virtuella datorn startas om går den temporära disken förlorad och dynamiska IP-adresser som är kopplade till det virtuella nätverks gränssnittet uppdateras.

Om ett fel uppstår under självbetjänings underhållet stoppas åtgärden, den virtuella datorn uppdateras inte och du får möjlighet att försöka utföra självbetjänings underhållet igen. 

När självbetjänings fasen avslutas börjar fasen för det *schemalagda underhållet* . Under den här fasen kan du fortfarande fråga efter underhålls fasen, men du kan inte starta underhållet själv.

Mer information om hur du hanterar underhåll som kräver en omstart finns i **Hantera planerade underhålls aviseringar** med hjälp av Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) eller [Portal](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Tillgänglighets överväganden under schemalagt underhåll 

Om du väljer att vänta tills den schemalagda underhålls fasen finns det några saker som du bör tänka på när du behåller den högsta tillgängligheten för dina virtuella datorer. 

#### <a name="paired-regions"></a>Länkade regioner

Varje Azure-region är kopplad till en annan region inom samma geografiska närhet. Tillsammans skapar de ett region par. Under den schemalagda underhålls fasen uppdaterar Azure bara de virtuella datorerna i en region i ett region par. Till exempel, när du uppdaterar den virtuella datorn i norra centrala USA, uppdaterar Azure inte någon virtuell dator i södra centrala USA på samma tid. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra. Att förstå hur regions par fungerar kan hjälpa dig att distribuera dina virtuella datorer i flera regioner bättre. Mer information finns i [regions par för Azure](../best-practices-availability-paired-regions.md).

#### <a name="availability-sets-and-scale-sets"></a>Tillgänglighets uppsättningar och skalnings uppsättningar

När du distribuerar en arbets belastning på virtuella Azure-datorer kan du skapa de virtuella datorerna i en *tillgänglighets uppsättning* för att ge hög tillgänglighet till ditt program. Med hjälp av tillgänglighets uppsättningar kan du se till att minst en virtuell dator är tillgänglig under ett avbrott eller underhålls händelser som kräver en omstart.

I en tillgänglighets uppsättning sprids enskilda virtuella datorer över upp till 20 uppdaterings domäner. Under det schemalagda underhållet uppdateras bara en uppdaterings domän vid en specifik tidpunkt. Uppdaterings domäner uppdateras inte nödvändigt vis i turordning. 

*Skalnings uppsättningar* för virtuella datorer är en Azure Compute-resurs som du kan använda för att distribuera och hantera en uppsättning identiska virtuella datorer som en enda resurs. Skalnings uppsättningen distribueras automatiskt över UDs, t. ex. virtuella datorer i en tillgänglighets uppsättning. Precis som med tillgänglighets uppsättningar, när du använder skalnings uppsättningar, uppdateras bara en UD vid en bestämd tidpunkt under det schemalagda underhållet.

Mer information om hur du konfigurerar virtuella datorer för hög tillgänglighet finns i [Hantera tillgängligheten för dina virtuella datorer för Windows](./availability.md) eller motsvarande artikel för [Linux](./availability.md).

#### <a name="availability-zones"></a>Tillgänglighetszoner

Tillgänglighets zoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. 

En tillgänglighets zon är en kombination av en fel domän och en uppdaterings domän. Om du skapar tre eller fler virtuella datorer över tre zoner i en Azure-region, distribueras de virtuella datorerna på ett effektivt sätt mellan tre fel domäner och tre uppdaterings domäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

Varje infrastruktur uppdatering slår ut zon efter zon, inom en enda region. Men du kan distribuera distribution i Zon 1, och en annan distribution som går i Zon 2, på samma gång. Distributioner är inte alla serialiserade. Men en enda distribution slår bara ut en zon i taget för att minska risken.

## <a name="next-steps"></a>Nästa steg 

Du kan använda [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md) för att hantera planerat underhåll.