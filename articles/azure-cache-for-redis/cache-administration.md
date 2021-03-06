---
title: Så här administrerar du Azure cache för Redis
description: Lär dig hur du utför administrations åtgärder som att starta om och schemalägga uppdateringar för Azure cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 0a79b0b5b5f21d1c75fec6b062f1ca91cfe9dd1f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219207"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Så här administrerar du Azure cache för Redis
I det här avsnittet beskrivs hur du utför administrations åtgärder som att [Starta](#reboot) om och [schemalägga uppdateringar](#schedule-updates) för Azure cache för Redis-instanser.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Starta om
På bladet **starta om** kan du starta om en eller flera noder i cacheminnet. Med den här omstarts funktionen kan du testa ditt program för återhämtning om det uppstår ett problem med en cache-nod.

![Skärm bild som visar meny alternativet starta om.](./media/cache-administration/redis-cache-administration-reboot.png)

Välj de noder som ska startas om och klicka på **starta om**.

![Skärm bild som visar vilka noder du kan starta om.](./media/cache-administration/redis-cache-reboot.png)

Om du har en Premium-cache med aktive rad kluster kan du välja vilken Shards som ska startas om.

![Starta om](./media/cache-administration/redis-cache-reboot-cluster.png)

Om du vill starta om en eller flera noder i cacheminnet väljer du önskade noder och klickar på **starta om**. Om du har en Premium-cache med klustrad aktive rad väljer du önskad Shards för omstart och klickar sedan på **starta om**. Efter ett par minuter startar om de valda noderna och är online igen några minuter senare.

Påverkan på klient program varierar beroende på vilka noder du startar om.

* **Master** – när den primära noden startas om växlar Azure cache för Redis över till noden replikering och höjer den till primär. Under den här redundansväxlingen kan det finnas ett kort intervall där anslutningar kan Miss lyckas med cachen.
* **Replik** – när replik-noden startas om är det vanligt vis ingen påverkan på cachelagring av klienter.
* **Både primär och replik** – när båda cache-noderna startas om, går alla data förlorade i cacheminnet och anslutningar till cacheminnet misslyckades tills den primära noden är online igen. Om du har konfigurerat [data beständighet](cache-how-to-premium-persistence.md)återställs den senaste säkerhets kopian när cachen är online igen, men alla cache skrivningar som inträffat efter den senaste säkerhets kopieringen går förlorade.
* **Noder i en Premium-cache med aktive rad kluster** – när du startar om en eller flera noder i en Premium-cache med klustrad aktive rad är beteendet för de valda noderna detsamma som när du startar om motsvarande nod eller noder i en icke-klustrad cache.

## <a name="reboot-faq"></a>Vanliga frågor och svar om omstart
* [Vilken nod ska jag starta om för att testa mitt program?](#which-node-should-i-reboot-to-test-my-application)
* [Kan jag starta om cacheminnet för att rensa klient anslutningar?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Kommer jag att förlora data från mitt cacheminne om jag gör en omstart?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Kan jag starta om mitt cacheminne med PowerShell, CLI eller andra hanterings verktyg?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Vilken nod ska jag starta om för att testa mitt program?
Om du vill testa återhämtnings förmågan för ditt program mot att det inte går att utföra den primära noden i cacheminnet startar du om **huvud** noden. Om du vill testa återhämtnings förmågan för ditt program mot Miss lyckas med att replikera noden, startar du om noden **replikering** . Om du vill testa programmets återhämtning mot det totala antalet felaktiga cacheminnen startar du om **båda** noderna.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kan jag starta om cacheminnet för att rensa klient anslutningar?
Ja, om du startar om cacheminnet rensas alla klient anslutningar. Det kan vara användbart att starta om alla klient anslutningar på grund av ett logiskt fel eller en bugg i klient programmet. Varje pris nivå har olika [klient anslutnings gränser](cache-configure.md#default-redis-server-configuration) för olika storlekar, och när gränserna har nåtts godkänns inga fler klient anslutningar. Att starta om cacheminnet gör det möjligt att rensa alla klient anslutningar.

> [!IMPORTANT]
> Om du startar om cacheminnet för att rensa klient anslutningar återansluter StackExchange. Redis automatiskt när Redis-noden är online igen. Om det underliggande problemet inte är löst, kan klient anslutningarna fortsätta att användas.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Kommer jag att förlora data från mitt cacheminne om jag gör en omstart?
Om du startar om både **huvud** -och **replik** -noderna kan alla data i cachen (eller i den Shard om du använder en Premium-cache med klustring aktive rad) gå förlorade, men detta garanterar inte något. Om du har konfigurerat [data beständighet](cache-how-to-premium-persistence.md)återställs den senaste säkerhets kopian när cachen är online igen, men alla cache-skrivningar som har inträffat efter att säkerhets kopieringen gjordes går förlorade.

Om du bara startar om en av noderna, försvinner normalt inte data, men det kan fortfarande vara. Till exempel om den primära noden startas om och en cache-skrivning pågår går data från cache-skrivningen förlorade. Ett annat scenario för data förlust är om du startar om en nod och den andra noden inträffar på grund av ett problem på samma gång. Mer information om möjliga orsaker till data förlust finns i [vad hände med mina data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kan jag starta om mitt cacheminne med PowerShell, CLI eller andra hanterings verktyg?
Ja, för PowerShell-instruktioner, se [så här startar du om en Azure-cache för Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Schemauppdateringar
På bladet **schema uppdateringar** kan du ange ett underhålls fönster för din cache-instans. Med en underhålls period kan du kontrol lera dagar och tidpunkter för en vecka under vilken de virtuella datorerna som är värdar för din cache kan uppdateras. Azure cache för Redis hjälper dig att starta och slutföra uppdateringen av Redis-serverprogrammet inom den angivna tids perioden som du definierar.

> [!NOTE] 
> Underhålls perioden gäller för redis server-uppdateringar och uppdateringar av operativ systemet för de virtuella datorer som är värdar för cachen. Underhålls perioden gäller inte för värdar för OS-uppdateringar till värdarna som är värdar för cache-VM: ar eller andra Azure nätverks komponenter. I sällsynta fall, där cacheminnen finns i äldre modeller (du kan kontrol lera om cacheminnet finns på en äldre modell om DNS-namnet på cachen matchar suffixet "cloudapp.net", "chinacloudapp.cn", "usgovcloudapi.net" eller "cloudapi.de"), gäller inte underhålls perioden för gäst operativ system uppdateringar.
>


![Schemauppdateringar](./media/cache-administration/redis-schedule-updates.png)

Om du vill ange en underhålls period kontrollerar du önskade dagar och anger start timme för underhålls perioden för varje dag och klickar på **OK**. Observera att tiden för underhålls perioden är UTC. 

Standard-och minimi underhålls perioden för uppdateringar är fem timmar. Det här värdet kan inte konfigureras från Azure Portal, men du kan konfigurera det i PowerShell med `MaintenanceWindow` parametern för cmdleten [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) . Mer information finns i kan jag hantera schemalagda uppdateringar med hjälp av PowerShell, CLI eller andra hanterings verktyg?

## <a name="schedule-updates-faq"></a>Vanliga frågor och svar om schemalagda uppdateringar
* [När sker uppdateringar om jag inte använder funktionen schemalagda uppdateringar?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Vilken typ av uppdateringar görs under den schemalagda underhålls perioden?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kan jag hantera schemalagda uppdateringar med PowerShell, CLI eller andra hanterings verktyg?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>När sker uppdateringar om jag inte använder funktionen schemalagda uppdateringar?
Om du inte anger någon underhålls period kan du göra uppdateringar när som helst.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Vilken typ av uppdateringar görs under den schemalagda underhålls perioden?
Endast redis server-uppdateringar görs under det schemalagda underhålls fönstret. Underhålls perioden gäller inte för Azure-uppdateringar eller uppdateringar av den virtuella datorns operativ system.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kan jag hantera schemalagda uppdateringar med PowerShell, CLI eller andra hanterings verktyg?
Ja, du kan hantera dina schemalagda uppdateringar med följande PowerShell-cmdletar:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis-tjänst nivåer](cache-overview.md#service-tiers)

