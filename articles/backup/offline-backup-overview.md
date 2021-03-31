---
title: Översikt över säkerhets kopiering offline
description: Läs mer om komponenterna i säkerhets kopiering offline. De inkluderar offline-säkerhetskopiering som baseras på Azure Data Box och offline-säkerhetskopiering baserat på Azure import/export-tjänsten.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704143"
---
# <a name="overview-of-offline-backup"></a>Översikt över säkerhets kopiering offline

Den här artikeln ger en översikt över säkerhets kopiering offline.

Första fullständiga säkerhets kopieringar till Azure överför vanligt vis stora mängder data online och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför stegvisa ändringar. Fjärranslutna kontor eller data Center i vissa geografiska områden har inte alltid tillräckligt med nätverks bandbredd. Av den anledningen tar dessa inledande säkerhets kopieringar flera dagar. Under den här tiden använder säkerhets kopieringarna alltid samma nätverk som etablerades för program som körs i det lokala data centret.

Azure Backup stöder säkerhets kopiering offline, som överför de första säkerhets kopierings data offline, utan att nätverks bandbredden används. Den innehåller en mekanism för att kopiera säkerhets kopierings data till fysiska lagrings enheter. Enheterna levereras sedan till ett närliggande Azure-datacenter och överförs till ett Recovery Services-valv. Den här processen säkerställer robust överföring av säkerhets kopierings data utan att använda någon nätverks bandbredd.

## <a name="offline-backup-options"></a>Alternativ för säkerhets kopiering offline

Offline-säkerhetskopiering erbjuds i två lägen baserat på ägarskapet för lagrings enheterna:

- Offline-säkerhetskopiering baserat på Azure Data Box (förhands granskning)
- Offline-säkerhetskopiering baserat på Azure import/export-tjänsten

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Offline-säkerhetskopiering baserat på Azure Data Box (förhands granskning)

