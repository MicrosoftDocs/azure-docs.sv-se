---
title: Meddelanden om underhåll
description: Översikt över underhålls meddelanden för virtuella datorer som körs i Azure.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 92cb780a80f1010fd1c2f5d19fe616e0285de73b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564534"
---
# <a name="handling-planned-maintenance-notifications"></a>Hantera meddelanden om planerat underhåll

Azure utför med jämna mellanrum uppdateringar för att förbättra värdinfrastrukturens tillförlitlighet, prestanda och säkerhet för virtuella datorer. Uppdateringar är ändringar som att korrigera värd miljön eller uppgradera och inaktivera maskin vara. En majoritet av dessa uppdateringar slutförs utan att det påverkar de virtuella datorer som är värdar. Det finns dock fall där uppdateringar påverkar:

- Om underhållet inte kräver en omstart pausar Azure den virtuella datorn i några sekunder medan värden uppdateras. De här typerna av underhålls åtgärder tillämpas på fel domän av fel domän. Förloppet stoppas om eventuella varnings hälso signaler tas emot.

- Om underhåll kräver en omstart får du ett meddelande om när underhållet planeras. Du får ett tidsfönster på cirka 35 dagar där du kan starta underhållet själv när det passar dig.


Planerat underhåll som kräver en omstart är schemalagt i vågor. Varje våg har olika omfång (regioner).

- En våg börjar med ett meddelande till kunderna. Som standard skickas meddelandet till prenumerations administratören och medadministratörerna. Du kan lägga till fler mottagare och meddelande alternativ, t. ex. e-post, SMS och Webhooks, med [aktivitets logg aviseringar](../service-health/alerts-activity-log-service-notifications-portal.md).  
- När ett meddelande skickas görs ett *självbetjänings fönster* tillgängligt. Under det här fönstret kan du fråga vilka av dina virtuella datorer som påverkas och starta underhållet utifrån dina egna schemaläggnings behov. Självbetjänings fönstret är normalt cirka 35 dagar.
- Efter självbetjänings fönstret startar en *schemalagd underhålls period* . Vid något tillfälle under det här fönstret schemalägger Azure och tillämpar det nödvändiga underhållet på den virtuella datorn. 

Målet med två Windows är att ge dig tillräckligt med tid för att starta underhållet och starta om den virtuella datorn samtidigt som du vet när Azure startar underhåll automatiskt.


Du kan använda Azure Portal, PowerShell, REST API och CLI för att fråga efter underhålls Fönstren för dina virtuella datorer och starta självbetjänings underhållet.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Bör du starta underhåll med hjälp av självbetjänings fönstret?  

Följande rikt linjer hjälper dig att bestämma om du vill använda den här funktionen och starta underhållet vid din egen tidpunkt. 

> [!NOTE] 
> Självbetjänings underhåll kanske inte är tillgängligt för alla dina virtuella datorer. Du kan ta reda på om proaktiv omdistribution är tillgängligt för den virtuella datorn genom att leta reda på **Start nu** i underhålls status. Självbetjänings underhåll är för närvarande inte tillgängligt för Cloud Services (webb-och arbets roll) och Service Fabric.


Självbetjänings underhåll rekommenderas inte för distributioner med hjälp av **tillgänglighets uppsättningar**. Tillgänglighets uppsättningar uppdateras redan en uppdaterings domän i taget. 

- Låt Azure utlösa underhållet. För underhåll som kräver omstart görs underhållet av en uppdaterings domän. Uppdaterings domänerna får inte nödvändigt vis underhållet sekventiellt, och det finns en 30 minuters paus mellan uppdaterings domäner. 
- Om en tillfällig förlust av viss kapacitet (1 uppdaterings domän) är ett problem, kan du lägga till instanser under underhålls perioden. 
- För underhåll som inte kräver omstart tillämpas uppdateringar på fel domän nivå. 

Använd **inte** självbetjänings underhåll i följande scenarier: 
- Om du stänger av de virtuella datorerna ofta, antingen manuellt, med DevTest Labs, med automatisk avstängning eller efter ett schema, kan det återställa underhålls statusen och därmed orsaka ytterligare nedtid.
- På virtuella datorer med korta livs längder som du vet kommer att tas bort innan underhålls vågen är slut. 
- För arbets belastningar med stor status lagrad på den lokala (tillfälliga) disken som du vill behålla vid uppdateringen. 
- I de fall där du ändrar storlek på den virtuella datorn ofta, eftersom det kan återställa underhålls statusen. 
- Om du har antagit schemalagda händelser som aktiverar proaktiv redundans eller en korrekt avstängning av arbets belastningen, 15 minuter innan underhålls avstängning påbörjas

**Använd** självbetjänings underhåll, om du planerar att köra den virtuella datorn utan avbrott under fasen för schemalagt underhåll och ingen av räknarna som anges ovan är tillämpliga. 

Det är bäst att använda självbetjänings underhåll i följande fall:
- Du måste kommunicera med en exakt underhålls period till din hantering eller till slut kunden. 
- Du måste slutföra underhållet vid ett visst datum. 
- Du måste kontrol lera sekvensen av underhåll, till exempel ett program på flera nivåer för att garantera säker återställning.
- Det krävs mer än 30 minuters återställnings tid för virtuella datorer mellan två uppdaterings domäner (UDs). Om du vill kontrol lera tiden mellan uppdaterings domäner måste du aktivera underhåll av virtuella datorer en uppdaterings domän (UD) i taget.


## <a name="faq"></a>Vanliga frågor


**F: Varför måste du starta om mina virtuella datorer nu?**

