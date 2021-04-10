---
title: Resource Health för Cloud Services (klassisk)
description: Den här artikeln pratar om stöd för Resource Health Check (RHC) för Microsoft Azure Cloud Services (klassisk)
ms.topic: article
ms.service: cloud-services
ms.subservice: resource-health
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 46a27e42d6b3440914ca0d32e1ae2138b128021e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935166"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Stöd för Resource Health Check (RHC) för Azure Cloud Services (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln pratar om stöd för Resource Health Check (RHC) för [Microsoft Azure Cloud Services (klassisk)](https://azure.microsoft.com/services/cloud-services)

[Azure Resource Health](../service-health/resource-health-overview.md) för moln tjänster hjälper dig att diagnostisera och få support för tjänst problem som påverkar moln tjänst distributionen, roller & roll instanser. Den rapporterar om aktuella och tidigare hälso tillstånd för moln tjänster vid distribution, roll & roll instans nivå.

Azures status rapporter om problem som påverkar en bred uppsättning Azure-kunder. Resource Health ger dig en anpassad instrument panel med hälso tillståndet för dina resurser. Resource Health visar alla tider som resurserna har varit otillgängliga på grund av problem med Azure-tjänsten. Dessa data gör det enkelt för dig att se om ett SLA bröts.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Bild visar bladet för resurs hälso kontroll i Azure Portal.":::

