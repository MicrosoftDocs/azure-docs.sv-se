---
title: Så här ersätter du band infrastrukturen
description: Lär dig hur Azure Backup innehåller band som liknar semantik som gör att du kan säkerhetskopiera och återställa data i Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 695cc2644521384527ecd871f3613a078e987aa7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89378448"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Flytta långsiktig lagring från band till Azure-molnet

Azure Backup-och System Center-Data Protection Manager kunder kan:

* Säkerhetskopiera data i scheman som passar organisationens behov bäst.
* Behåll säkerhets kopierings data under längre perioder.
* Gör Azure till en del av deras långsiktiga behov (i stället för på band).

Den här artikeln förklarar hur kunder kan aktivera säkerhets kopierings-och bevarande principer. Kunder som använder band för att hantera sina långsiktiga behov har nu ett kraftfullt och livskraftigt alternativ med tillgängligheten för den här funktionen. Funktionen aktive ras i den senaste versionen av Azure Backup (som finns [här](https://aka.ms/azurebackup_agent)). System Center DPM-kunder måste uppdatera till minst DPM 2012 R2-UR5 innan DPM kan använda tjänsten Azure Backup.

## <a name="what-is-the-backup-schedule"></a>Vad är schemat för säkerhets kopiering?

Säkerhets kopierings schemat visar frekvensen för säkerhets kopieringen. Inställningarna på följande skärm visar till exempel att säkerhets kopior tas dagligen vid. 18:00 och midnatt.

![Dags schema](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Kunder kan också schemalägga en veckovis säkerhets kopiering. Inställningarna på följande skärm visar till exempel att säkerhets kopiorna tas varje alternativ söndag & onsdag vid 9:10:30 och 1:10:00.

![Vecko schema](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Vad är bevarande principen?

Bevarande principen anger hur länge säkerhets kopian måste lagras. I stället för att bara ange en "platt princip" för alla säkerhets kopierings punkter kan kunder ange olika bevarande principer baserat på när säkerhets kopieringen utförs. Säkerhets kopierings punkten som tas dagligen, som fungerar som en drift återställnings punkt, bevaras i 90 dagar. Säkerhets kopierings punkten som tas i slutet av varje kvartal i gransknings syfte bevaras under en längre tid.

![Loggperiod](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Det totala antalet "kvarhållning Points" som anges i den här principen är 90 (dagliga punkter) + 40 (ett per kvartal i 10 år) = 130.

## <a name="example--putting-both-together"></a>Exempel – lägga både tillsammans

![Exempel skärm](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Bevarande princip per dag**: säkerhets kopior som tas dagligen lagras i sju dagar.
2. **Bevarande princip per vecka**: säkerhets kopieringar som gjorts kl. midnatt och lördag lördag bevaras i fyra veckor.
3. **Bevarande princip per månad**: säkerhets kopieringar som gjorts kl. midnatt och 6 PM den sista lördagen i varje månad bevaras i 12 månader.
4. **Bevarande princip per år**: säkerhets kopieringar som gjorts vid midnatt den sista lördagen i varje mars bevaras i 10 år.

Det totala antalet "kvarhållning Points" (punkter från vilka en kund kan återställa data) i föregående diagram beräknas enligt följande:

* två poäng per dag i sju dagar = 14 återställnings punkter
* två poäng per vecka i fyra veckor = 8 återställnings punkter
* två poäng per månad i 12 månader = 24 återställnings punkter
* en poäng per år per 10 år = 10 återställnings punkter

Det totala antalet återställnings punkter är 56.

> [!NOTE]
> Med Azure Backup kan du skapa upp till 9999 återställnings punkter per skyddad instans. En skyddad instans är en dator, server (fysisk eller virtuell) eller arbetsbelastning som säkerhetskopierar data till Azure.
>

## <a name="advanced-configuration"></a>Avancerad konfiguration

Genom att välja **ändra** på föregående skärm, har kunderna ytterligare möjlighet att ange bevarande scheman.

![Ändra princip fönster](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup finns i:

* [Introduktion till Azure Backup](./backup-overview.md)
* [Prova Azure Backup](./backup-windows-with-mars-agent.md)
