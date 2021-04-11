---
title: Teknisk information och krav för att migrera till Azure Cloud Services (utökad support)
description: Innehåller teknisk information och krav för migrering från Azure Cloud Services (klassisk) till Azure Cloud Services (utökad support)
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286996"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Teknisk information om migrering till Azure Cloud Services (utökad support)   

I den här artikeln beskrivs den tekniska informationen om Migreringsverktyg som gäller Cloud Services (klassisk). 

> [!IMPORTANT]
> Migrering från Cloud Services (klassisk) till Cloud Services (utökad support) med migreringsverktyget är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Information om funktioner/scenarier som stöds för migrering 

### <a name="extensions-and-plugin-migration"></a>Migrering av tillägg och plugin 
- Alla aktiverade och stödda tillägg kommer att migreras. 
- Inaktiverade tillägg kommer inte att migreras. 
- Plugin-program är ett bakåtkompatibelt koncept och bör tas bort innan migrering. De stöds för migrering och men efter migreringen måste plugin-programmet tas bort innan du installerar tillägget om tillägget måste vara aktiverat. Plugin-program för fjärr skrivbord och tillägg påverkas mest av detta.   
 
### <a name="certificate-migration"></a>Migrera certifikat
- I Cloud Services (utökad support) lagras certifikat i en Key Vault. Som en del av migreringen skapar vi en Key Vault för kunderna som har moln tjänst namnet och överför alla certifikat från Azure Service Manager till Key Vault. 
- Referensen till den här Key Vault anges i mallen eller skickas via PowerShell eller Azure CLI. 

