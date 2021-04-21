---
title: Så här administrerar du Azure Cache for Redis
description: Lär dig hur du utför administrationsuppgifter, till exempel starta om och schemalägga uppdateringar för Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7f8eb44ab301b211eaeb2dc3679c97714f91f0da
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833048"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Så här administrerar du Azure Cache for Redis
Det här avsnittet beskriver hur du utför administrationsuppgifter som [att starta om](#reboot) och schemalägga [uppdateringar](#schedule-updates) för Azure Cache for Redis instanser.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Starta om
På **bladet** Starta om kan du starta om en eller flera noder i cacheminnet. Med den här omstartsfunktionerna kan du testa programmet för återhämtning om det uppstår fel på en cachenod.

![Skärmbild som visar menyalternativet Starta om.](./media/cache-administration/redis-cache-administration-reboot.png)

Välj de noder som ska startas om och klicka på **Starta om.**

![Skärmbild som visar vilka noder du kan starta om.](./media/cache-administration/redis-cache-reboot.png)

Om du har en premium-cache med klustring aktiverat kan du välja vilka fragment av cacheminnet som ska startas om.

![Starta om](./media/cache-administration/redis-cache-reboot-cluster.png)

Om du vill starta om en eller flera noder i cacheminnet väljer du önskade noder och klickar på **Starta om.** Om du har en premium-cache med klustring aktiverat väljer du önskade shards för att starta om och klickar sedan på **Starta om.** Efter några minuter startar de valda noderna om och är online igen några minuter senare.

Effekten på klientprogram varierar beroende på vilka noder du startar om.

* **Master** – När den primära noden startas om Azure Cache for Redis till repliknoden och befordrar den till primär. Under den här redundansen kan det finnas ett kort intervall där anslutningar kan misslyckas till cachen.
* **Replik** – När repliknoden startas om påverkas vanligtvis inte cacheklienter.
* **Både primär och replik** – När båda cachenoderna startas om går alla data förlorade i cacheminnet och anslutningarna till cachen misslyckas tills den primära noden är online igen. Om du har konfigurerat [datapersistence](cache-how-to-premium-persistence.md)återställs den senaste säkerhetskopian när cachen är online igen, men eventuella cache-skrivningar som har inträffat efter den senaste säkerhetskopieringen går förlorade.
* **Noder i en Premium-cache** med klustring aktiverat – När du startar om en eller flera noder i en premiumcache med klustring aktiverat är beteendet för de valda noderna detsamma som när du startar om motsvarande nod eller noder i en icke-klustrad cache.

## <a name="reboot-faq"></a>Vanliga frågor och svar om omstart
* [Vilken nod ska jag starta om för att testa mitt program?](#which-node-should-i-reboot-to-test-my-application)
* [Kan jag starta om cacheminnet för att rensa klientanslutningar?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Kommer jag att förlora data från mitt cacheminne om jag startar om?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Kan jag starta om mitt cacheminne med PowerShell, CLI eller andra hanteringsverktyg?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Vilken nod ska jag starta om för att testa mitt program?
Starta om huvudnoden för att testa programmets återhämtning mot  fel i den primära noden i cacheminnet. Starta om repliknoden för att testa programmets återhämtning mot fel på **repliknoden.** Om du vill testa programmets återhämtning mot ett totalt fel i cacheminnet startar du om **Båda noderna.**

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kan jag starta om cacheminnet för att rensa klientanslutningar?
Ja, om du startar om cachen rensas alla klientanslutningar. Omstart kan vara användbart om alla klientanslutningar används på grund av ett logikfel eller en bugg i klientprogrammet. Varje prisnivå har olika [klientanslutningsgränser](cache-configure.md#default-redis-server-configuration) för de olika storlekarna, och när dessa gränser har nåtts godkänns inga fler klientanslutningar. Genom att starta om cacheminnet kan du rensa alla klientanslutningar.

> [!IMPORTANT]
> Om du startar om cacheminnet för att rensa klientanslutningar återansluter StackExchange.Redis automatiskt när Redis-noden är online igen. Om det underliggande problemet inte är löst kan klientanslutningarna fortsätta att vara slut.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Kommer jag att förlora data från mitt cacheminne om jag startar om?
Om du startar  om  både huvudnoderna och repliknoderna kan alla data i cacheminnet (eller i den fragmentet om du använder en premium-cache med klustring aktiverat) gå förlorade, men detta garanteras inte heller. Om du har konfigurerat [datapersistence](cache-how-to-premium-persistence.md)återställs den senaste säkerhetskopian när cachen är online igen, men eventuella cache-skrivningar som har inträffat efter säkerhetskopieringen går förlorade.

Om du startar om bara en av noderna går data normalt inte förlorade, men det kan fortfarande vara det. Om till exempel den primära noden startas om och en cacheskrivning pågår går data från cacheskrivningen förlorade. Ett annat scenario för dataförlust är om du startar om en nod och den andra noden går ned på grund av ett fel samtidigt. Mer information om möjliga orsaker till dataförlust finns i [Vad hände med mina data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kan jag starta om mitt cacheminne med PowerShell, CLI eller andra hanteringsverktyg?
Ja, PowerShell-instruktioner finns i [Starta om en Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Schemauppdateringar
På **bladet Schemalägg** uppdateringar kan du ange en underhållsfönstret för din cacheinstans. Med en underhållstid kan du styra vilka dagar och tidpunkter i veckan som de virtuella datorer som är värdar för din cache kan uppdateras. Azure Cache for Redis gör bästa möjliga försök att starta och slutföra uppdateringen av Redis-serverprogramvaran inom den angivna tidsperiod som du definierar.

> [!NOTE] 
> Underhållsfönstret gäller redis-serveruppdateringar och uppdateringar av operativsystemet för de virtuella datorer som är värdar för cachen. Underhållsfönstret gäller inte uppdateringar av värdoperativsystemet för värdar som är värdar för de virtuella cachedatorerna eller andra Azure-nätverkstjänster komponenter. I sällsynta fall, där cacheminnen finns på äldre modeller (du kan se om ditt cacheminne finns på en äldre modell om DNS-namnet på cacheminnet matchar suffixet "cloudapp.net", "chinacloudapp.cn", "usgovcloudapi.net" eller "cloudapi.de") gäller underhållsfönstret inte heller för gästoperativsystemuppdateringar.
>


![Schemauppdateringar](./media/cache-administration/redis-schedule-updates.png)

Om du vill ange en underhållstid kontrollerar du önskade dagar och anger underhållsfönstrets starttid för varje dag och klickar på **OK.** Observera att underhållsfönstrets tid är i UTC. 

Standard- och minsta underhållsfönstret för uppdateringar är fem timmar. Det här värdet kan inte konfigureras från Azure Portal, men du kan konfigurera det i PowerShell med hjälp av parametern för `MaintenanceWindow` cmdleten [New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Mer information finns i Kan jag hantera schemalagda uppdateringar med PowerShell, CLI eller andra hanteringsverktyg?

## <a name="schedule-updates-faq"></a>Vanliga frågor och svar om schemauppdateringar
* [När sker uppdateringar om jag inte använder funktionen för schemauppdateringar?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Vilken typ av uppdateringar görs under den schemalagda underhållsfönstret?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kan jag hantera schemalagda uppdateringar med PowerShell, CLI eller andra hanteringsverktyg?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>När sker uppdateringar om jag inte använder funktionen för schemauppdateringar?
Om du inte anger någon underhållstid kan uppdateringar göras när som helst.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Vilken typ av uppdateringar görs under den schemalagda underhållsfönstret?
Endast Redis-serveruppdateringar görs under den schemalagda underhållsfönstret. Underhållsfönstret gäller inte för Azure-uppdateringar eller uppdateringar av operativsystemet för den virtuella datorn.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kan jag hantera schemalagda uppdateringar med PowerShell, CLI eller andra hanteringsverktyg?
Ja, du kan hantera schemalagda uppdateringar med hjälp av följande PowerShell-cmdlets:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure Cache for Redis funktioner.

* [Azure Cache for Redis tjänstnivåer](cache-overview.md#service-tiers)

