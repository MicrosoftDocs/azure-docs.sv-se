---
title: Förstå en omstart av systemet för en virtuell Azure-dator | Microsoft Docs
description: Visar en lista över händelser som kan göra att en virtuell dator startas om
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c3dcb4a7b92f5aab1336e49a7d299614855b48fd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591162"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Förstå en omstart av systemet för virtuell Azure-dator

Virtuella Azure-datorer (VM) kan ibland starta om utan någon uppenbar anledning, utan att du behöver starta om åtgärden. Den här artikeln innehåller en lista över de åtgärder och händelser som kan göra det möjligt för virtuella datorer att starta om och ger insyn i hur du undviker oväntade omstarter eller minskar konsekvenserna av sådana problem.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurera virtuella datorer för hög tillgänglighet

Det bästa sättet att skydda ett program som körs på Azure mot VM-omstarter och nedtid är att konfigurera de virtuella datorerna för hög tillgänglighet.

För att tillhandahålla den här nivån av redundans för ditt program, rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighets uppsättning. Den här konfigurationen garanterar att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse och uppfyller 99,95% [SLA för Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Mer information om tillgänglighets uppsättningar finns i [Hantera tillgängligheten för virtuella datorer](../manage-availability.md)

## <a name="resource-health-information"></a>Resource Health information

Azure Resource Health är en tjänst som exponerar hälsan för enskilda Azure-resurser och tillhandahåller åtgärds bara vägledning för fel sökning av problem. I en moln miljö där det inte är möjligt att direkt komma åt servrar eller infrastruktur element, är målet för Resource Health att minska den tid som du lägger på fel sökning. Syftet är särskilt att minska den tid som du ägnat åt att fastställa om roten till problemet finns i programmet eller i en händelse i Azure-plattformen. Mer information finns i [förstå och använda Resource Health](../../service-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Åtgärder och händelser som kan göra att den virtuella datorn startas om

### <a name="planned-maintenance"></a>Planerat underhåll

Microsoft Azure regelbundet utför uppdateringar över hela världen för att förbättra tillförlitligheten, prestandan och säkerheten i värd infrastrukturen som är beroende av virtuella datorer. Många av dessa uppdateringar, inklusive minnes bevarande uppdateringar, utförs utan påverkan på dina virtuella datorer eller moln tjänster.

Vissa uppdateringar kräver dock en omstart. I sådana fall stängs de virtuella datorerna ned medan vi uppdaterar infrastrukturen och sedan startas de virtuella datorerna om.

Information om vad Azure-planerat underhåll är och hur det kan påverka tillgängligheten för dina virtuella Linux-datorer finns i artiklarna som visas här. Artiklarna innehåller information om processen för planerat underhåll av Azure och hur du schemalägger planerat underhåll för att minska påverkan ytterligare.

- [Planerat underhåll av virtuella datorer i Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)
- [Så här schemalägger du planerat underhåll av virtuella Azure-datorer](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)

### <a name="memory-preserving-updates"></a>Minnesbevarande uppdateringar

För den här klassen av uppdateringar i Microsoft Azure påverkar användarna inte de virtuella datorerna som körs. Många av de här uppdateringarna är till komponenter eller tjänster som kan uppdateras utan att störa den instans som körs. Vissa är plattforms infrastruktur uppdateringar på värd operativ systemet som kan tillämpas utan omstart av de virtuella datorerna.

De här minnesbevarande uppdateringarna möjliggörs med teknik som aktiverar livemigrering på plats. När den uppdateras placeras den virtuella datorn i *paus* läge. Det här tillståndet bevarar minnet i RAM medan det underliggande värdoperativsystemet får nödvändiga uppdateringar och korrigeringar. Den virtuella datorn återupptas inom 30 sekunder efter att ha pausats. När det virtuella datorn har återupptagits synkroniseras klockan automatiskt.

På grund av den korta paus perioden minskar distributionen av uppdateringar via den här mekanismen avsevärt påverkan på de virtuella datorerna. Men alla uppdateringar kan inte distribueras på det här sättet. 

Uppdateringar med flera instanser (för virtuella datorer i en tillgänglighetsuppsättning) tillämpas med en uppdateringsdomän i taget.

> [!NOTE]
> Linux-datorer som har gamla kernel-versioner påverkas av en kernel-panik under den här uppdaterings metoden. Undvik det här problemet genom att uppdatera till kernel-version 3.10.0-327.10.1 eller senare. Mer information finns i [en virtuell Azure Linux-dator på en 3,10-baserad kernel-panic efter en uppgradering av noden värd](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Användarinitierade åtgärder för omstart eller avstängning

Om du utför en omstart från Azure Portal, Azure PowerShell, kommando rads gränssnitt eller REST API kan du hitta händelsen i [Azure aktivitets loggen](../../azure-monitor/essentials/platform-logs-overview.md).

Om du utför åtgärden från den virtuella datorns operativ system kan du hitta händelsen i system loggarna.

Andra scenarier som vanligt vis leder till att den virtuella datorn startas om är flera konfigurations ändrings åtgärder. Du ser vanligt vis ett varnings meddelande om att körningen av en viss åtgärd leder till en omstart av den virtuella datorn. Exempel på åtgärder för att ändra storlek på virtuella datorer, ändra lösen ordet för det administrativa kontot och ange en statisk IP-adress.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center och Windows Update

Azure Security Center övervakar dagliga virtuella Windows-och Linux-datorer för saknade uppdateringar av operativ systemet. Security Center hämtar en lista över tillgängliga säkerhets uppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services (WSUS), beroende på vilken tjänst som har kon figurer ATS på en virtuell Windows-dator. Security Center också att söka efter de senaste uppdateringarna för Linux-system. Om en system uppdatering saknas i den virtuella datorn rekommenderar Security Center att du installerar System uppdateringar. Programmet för dessa system uppdateringar styrs via Security Center i Azure Portal. När du har installerat vissa uppdateringar kan det krävas omstarter av virtuella datorer. Mer information finns i [tillämpa system uppdateringar i Azure Security Center](../../security-center/asset-inventory.md).

Precis som lokala servrar skickar Azure inga uppdateringar från Windows Update till virtuella Windows-datorer, eftersom dessa datorer är avsedda att hanteras av deras användare. Du uppmanas dock att lämna inställningen för automatisk Windows Update aktive rad. Automatisk installation av uppdateringar från Windows Update kan också medföra att omstarter sker efter att uppdateringarna har tillämpats. Mer information finns i [vanliga frågor och svar om Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Andra situationer som påverkar tillgängligheten för din virtuella dator

Det finns andra fall då Azure kan pausa användningen av en virtuell dator aktivt. Du får e-postaviseringar innan den här åtgärden utförs, så du får chansen att lösa de underliggande problemen. Exempel på problem som påverkar VM-tillgänglighet inkluderar säkerhets överträdelser och förfallo datum för betalnings metoder.

### <a name="host-server-faults"></a>Värdserverfel

Den virtuella datorn finns på en fysisk server som körs i ett Azure-datacenter. Den fysiska servern kör en agent som kallas värd agenten förutom några andra Azure-komponenter. När dessa Azure-programkomponenter på den fysiska servern slutar svara, utlöser övervaknings systemet en omstart av värd servern för att försöka återställa. Den virtuella datorn är vanligt vis tillgänglig inom fem minuter och fortsätter att leva på samma värd som tidigare.

Server fel orsakas vanligt vis av maskin varu fel, t. ex. fel på en hård disk eller en solid state-enhet. Azure övervakar kontinuerligt dessa förekomster, identifierar underliggande buggar och slår ut uppdateringar efter att lösningen har implementerats och testats.

Eftersom vissa värd Server fel kan vara specifika för den servern kan en upprepad omstart av virtuella datorer förbättras genom manuell omdistribution av den virtuella datorn till en annan värd Server. Den här åtgärden kan utlösas med hjälp av alternativet **distribuera** på sidan information på den virtuella datorn, eller genom att stoppa och starta om den virtuella datorn i Azure Portal.

### <a name="auto-recovery"></a>Automatisk återställning

Om värd servern inte kan starta om av någon anledning initierar Azure-plattformen en åtgärd för automatisk återställning för att ta den felande värd servern från rotationen för ytterligare undersökning. 

Alla virtuella datorer på värden flyttas automatiskt till en annan, felfri värd Server. Den här processen slutförs vanligt vis inom 15 minuter. Mer information om den automatiska återställnings processen finns i [Automatisk återställning av virtuella datorer](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Oplanerat underhåll

I sällsynta fall kan Azure Operations-teamet behöva utföra underhålls aktiviteter för att säkerställa den övergripande hälsan för Azure-plattformen. Det här beteendet kan påverka VM-tillgänglighet och ger vanligt vis samma automatiska återställnings åtgärd enligt beskrivningen ovan.  

Oplanerat underhåll inkluderar följande:

- Brådskande nod-defragmentering
- Brådskande nätverks växel uppdateringar

### <a name="vm-crashes"></a>VM-krascher

Virtuella datorer kan starta om på grund av problem i själva den virtuella datorn. Arbets belastningen eller rollen som körs på den virtuella datorn kan utlösa en fel kontroll i gäst operativ systemet. Om du behöver hjälp med att fastställa orsaken till kraschen kan du Visa system-och program loggarna för virtuella Windows-datorer och serie loggarna för virtuella Linux-datorer.

### <a name="storage-related-forced-shutdowns"></a>Storage-relaterade tvingade avstängningar

Virtuella datorer i Azure använder virtuella diskar för operativ system och data lagring som finns på Azure Storage-infrastrukturen. När tillgängligheten eller anslutningen mellan den virtuella datorn och de tillhör ande virtuella diskarna påverkas i mer än 120 sekunder, utför Azure-plattformen en Tvingad avstängning av de virtuella datorerna för att undvika skadade data. De virtuella datorerna stängs av automatiskt när lagrings anslutningen har återställts. 

Varaktigheten för avstängningen kan vara så kort som fem minuter, men det kan vara betydligt längre. Följande är ett av de speciella fall som är kopplade till lagringsrelaterade tvingade avstängning: 

**Överstiger IO-gränser**

De virtuella datorerna kan tillfälligt stängas av när I/O-begäranden är konsekvent begränsade eftersom volymerna i/O-åtgärder per sekund (IOPS) överskrider diskens I/O-gränser. (Standard disk lagring är begränsad till 500 IOPS.) Du kan åtgärda det här problemet genom att använda disk ränder eller konfigurera lagrings utrymmet i den virtuella gäst datorn, beroende på arbets belastningen. 

### <a name="other-incidents"></a>Andra incidenter

I sällsynta fall kan ett omfattande problem påverka flera servrar i ett Azure-datacenter. Om det här problemet uppstår skickar Azure-teamet e-postaviseringar till de påverkade prenumerationerna. Du kan kontrol lera [Azure Service Health instrument panelen](https://azure.microsoft.com/status/) och Azure Portal för status för pågående avbrott och tidigare incidenter.