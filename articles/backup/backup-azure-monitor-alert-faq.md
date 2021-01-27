---
title: Vanliga frågor och svar om övervakning av avisering och rapporter
description: I den här artikeln hittar du svar på vanliga frågor om Azure Backup övervaknings avisering och Azure Backup rapporter.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805863"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup övervaknings avisering – vanliga frågor och svar

I den här artikeln besvaras vanliga frågor om Azure Backup övervakning och rapportering.

## <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Hur gör jag för att kontrol lera om rapport data har börjat flöda till en arbets yta för Log Analytics (LA)?

Navigera till den LA-arbets yta som du har konfigurerat. Navigera till meny alternativet **loggar** och kör frågan `CoreAzureBackup | take 1` . Om du ser en post som returneras innebär det att data har börjat flöda till arbets ytan. Det kan ta upp till 24 timmar innan den första data-push.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Vad är frekvensen för data-push till en LA-arbetsyta?

Diagnostikdata från valvet pumpas till Log Analytics arbets ytan med en fördröjning. Varje händelse anländer till Log Analytics arbets ytan 20 till 30 minuter efter att den har överförts från Recovery Services-valvet. Här är mer information om fördröjningen:

* I alla lösningar skickas säkerhets kopierings tjänstens inbyggda aviseringar så snart de har skapats. De visas vanligt vis i arbets ytan Log Analytics efter 20 till 30 minuter.
* I alla lösningar skickas säkerhets kopierings jobb på begäran och återställnings jobb så fort de är klara.
* För alla lösningar utom SQL-säkerhetskopiering skickas schemalagda säkerhets kopierings jobb så snart de är klara.
* För SQL-säkerhetskopiering, eftersom logg säkerhets kopieringar kan ske var 15: e minut, är information för alla schemalagda säkerhets kopierings jobb, inklusive loggar, batch och push-överförd var 6: e timme.
* Alla lösningar, annan information som säkerhets kopierings objekt, princip, återställnings punkter, lagring och så vidare, överförs minst en gång per dag.
* En ändring i konfigurationen för säkerhets kopiering (till exempel att ändra princip eller redigera princip) utlöser en push av all relaterad säkerhets kopierings information.

### <a name="how-long-can-i-retain-reporting-data"></a>Hur länge kan jag behålla rapporterings data?

När du har skapat en LA-arbetsyta kan du välja att behålla data i högst två år. Som standard behåller en LA-arbetsyta data i 31 dagar.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Ser jag alla mina data i rapporter efter att jag har konfigurerat arbets ytan LA?

 Alla data som genereras efter att du har konfigurerat diagnostikinställningar överförs till arbets ytan LA och är tillgängliga i rapporter. Pågående jobb flyttas inte för rapportering. När jobbet har slutförts eller misslyckats skickas det till rapporter.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Kan jag visa rapporter över valv och prenumerationer?

Ja, du kan visa rapporter över valv och prenumerationer samt regioner. Dina data kan finnas på en och samma LA-arbets yta eller en grupp med LA-arbetsytor.

### <a name="can-i-view-reports-across-tenants"></a>Kan jag visa rapporter mellan klienter?

Om du är en [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) -användare med delegerad åtkomst till dina kunders prenumerationer eller La arbets ytor, kan du använda säkerhets kopierings rapporter för att visa data över alla dina klienter.

## <a name="recovery-services-vault"></a>Recovery Services-valv

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Hur lång tid tar det för jobb status för Azure Backup Agent att avspeglas i portalen?

Det kan ta upp till 15 minuter för Azure Portal att återspegla jobb status för Azure Backup Agent.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Hur lång tid tar det innan en avisering utlöses när ett säkerhetskopieringsjobb misslyckas?

En avisering aktive ras inom 20 minuter från Azure Backup-felen.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Finns det ett fall där ett e-postmeddelande inte skickas om meddelanden har kon figurer ATS?

Ja. I följande situationer skickas inte meddelanden:

* Om meddelanden konfigureras varje timme och en avisering höjs och löses inom timmen
* När ett jobb avbryts
* Om ett andra säkerhets kopierings jobb Miss lyckas eftersom det ursprungliga säkerhets kopierings jobbet pågår

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

* [Vanliga frågor](backup-azure-vm-backup-faq.yml) om säkerhetskopiering av virtuella Azure-datorer.
* [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup-agenten