Det här läget stöds för närvarande med Microsoft Azure Recovery Services (MARS)-agenten i för hands versionen. Det här alternativet drar nytta av [Azure Data Box](https://azure.microsoft.com/services/databox/) för att leverera Microsoft-patentskyddade, säkra och Manipulerings beständiga överförings apparater med USB-anslutningar till ditt data Center eller fjärrkontoret. Säkerhetskopierade data skrivs direkt till dessa enheter. Det här alternativet sparar den ansträngning som krävs för att skaffa dina egna Azure-kompatibla diskar och anslutningar eller för att etablera tillfällig lagring som en mellanlagringsplats. Microsoft hanterar även överförings logistiken från slut punkt till slut punkt, som du kan spåra genom Azure Portal.

En arkitektur som beskriver förflyttningen av säkerhetskopierade data med det här alternativet visas här.

![Azure Backup Data Box-enhet arkitektur](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Här är en sammanfattning av arkitekturen:

1. Azure Backup kopierar säkerhetskopierade data direkt till dessa förkonfigurerade enheter.
2. Sedan kan du skicka tillbaka enheterna till ett Azure-datacenter.
3. Azure Data Box kopierar data till ett kundägda lagrings konto.
4. Azure Backup kopierar automatiskt säkerhetskopierade data från lagrings kontot till det angivna Recovery Services valvet. Stegvisa säkerhets kopieringar är schemalagda.

Om du vill använda offline-säkerhetskopiering baserat på Azure Data Box, se [säkerhets kopiering offline med Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Offline-säkerhetskopiering baserat på Azure import/export-tjänsten

Det här alternativet stöds av Microsoft Azure Backup Server (MABS), System Center Data Protection Manager (DPM) DPM-A och MARS-agenten. [Tjänsten Azure import/export](../import-export/storage-import-export-service.md)används. Du kan överföra första säkerhets kopierings data till Azure med hjälp av dina egna Azure-kompatibla diskar och anslutningar. Den här metoden kräver att du etablerar tillfällig lagring som kallas mellanlagringsplats och använder förinställda verktyg för att formatera och kopiera säkerhetskopierade data till kundägda diskar.

En arkitektur som beskriver förflyttningen av säkerhetskopierade data med det här alternativet visas här.

![Azure Backup import/export-tjänstens arkitektur](./media/offline-backup-overview/azure-backup-import-export.png)

Här är en sammanfattning av arkitekturen:

1. I stället för att skicka säkerhetskopierade data via nätverket skriver Azure Backup säkerhets kopierings data till en mellanlagringsplats.
2. Data på mellanlagringsplatsen skrivs till en eller flera SATA-diskar med hjälp av ett anpassat verktyg.
3. Som en del av det förberedande arbetet skapar verktyget ett Azure-importerat jobb. SATA-enheterna levereras till närmaste Azure-datacenter och refererar till import jobbet för att ansluta aktiviteterna.
4. På Azure-datacentret kopieras data på diskarna till ett Azure Storage-konto.
5. Azure Backup kopierar säkerhetskopierade data från lagrings kontot till Recovery Services-valvet. Stegvisa säkerhets kopieringar är schemalagda.

Om du vill använda offline-säkerhetskopiering baserat på Azure import/export-tjänsten med MARS-agenten, se [arbets flöde för säkerhets kopiering offline i Azure Backup](./backup-azure-backup-import-export.md).

Om du vill använda samma tillsammans med MABS eller DPM-A, se [arbets flöde för offline-säkerhetskopiering för DPM och Azure Backup Server](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Översikt över stöd för offline-säkerhetskopiering

I följande tabell jämförs de två tillgängliga alternativen så att du kan göra lämpliga val baserat på ditt scenario.

| **Att tänka på**                                            | **Offline-säkerhetskopiering baserat på Azure Data Box**                     | **Offline-säkerhetskopiering baserat på Azure import/export-tjänsten**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup distributions modeller                              | MARS-agent (för hands version)                                              | MARS-agent, MABS, DPM-A                                           |
| Högsta antal säkerhets kopierings data per server (MARS) eller per skydds grupp (MABS, DPM-A) | [Azure Data Box disk](../databox/data-box-disk-overview.md) -7,2 TB <br> [Azure Data Box](../databox/data-box-overview.md) -80 TB       | 80 TB (upp till 10 diskar av 8 TB var)                          |
| Säkerhet (data, enhet och tjänst)                           | [Data](../databox/data-box-security.md#data-box-data-protection) AES 256-bit-krypterad <br> [Enhet](../databox/data-box-security.md#data-box-device-protection) – robust väska, patentskyddat, Credential-baserat gränssnitt för att kopiera data <br> [Tjänst](../databox/data-box-security.md#data-box-service-protection) – skyddas av säkerhetsfunktioner i Azure | Data-BitLocker-krypterad                                 |
| Tillfällig etablering av lagrings plats                     | Krävs inte                                                | Mer än eller lika med den beräknade säkerhets kopierings data storleken        |
| Regioner som stöds                                           | [Azure Data Box disk regioner](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box regioner](../databox/data-box-disk-overview.md#region-availability) | [Azure import/export-tjänst regioner](../import-export/storage-import-export-service.md#region-availability) |
| Leverans mellan länder                                     | Stöds inte  <br>    Käll adress och mål Azure-datacenter måste finnas i samma land/region * | Stöds                                                    |
| Överför logistik (leverans, transport, upphämtning)           | Fullständigt Microsoft-hanterat                                     | Kund hantering                                            |
| Priser                                                      | [Azure Data Box priser](https://azure.microsoft.com/pricing/details/databox/) <br> [Priser för Azure Data Box disken](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prissättning för Azure import/export-tjänsten](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Om ditt land/din region inte har något Azure-datacenter, måste du leverera diskarna till ett Azure-datacenter i ett annat land/en annan region.

## <a name="next-steps"></a>Nästa steg

- [Azure Backup offline-säkerhetskopiering med Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Arbets flöde för offline-säkerhetskopiering i Azure Backup](backup-azure-backup-import-export.md)
- [Arbets flöde för offline-säkerhetskopiering för DPM och Azure Backup Server](backup-azure-backup-server-import-export.md)
