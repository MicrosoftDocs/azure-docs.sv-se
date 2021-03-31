---
title: Azure cache för Redis-övervakning och fel sökning av vanliga frågor och svar
description: Läs om svaren på vanliga frågor som hjälper dig att övervaka och felsöka Azure cache för Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537532"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Azure cache för Redis-övervakning och fel sökning av vanliga frågor och svar
Den här artikeln innehåller svar på vanliga frågor om hur du övervakar och felsöker Azure cache för Redis.

## <a name="common-questions-and-answers"></a>Vanliga frågor och svar
I det här avsnittet beskrivs följande vanliga frågor och svar:

* [Hur gör jag för att övervaka hälso tillstånd och prestanda för mitt cacheminne?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Varför ser jag timeout?](#why-am-i-seeing-timeouts)
* [Varför var min klient frånkopplad från cachen?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hur gör jag för att övervaka hälso tillstånd och prestanda för mitt cacheminne?
Microsoft Azure cache för Redis-instanser kan övervakas i [Azure Portal](https://portal.azure.com). Du kan visa mått, fästa mått diagram på Start sidan, anpassa datum-och tidsintervallet för övervaknings diagram, lägga till och ta bort mått från diagrammen och ange aviseringar när vissa villkor uppfylls. Mer information finns i [övervaka Azure cache för Redis](cache-how-to-monitor.md).

**Resurs menyn** i Azure cache för Redis innehåller också flera verktyg för att övervaka och felsöka dina cacheminnen.

* **Diagnostisera och lösa problem** innehåller information om vanliga problem och strategier för att lösa dem.
* **Resource Health** bevakar din resurs och meddelar dig om den körs som förväntat. Mer information om Azure Resource Health Service finns i [Översikt över Azure Resource Health](../service-health/resource-health-overview.md).
* Den **nya support förfrågan** innehåller alternativ för att öppna en supportbegäran för din cache.

Med de här verktygen kan du övervaka Azure-cachens hälso tillstånd för Redis-instanser och hjälpa dig att hantera dina cachelagring-program. Mer information finns i avsnittet "support & fel söknings inställningar" i [så här konfigurerar du Azure cache för Redis](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Varför ser jag timeout?
Timeout sker i klienten som du använder för att prata med Redis. När ett kommando skickas till Redis-servern, köas kommandot och Redis-servern hämtar sedan kommandot och kör det. Klienten kan dock ta lång tid under den här processen och om den inträffar utlöses ett undantags fel på den anropande sidan. Mer information om hur du felsöker timeout-problem finns i [fel sökning på klient sidan](cache-troubleshoot-client.md) och [stackexchange. Redis timeout-undantag](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Varför var min klient frånkopplad från cachen?
Följande är några vanliga orsaker till att en cache kopplas från.

* Orsaker på klient Sidan
  * Klient programmet distribuerades om.
  * Klient programmet utförde en skalnings åtgärd.
    * Om Cloud Services eller Web Apps kan det bero på automatisk skalning.
  * Nätverks lagret på klient sidan har ändrats.
  * Tillfälliga fel uppstod i klienten eller på nätverks noderna mellan klienten och servern.
  * Gränserna för bandbredds tröskeln har uppnåtts.
  * PROCESSOR bindnings åtgärder tog för lång tid att slutföra.
* Orsaker på Server Sidan
  * I standard-cachen initierade Azure cache för Redis-tjänsten en redundans från den primära noden till noden replikering.
  * Azure har korrigerat den instans där cachen distribuerades
    * Detta kan vara för redis-server uppdateringar eller underhåll av den virtuella datorn.


## <a name="next-steps"></a>Nästa steg

Mer information om övervakning och fel sökning av Azure-cache för Redis-instanser finns i [så här övervakar du Azure cache för Redis](cache-how-to-monitor.md) och de olika fel söknings guiderna.

Lär dig mer om andra [Azure cache för vanliga frågor och svar om Redis](cache-faq.md).