**A:** Majoriteten av uppdateringar och uppgraderingar av Azure-plattformen påverkar inte den virtuella datorns tillgänglighet, men det finns fall där vi inte kan undvika att starta om virtuella datorer som finns i Azure. Vi har samlat flera ändringar som kräver att vi startar om våra servrar som kommer att leda till att virtuella datorer startas om.

**F: om jag följer dina rekommendationer för hög tillgänglighet med hjälp av en tillgänglighets uppsättning, är jag säker?**

**A:** Virtuella datorer som distribueras i en tillgänglighets uppsättning eller skalnings uppsättningar för virtuella datorer har begreppet uppdaterings domäner (UD). När du utför underhåll följer Azure UD-begränsningen och startar inte om virtuella datorer från olika UD (inom samma tillgänglighets uppsättning).  Azure väntar också i minst 30 minuter innan det flyttas till nästa grupp med virtuella datorer. 

Mer information om hög tillgänglighet finns i [tillgänglighet för virtuella datorer i Azure](availability.md).

**F: Hur gör jag för att få ett meddelande om planerat underhåll?**

**A:** En planerad underhålls våg börjar genom att ange ett schema för en eller flera Azure-regioner. Strax efter skickas ett e-postmeddelande till prenumerations administratörerna, medadministratörer, ägare och deltagare (ett e-postmeddelande per prenumeration). Ytterligare kanaler och mottagare för det här meddelandet kan konfigureras med aktivitets logg aviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll redan har schemalagts, får du inte meddelandet, utan du behöver kontrol lera underhålls statusen för den virtuella datorn.

**F: Jag ser inga uppgifter om planerat underhåll i portalen, PowerShell eller CLI. Vad är fel?**

**A:** Information som rör planerat underhåll är bara tillgänglig under en planerad underhålls våg för de virtuella datorer som ska påverkas av den. Om du inte ser några data kan det bero på att underhålls vågen redan har slutförts (eller inte startats) eller att den virtuella datorn redan finns på en uppdaterad Server.

**F: är det något sätt att veta exakt när min virtuella dator påverkas?**

**A:** När du anger ett schema definierar vi en tids period på flera dagar. Men den exakta ordningsföljden av servrar (och virtuella datorer) i det här fönstret är okänd. Kunder som vill veta exakt hur lång tid det tar för sina virtuella datorer att använda [schemalagda händelser](./linux/scheduled-events.md) och frågor från den virtuella datorn och få en 15 minuters avisering innan en virtuell dator startas om.

**F: hur lång tid tar det att starta om min virtuella dator?**

**A:**  Beroende på storleken på den virtuella datorn kan det ta upp till flera minuter att starta om i fönstret för självbetjänings underhåll. Under den Azure-initierade omstarten i det schemalagda underhålls fönstret tar det vanligt vis ungefär 25 minuter att starta om. Observera att om du använder Cloud Services (webb-eller arbets roll), Virtual Machine Scale Sets eller tillgänglighets uppsättningar får du 30 minuter mellan varje grupp av virtuella datorer (UD) under den schemalagda underhålls perioden.

**F: Vad händer med Virtual Machine Scale Sets?**

**A:** Planerat underhåll är nu tillgängligt för Virtual Machine Scale Sets. Instruktioner för hur du startar självbetjänings underhållet hänvisa [planerat underhåll för Virtual Machine Scale set](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) -dokument.

**F: Vad händer med Cloud Services (webb-och arbets roll) och Service Fabric?**

**A:** Även om dessa plattformar påverkas av planerat underhåll anses kunder som använder dessa plattformar som säkra att endast virtuella datorer i en enda uppgraderings domän (UD) kommer att påverkas vid en viss tidpunkt. Självbetjänings underhåll är för närvarande inte tillgängligt för Cloud Services (webb-och arbets roll) och Service Fabric.

**F: Jag ser ingen underhålls information på mina virtuella datorer. Vad gick fel?**

**A:** Det finns flera orsaker till varför du inte ser någon underhålls information på dina virtuella datorer:
1.  Du använder en prenumeration som marker ATS som Microsoft Internal.
2.  De virtuella datorerna är inte schemalagda för underhåll. Det kan vara att underhålls vågen har slutat, avbrutits eller ändrats så att dina virtuella datorer inte längre påverkas av den.
3. Du har avallokerat den virtuella datorn och startat den. Detta kan leda till att den virtuella datorn flyttas till en plats som inte har planerat planerad underhålls våg. Det innebär att den virtuella datorn inte visar någon information om underhållet. 
4.  Du har inte lagt till **underhålls** kolumnen i listvyn för din VM. Vi har lagt till den här kolumnen i standardvyn, men kunder som har konfigurerat för att se kolumner som inte är standard måste manuellt lägga till **underhålls** kolumnen i sin listvy för virtuella datorer.

**F: min virtuella dator är schemalagd för underhåll för andra gången. Varför?**

**A:** Det finns flera användnings fall där du ser den virtuella datorn som är schemalagd för underhåll när du redan har slutfört din underhålls omdistribution:
1.  Vi har avbrutit underhålls vågen och startat om den med en annan nytto Last. Det kan bero på att vi har upptäckt en felaktig nytto last och att vi bara behöver distribuera ytterligare en nytto Last.
2.  Den virtuella datorn har *tjänsten lagas* till en annan nod på grund av ett maskin varu fel.
3.  Du har valt att stoppa (frigöra) och starta om den virtuella datorn.
4.  Du har aktiverat **Automatisk avstängning** för den virtuella datorn.



## <a name="next-steps"></a>Nästa steg

Du kan hantera planerat underhåll med hjälp av [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md).
