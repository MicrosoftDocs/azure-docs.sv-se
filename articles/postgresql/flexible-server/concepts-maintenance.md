---
title: Schemalagt underhåll – Azure Database for PostgreSQL – flexibel server
description: I den här artikeln beskrivs funktionen för schemalagt underhåll Azure Database for PostgreSQL – flexibel server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 50ef040f1cb7d8c533ec5ee31e9bffa2e6dca2f5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478020"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Schemalagt underhåll i Azure Database for PostgreSQL – flexibel server
 
Azure Database for PostgreSQL – Flexibel server utför regelbundet underhåll för att hålla din hanterade databas säker, stabil och uppdaterad. Under underhållet får servern nya funktioner, uppdateringar och korrigeringar.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexibel server är i förhandsversion
 
## <a name="selecting-a-maintenance-window"></a>Välja en underhållsfönstret
 
Du kan schemalägga underhåll under en viss dag i veckan och en tidsperiod inom den dagen. Eller så kan du låta systemet välja en dag och en tidsfönster för dig automatiskt. Oavsett vilket varnar systemet dig fem dagar innan underhållet körs. Systemet kommer också att meddela dig när underhållet har startats och när det har slutförts.
 
Meddelanden om kommande schemalagt underhåll kan vara:
 
* E-postad till en specifik adress
* E-postas till en Azure Resource Manager roll
* Skickas i ett SMS till mobila enheter
* Som en push-avisering till en Azure-app
* Som ett röstmeddelande
 
När du anger inställningar för underhållsschemat kan du välja en veckodag och en tidsperiod. Om du inte anger detta väljer systemet tider mellan 23:00 och 07:00 (i serverns tidszon). Du kan definiera olika scheman för varje flexibel server i din Azure-prenumeration. 
 
> [!IMPORTANT]
> Vanligtvis är tiden mellan schemalagda underhållshändelser för en server minst 30 dagar.
>
> Men om det finns en kritisk uppdatering, till exempel en allvarligt säkerhetsrisk kan aviseringsperioden vara kortare än fem dagar. Den kritiska uppdateringen kan tillämpas på servern även om ett lyckat schemalagt underhåll har utförts under de senaste 30 dagarna.

Du kan uppdatera schemaläggningsinställningarna när som helst. Om underhåll har schemalagts för din flexibla server och du uppdaterar schemaläggningsinställningarna fortsätter den aktuella versionen som schemalagd och ändringen av schemaläggningsinställningarna börjar gälla när den har slutförts för nästa schemalagda underhåll.

Du kan definiera system-hanterat schema eller anpassat schema för varje flexibel server i din Azure-prenumeration.  
* Med anpassat schema kan du ange underhållsfönstret för servern genom att välja veckodag och en timma.  
* Med system-hanterat schema väljer systemet ett entimmefönster mellan kl. 11:00 och 07:00 i serverns regiontid.  

Som en del av att distribuera ändringar tillämpar vi uppdateringarna på de servrar som konfigurerats med system-hanterat schema först följt av servrar med anpassat schema efter en minsta lucka på 7 dagar inom en viss region. Om du planerar att få tidiga uppdateringar på en vagnpark med servrar för utvecklings- och testmiljöer rekommenderar vi att du konfigurerar system hanterat schema för servrar som används i utvecklings- och testmiljön. På så sätt kan du få den senaste uppdateringen först i din Dev/Test-miljö för testning och utvärdering för verifiering. Om du stöter på något beteende eller icke-större ändringar har du tid att åtgärda dem innan samma uppdatering distribueras till produktionsservrar med anpassat hanterat schema. Uppdateringen börjar distribueras på flexibla servrar enligt anpassat schema efter 7 dagar och tillämpas på servern i den definierade underhållsfönstret. Just nu finns det inget alternativ för att skjuta upp uppdateringen när meddelandet har skickats. Anpassat schema rekommenderas endast för produktionsmiljöer. 

I sällsynta fall kan underhållshändelsen avbrytas av systemet eller misslyckas med att slutföras. Om uppdateringen misslyckas återställs uppdateringen och den tidigare versionen av binärfilerna återställs. I sådana scenarier med misslyckad uppdatering kan det fortfarande uppstå omstart av servern under underhållsfönstret. Om uppdateringen avbryts eller misslyckas skapar systemet ett meddelande om avbruten eller misslyckad underhållshändelse som meddelar dig. Nästa försök att utföra underhåll schemaläggs enligt dina aktuella schemaläggningsinställningar och du får ett meddelande om det fem dagar i förväg. 

 
## <a name="next-steps"></a>Nästa steg
 
* Lär dig hur [du ändrar underhållsschemat](how-to-maintenance-portal.md)
* Lär dig hur [du får meddelanden om kommande underhåll med](../../service-health/service-notifications.md) hjälp av Azure Service Health
* Lär dig hur [du ställer in aviseringar om kommande schemalagda underhållshändelser](../../service-health/resource-health-alert-monitor-guide.md)
