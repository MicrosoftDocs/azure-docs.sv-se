---
title: Stödmatris för SAP HANA-säkerhetskopiering
description: I den här artikeln lär du dig om de scenarier och begränsningar som stöds när du använder Azure Backup för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: 5065f4cf3d9ab75c6e3b682b6a24387ce7449b0f
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562666"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Supportmatris för säkerhetskopiering av SAP HANA-databaser på virtuella Azure-datorer

Azure Backup stöder säkerhets kopiering av SAP HANA-databaser till Azure. Den här artikeln sammanfattar scenarier som stöds och begränsningar som finns när du använder Azure Backup för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer.

> [!NOTE]
> Frekvensen av logg säkerhets kopieringen kan nu anges till minst 15 minuter. Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering för databasen har slutförts.

## <a name="scenario-support"></a>Scenariostöd

| **Scenario**               | **Konfigurationer som stöds**                                | **Konfigurationer som inte stöds**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologi**               | Endast SAP HANA som körs i virtuella Azure Linux-datorer                    | HANA stora instanser (HLI)                                   |
| **Regioner**                   | **GA**<br> **Amerika** – centrala USA, östra USA 2, östra USA, norra centrala USA, södra centrala USA, västra USA 2, västra centrala USA, västra USA, centrala Kanada, Östra Kanada, södra Brasilien <br> **Asien och Stillahavsområdet** – Australien, centrala Australien 2, östra Australien, sydöstra Australien, Östra Japan, västra Japan, centrala Korea, södra, Asien, östra, Sydostasien, centrala Indien, södra Indien, västra indien, Kina, östra, Kina, norra, Kina östra, Kina, norra 2 <br> **Europa** – Västeuropa, Nord Europa, Frankrike Central, Storbritannien, södra, Storbritannien, västra, Tyskland, norra, Tyskland, västra centrala, Schweiz, norra, Schweiz, västra, Central Schweiz, norra, östra Norge, västra Norge <br> **Afrika/me** – Sydafrika, västra Sydafrika, västra Förenade Arabemiraten Nord, Förenade Arabemiraten Central  <BR>  **Azure Government-regioner** | Södra Frankrike, centrala Tyskland, Tyskland nordöstra, US Gov IOWA |
| **OS-versioner**            | SLES 12 med SP2, SP3, SP4 och SP5; SLES 15 med SP0, SP1, SP2 <br><br>  Från och med den 1 augusti 2020 är SAP HANA säkerhets kopiering för RHEL (7,4, 7,6, 7,7 & 8,1) allmänt tillgänglig.                |                                             |
| **HANA-versioner**          | SDC på HANA 1. x, MDC på HANA 2. x <= SPS04 rev 53, SPS05 (som ännu ska verifieras för krypterings aktiverade scenarier)      |                                                            |
| **HANA-distributioner**       | SAP HANA på en enda virtuell Azure VM-skalning. <br><br> För distributioner med hög tillgänglighet behandlas båda noderna på de två olika datorerna som enskilda noder med separata data kedjor.               | Utskalning <br><br> Vid distributioner med hög tillgänglighet redundansväxlas inte säkerhets kopieringen till den sekundära noden automatiskt. Konfiguration av säkerhets kopiering bör göras separat för varje nod.                                           |
| **HANA-instanser**         | En enda SAP HANA instans på en enda virtuell Azure-dator – skala upp | Flera SAP HANA-instanser på en enskild virtuell dator. Du kan bara skydda en av dessa flera instanser i taget.                  |
| **HANA-databas typer**    | Enkel databas container (SDC) på 1. x, MDC (Multi-Database container) på 2. x | MDC i HANA 1. x                                              |
| **HANA-databasens storlek**     | HANA-databaser med storlek <= 2 TB (detta är inte minnes storleken för HANA-systemet)               |                                                              |
| **Säkerhets kopierings typer**           | Fullständiga, differentiella, stegvisa (för hands version) och logg säkerhets kopior                          |  Ögonblicksbilder                                       |
| **Återställningstyper**          | Läs SAP HANA anmärkning [1642148](https://launchpad.support.sap.com/#/notes/1642148) om du vill veta mer om vilka återställnings typer som stöds |                                                              |
| **Säkerhets kopierings gränser**          | Upp till 2 TB fullständig säkerhets kopierings storlek per SAP HANA instans (mjuk gräns)         |                                                              |
| **Särskilda konfigurationer** |                                                              | SAP HANA + dynamisk nivå <br>  Kloning via LaMa        |

------

>[!NOTE]
>Azure Backup anpassas inte automatiskt för sommar tids ändringar vid säkerhets kopiering av en SAP HANA databas som körs på en virtuell Azure-dator.
>
>Ändra principen manuellt efter behov.

> [!NOTE]
> Nu kan du [övervaka säkerhets kopierings-och återställnings](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) jobben (till samma dator) som har utlösts från Hana-ursprungliga klienter (SAP HANA Studio/cockpit/DBA-cockpit) i Azure Portal.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [säkerhetskopierar SAP HANA databaser som körs på virtuella Azure-datorer](./backup-azure-sap-hana-database.md)
* Lär dig hur du [återställer SAP HANA databaser som körs på virtuella Azure-datorer](./sap-hana-db-restore.md)
* Lär dig hur du [hanterar SAP HANA databaser som säkerhets kopie ras med Azure Backup](sap-hana-db-manage.md)
* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA databaser](./backup-azure-sap-hana-database-troubleshoot.md)