## <a name="how-health-is-checked-and-reported"></a>Hur kontrol leras och rapporteras hälso tillståndet?
Resurs hälsan rapporteras på en distributions-eller roll nivå. Hälso kontrollen sker på roll instans nivå, vi sammanställer status och rapporterar den på roll nivå. T.ex. Om alla roll instanser är tillgängliga är roll statusen tillgänglig. På samma sätt sammanställer vi hälso status för alla roller och rapporterar den på distributions nivå. T.ex. Om alla roller är tillgängliga blir distributions statusen tillgänglig. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Varför kan jag inte se hälso status för min mellanlagrings plats distribution?
Resurs hälso kontroller fungerar bara för distribution av produktions platser. Distribution av mellanlagrings platser stöds inte ännu. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Kontrollerar Resource Health även hälso tillståndet för programmet?
Nej, hälso kontroll sker bara för roll instanser och övervakar inte program hälsan. T.ex. Även om 1 av 3 roll instanserna inte är felfria kan programmet fortfarande vara tillgängligt. RHC använder inte [avsökningar av belastnings utjämning](../load-balancer/load-balancer-custom-probe-overview.md) eller en gästa Gent avsökning. Därför bör kunderna fortsätta att använda belastnings Utjämnings avsökningar för att övervaka hälso tillståndet för programmet. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Vilka är anteckningarna för Cloud Services?
Anteckningar är distributionens eller rollernas hälso status. Det finns olika anteckningar baserade på hälso status, orsak till status ändring osv. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Vad betyder det att roll instansen är "ej tillgänglig"?
Det innebär att roll instansen inte avger en felfri signal till plattformen. Kontrol lera roll instans statusen om du vill ha en detaljerad förklaring av varför den felfria signalen inte genereras.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Vad betyder det att distributionen är "okänd"?
Okänd innebär att det inte går att fastställa det sammanlagda hälso tillståndet för moln tjänst distributionen. Det innebär vanligt vis att ingen produktions distribution har skapats för moln tjänsten, distributionen har nyligen skapats (och att Azure börjar samla in hälso tillstånds händelser) eller plattform har problem med att samla in hälso händelser för den här distributionen.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Varför nämner roll instans anteckningarna virtuella datorer i stället för roll instanser?
Eftersom roll instanserna huvudsakligen är virtuella datorer och hälso kontrollen för virtuella datorer återanvänds för roll instanser, används den virtuella dator termen för att representera roll instanser. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Cloud Services (distributions nivå) anteckningar & deras betydelser
| Anteckning | Beskrivning | 
| --- | --- | 
| Tillgänglig| Inga kända Azure-plattforms problem påverkar den här moln tjänst distributionen |
| Okänt | Vi kan för närvarande inte fastställa hälso tillståndet för den här moln tjänst distributionen | 
| Konfigurera Resource Health | Konfigurerar resurs hälsa för den här resursen. Resource Health bevakar dina Azure-resurser för att ge information om pågående och tidigare händelser som har påverkat dem|
| Degraderad | Molntjänstdistributionen är degraderad. Vi arbetar med att återställa molntjänstdistributionen automatiskt och att fastställa orsaken till problemet. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Ohälsosamt | Distributionen av moln tjänsten är inte felfri eftersom det inte {0} {1} finns några tillgängliga roll instanser |
| Degraderad | Distributionen av moln tjänsten är försämrad eftersom det inte {0} {1} finns några tillgängliga roll instanser | 
| Tillgängligt och kanske påverkas | Moln tjänst distributionen körs, men ett pågående Azure Service-avbrott kan förhindra att du ansluter till den. Anslutningen kommer att återställas när avbrott har åtgärd ATS |
| Otillgänglig och kan påverkas | Hälso tillståndet för moln tjänst distributionen kan påverkas av ett avbrott i Azure-tjänsten. Distributionen av moln tjänsten kommer automatiskt att återställas när avbrottet har lösts |
| Okänt och kanske påverkas | Vi kan för närvarande inte fastställa hälso tillståndet för den här moln tjänst distributionen. Detta kan bero på ett pågående avbrott i Azure-tjänsten som kan påverka den här virtuella datorn, vilket automatiskt kommer att återställas när avbrottet har lösts |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Cloud Services (roll instans nivå) anteckningar & deras betydelser
| Anteckning | Beskrivning | 
| --- | --- | 
| Tillgänglig | Inga kända Azure-plattforms problem påverkar den här virtuella datorn | 
| Okänt | Vi kan för närvarande inte fastställa hälso tillståndet för den här virtuella datorn |
| Stoppa och frigör | Den här virtuella datorn stoppas och frigörs på begäran av en behörig användare eller process |
| Konfigurera Resource Health | Konfigurerar resurs hälsa för den här resursen. Resource Health bevakar dina Azure-resurser för att ge information om pågående och tidigare händelser som har påverkat dem |
| Inte tillgänglig | Din virtuella dator är inte tillgänglig. Vi arbetar med att återställa din virtuella dator automatiskt och att fastställa orsaken till problemet. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Degraderad | Din virtuella dator har nedgraderats. Vi arbetar med att återställa din virtuella dator automatiskt och att fastställa orsaken till problemet. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Maskin varu problem på värd servern | Den här virtuella datorn påverkas av ett allvarligt {HardwareCategory}-fel på värd servern. Azure distribuerar om den virtuella datorn till en felfria värd Server |
| Migreringen har schemalagts på grund av försämrad maskin vara | Azure har identifierat att värdservern har en nedgraderad {0} som förväntas sluta fungera snart. Vi kommer så snart som möjligt att direktmigrera den virtuella datorn (om det är möjligt), eller på annat sätt distribuera om den efter {1} (UTC-tid). För att minimera risken för din tjänst, och om maskin varan Miss lyckas innan den systeminitierade migreringen sker, rekommenderar vi att du distribuerar den virtuella datorn på egen plats så snart som möjligt |
| Tillgängligt och kanske påverkas | Den virtuella datorn körs, men ett pågående avbrott i Azure-tjänsten kan förhindra att du ansluter till den. Anslutningen kommer att återställas när avbrott har åtgärd ATS |
| Otillgänglig och kan påverkas | Hälso tillståndet för den här virtuella datorn kan påverkas av ett avbrott i Azure-tjänsten. Den virtuella datorn kommer att återställas automatiskt när avbrottet har lösts |
| Okänt och kanske påverkas | Vi kan för närvarande inte fastställa hälso tillståndet för den här virtuella datorn. Detta kan bero på ett pågående avbrott i Azure-tjänsten som kan påverka den här virtuella datorn, vilket automatiskt kommer att återställas när avbrottet har lösts |
| Allokerade maskin varu resurser | Maskinvaruresurser har tilldelats till den virtuella datorn och kommer snart att vara online |
| Stoppa och frigör | Den här virtuella datorn stoppas och frigörs på begäran av en behörig användare eller process |
| Konfigurationen uppdateras | Den virtuella datorns konfiguration ändras på begäran av en behörig användare eller process |
| Startats om av användaren | Den här virtuella datorn startas om på begäran av en behörig användare eller process. Den kommer att vara online igen när omstarten är klar |
| Distribuerar om till en annan värd | Den virtuella datorn distribueras om till en annan värd på begäran av en behörig användare eller process. Den kommer att vara online igen när omdistributionen har slutförts |
| Stoppades av användaren | Den här virtuella datorn stoppas på begäran av en behörig användare eller process |
| Stoppades av användaren eller processen | Den här virtuella datorn stoppas på begäran av en behörig användare eller en process som körs i den virtuella datorn |
| Startad av användare | Den här virtuella datorn startas på begäran av en behörig användare eller process. Den kommer att vara online snart |
| Underhåll omdistribueras till en annan värd | Den här virtuella datorn distribueras om till en annan värdserver som en del av en planerad underhållsaktivitet. Den kommer att vara online igen när omdistributionen har slutförts |
| Starta om startas inifrån datorn | En omstart utlöstes från den virtuella datorn. Detta kan inträffa på grund av ett operativsystemfel på den virtuella datorn, eller på begäran av en behörig användare eller process. Den virtuella datorn kommer att vara online igen när omstarten har slutförts |
| Ändrades av användare | Den virtuella datorns storlek ändras på begäran av en behörig användare eller process. Den kommer att vara online igen när storleks ändringen har slutförts |
| Datorn har kraschat | En omstart utlöstes från den virtuella datorn. Detta kan inträffa på grund av ett operativsystemfel på den virtuella datorn, eller på begäran av en behörig användare eller process. Den virtuella datorn kommer att vara online igen när omstarten har slutförts |
| Underhålls omstart för värd uppdatering | Underhållsuppdateringar utförs på värdservern som kör den virtuella datorn. Ingen ytterligare åtgärd krävs av dig just nu. Det kommer att vara online igen när underhållet har slutförts |
| Underhåll omdistribution till ny maskin vara | Den här virtuella datorn är inte tillgänglig eftersom den omdistribueras till nyare maskinvara som en del av en planerad underhållshändelse. Ingen ytterligare åtgärd krävs av dig just nu. Den kommer att vara online igen när det planerade underhållet har slutförts |
| Datorn med låg prioritet blockerades | Den här virtuella datorn har blockerats. Den här virtuella datorn med låg prioritet stoppas och frigörs |
| Omstart av värd Server | Den virtuella datorn är inte tillgänglig på grund av en oväntad omstart av värdservern. Värdservern håller på att startas om. Den virtuella datorn kommer att vara online igen när omstarten har slutförts. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Omdistribueras på grund av ett värd haveri | Den virtuella datorn är inte tillgänglig och den omdistribueras på grund av ett oväntat fel på värdservern. Azure har påbörjat den automatiska återställningsprocessen och håller på att starta den virtuella datorn på en annan värd. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Oväntat värd fel | Den virtuella datorn är inte tillgänglig på grund av ett oväntat fel på värdservern. Azure har påbörjat den automatiska återställningsprocessen och håller på att starta om värdservern. Ingen ytterligare åtgärd krävs av dig just nu. Den virtuella datorn kommer att vara online igen när omstarten har slutförts |
| Omdistribueras på grund av oplanerat underhåll av värd | Den virtuella datorn är inte tillgänglig och den omdistribueras på grund av ett oväntat fel på värdservern. Azure har påbörjat den automatiska återställningsprocessen och håller på att starta den virtuella datorn på en annan värdserver. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Etablerings problem | Den virtuella datorn är tyvärr inte tillgänglig på grund av oväntade etableringsproblem. Det gick inte att utföra etableringen av den virtuella datorn på grund av ett oväntat fel |
| Direktmigrering | Den virtuella datorn har pausats på grund av en minnesbevarande åtgärd under direktmigreringen. Den virtuella datorn startar om inom 10 sekunder. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Direktmigrering | Den virtuella datorn har pausats på grund av en minnesbevarande åtgärd under direktmigreringen. Den virtuella datorn startar om inom 10 sekunder. Ingen ytterligare åtgärd krävs från dig för tillfället | 
| Fjärrdisk frånkopplad | Din virtuella dator är tyvärr inte tillgänglig eftersom anslutningen till fjärrdisken har brutits. Vi arbetar med att återetablera diskanslutningen. Ingen ytterligare åtgärd krävs från dig för tillfället |
| Problem med Azure-tjänsten | Den virtuella datorn påverkas av problem med Azure-tjänsten |
| Nätverks problem | Den här virtuella datorn påverkas av en top-of-rack-nätverks enhet |
| Inte tillgänglig | Din virtuella dator är inte tillgänglig. Vi kan för närvarande inte fastställa orsaken till den här stillestånds tiden |
| Omstart av värd Server | Den virtuella datorn är inte tillgänglig på grund av en oväntad omstart av värdservern. Ett oväntat problem med värd servern hindrar oss från att automatiskt återskapa den virtuella datorn |
| Omdistribueras på grund av ett värd haveri | Den virtuella datorn är inte tillgänglig på grund av ett oväntat fel på värdservern. Ett oväntat problem med värden hindrar oss från att automatiskt återskapa den virtuella datorn |
| Oväntat värd fel | Den virtuella datorn är inte tillgänglig på grund av ett oväntat fel på värdservern. Ett oväntat problem med värden hindrar oss från att automatiskt återskapa den virtuella datorn |
| Omdistribueras på grund av oplanerat underhåll av värd | Den virtuella datorn är inte tillgänglig på grund av ett oväntat fel på värdservern. Ett oväntat problem med värden hindrar oss från att automatiskt återskapa den virtuella datorn |
| Etablerings problem | Den virtuella datorn är tyvärr inte tillgänglig på grund av oväntade etableringsproblem. Det gick inte att utföra etableringen av den virtuella datorn på grund av ett oväntat fel |
| Fjärrdisk frånkopplad | Din virtuella dator är tyvärr inte tillgänglig eftersom anslutningen till fjärrdisken har brutits. Det gick inte att återskapa den virtuella datorn automatiskt på grund av ett oväntat problem |
| Omstart på grund av gäst operativ system uppdatering | En omstart initierades av Azure-plattformen för att tillämpa en ny gäst operativ system uppdatering. Den virtuella datorn kommer att vara online igen när omstarten har slutförts |