### <a name="service-configuration-and-service-definition-files"></a>Tjänst konfiguration och tjänst definitions filer
- Filerna. cscfg och. csdef måste uppdateras för Cloud Services (utökad support) med mindre ändringar. 
- Namnen på resurser som virtuella nätverk och VM SKU är olika. Se [översättning av resurser och namngivnings konvention efter migrering](#translation-of-resources-and-naming-convention-post-migration)
- Kunderna kan hämta sina nya distributioner via [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) och [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/get). 

### <a name="cloud-service-and-deployments"></a>Moln tjänst och distributioner
- Varje Cloud Services (utökad support) distribution är en oberoende moln tjänst. Distributionen är inte längre grupperad i en moln tjänst med hjälp av platser.
- Om du har två platser i din moln tjänst (klassisk) måste du ta bort en plats (mellanlagring) och använda migrations verktyget för att flytta den andra (produktions-) platsen till Azure Resource Manager. 
- Den offentliga IP-adressen i moln tjänst distributionen förblir densamma efter migreringen till Azure Resource Manager och visas som en grundläggande resurs för SKU IP (dynamisk eller statisk). 
- DNS-namnet och domänen (cloudapp.azure.net) för den migrerade moln tjänsten är oförändrade. 

### <a name="virtual-network-migration"></a>Migrering av virtuellt nätverk
- Om en Cloud Services-distribution finns i ett virtuellt nätverk migreras alla Cloud Services och associerade virtuella nätverks resurser tillsammans. 
- Efter migreringen placeras det virtuella nätverket i en annan resurs grupp än moln tjänsten. 
- För virtuella nätverk med flera Cloud Services migreras varje moln tjänst en efter den andra. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migrering av distributioner som inte är i ett virtuellt nätverk
- I 2017 började Azure automatiskt skapa nya distributioner (utan kundens angivna virtuella nätverk) till en plattform som har skapat "standard" virtuellt nätverk. Dessa virtuella standard nätverk är dolda från kunder.   
- Som en del av migreringen kommer det här virtuella standard nätverket att exponeras för kunderna en gång i Azure Resource Manager. För att kunna hantera eller uppdatera distributionen i Azure Resource Manager måste kunderna lägga till den här virtuella nätverks informationen i avsnittet NetworkConfiguration i. cscfg-filen.    
- Det virtuella standard nätverket, vid migrering till Azure Resource Manager, placeras i samma resurs grupp som moln tjänsten.
- Cloud Services som skapats innan den här tiden kommer inte att finnas i något virtuellt nätverk och kan inte migreras med verktyget. Överväg att omdistribuera dessa Cloud Services direkt i Azure Resource Manager. 
- Om du vill kontrol lera om en distribution är kvalificerad att migrera kör du validate API i distributionen. Resultatet av verifierings-API: et kommer att innehålla ett fel meddelande som uttryckligen anges om den här distributionen är kvalificerad att migrera.     

### <a name="load-balancer"></a>Load Balancer   
- För en moln tjänst som använder en offentlig slut punkt exponeras en plattform som har skapat en belastningsutjämnare som är associerad med moln tjänsten i kundens prenumeration i Azure Resource Manager. Belastningsutjämnaren är en skrivskyddad resurs och uppdateringar begränsas bara via tjänst konfigurations-(. cscfg) och service definitions-filer (. csdef). 

### <a name="key-vault"></a>Key Vault
- Som en del av migreringen skapar Azure automatiskt en ny Key Vault och migrerar alla certifikat till den. Verktyget tillåter inte att du använder en befintlig Key Vault. 
- Cloud Services (utökad support) kräver ett Key Vault som finns i samma region och prenumeration. Den här Key Vault skapas automatiskt som en del av migreringen. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Översättning av resurser och namngivnings konvention efter migrering
Som en del av migreringen ändras resurs namnen och några Cloud Services funktioner visas som Azure Resource Manager resurser. Tabellen sammanfattar de ändringar som är speciella för migrering av Cloud Services.

| Cloud Services (klassisk) <br><br> Resursnamn | Cloud Services (klassisk) <br><br> Syntax| Cloud Services (utökad support) <br><br> Resursnamn| Cloud Services (utökad support) <br><br> Syntax | 
|---|---|---|---|
| Molntjänst | `cloudservicename` | Inte kopplad| Inte kopplad |
| Distribution (portalen har skapats) <br><br> Distribution (ej Portal skapad)  | `deploymentname` | Cloud Services (utökad support) | `deploymentname` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Inte kopplad |  Virtual Network (inte Portal skapad) <br><br> Virtual Network (portal skapad) <br><br> Virtuella nätverk (standard) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Inte kopplad | Inte kopplad | Key Vault | `cloudservicename` | 
| Inte kopplad | Inte kopplad | Resurs grupp för moln tjänst distributioner | `cloudservicename-migrated` | 
| Inte kopplad | Inte kopplad | Resurs grupp för Virtual Network | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Inte kopplad | Inte kopplad | Offentlig IP (dynamisk) | `cloudservicenameContractContract` | 
| Reserverad IP namn | `reservedipname` | Reserverad IP (inte Portal skapad) <br><br> Reserverad IP (portal skapad) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Inte kopplad| Inte kopplad | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Problem med migrering och hur du hanterar dem. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Migreringen har fastnat i en åtgärd under en längre tid. 
- Det kan ta lång tid att genomföra, förbereda och avbryta beroende på antalet distributioner. Tids gränsen för åtgärder upphör att vara efter 24 timmar.   
- Åtgärder för att genomföra, förbereda och avbryta är idempotenta. De flesta problem är fixable genom att försöka igen. Det kan finnas tillfälliga fel, vilket kan gå undan om några minuter, vi rekommenderar att du försöker igen efter ett mellanrum. Om du migrerar med hjälp av Azure Portal och åtgärden har fastnat i ett pågående tillstånd, använder du PowerShell för att försöka utföra åtgärden igen. 
- Kontakta supporten för att hjälpa att migrera eller återställa distributionen från Server delen. 

### <a name="migration-failed-in-an-operation"></a>Migreringen misslyckades i en åtgärd. 
- Om verifieringen misslyckades beror det på att distributionen eller det virtuella nätverket innehåller ett scenario/funktion/resurs som inte stöds. Använd listan med scenarier som inte stöds för att hitta arbetet runt i dokumenten.  
- PREPARE-åtgärden utför verifieringen, inklusive några dyra verifieringar (ingår inte i valideringen). Det kan bero på ett scenario som inte stöds. Hitta scenariot och arbetet runt i de offentliga dokumenten. Abort måste anropas för att gå tillbaka till det ursprungliga läget och låsa upp distributionen för uppdateringar och borttagnings åtgärder.
- Försök igen om det inte gick att avbryta. Kontakta supporten om nya försök inte fungerar.
- Försök igen om det inte gick att genomföra åtgärden. Kontakta supporten om det inte går att göra ett nytt försök. Även om ett bekräftelse fel uppstår bör det inte finnas något data Plans problem i distributionen. Distributionen bör kunna hantera kund trafik utan problem. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Portalen har uppdaterats efter förberedelse. Upplevelsen startas om och commit eller abort visas inte längre. 
- Portalen lagrar migreringen lokalt och därför kommer den att starta från validerings fasen även om moln tjänsten är i förberedelse fasen.  
- Du kan använda portalen för att gå igenom validera och förbereda stegen igen för att Visa knappen Avbryt och bekräfta. Det kommer inte att orsaka några fel.
- Kunder kan använda PowerShell eller REST API för att avbryta eller genomföra. 

### <a name="how-much-time-can-the-operations-takebr"></a>Hur lång tid kan åtgärderna ta?<br>
Validate är utformat för att vara snabbt. PREPARE körs och tar lite tid beroende på det totala antalet roll instanser som migreras. Det kan också ta tid att avbryta och bekräfta, men det tar kortare tid jämfört med att förbereda. Tids gränsen för alla åtgärder upphör efter 24 timmar. 
