---
title: Alternativ för Azure data överföring för stora data uppsättningar med låg eller ingen nätverks bandbredd | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för data överföring när du har begränsat till ingen nätverks bandbredd i din miljö och du planerar att överföra stora data mängder.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9b83ba106b35a0a3abd035e85f60c4c39bbadd3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704648"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Dataöverföring för stora datamängder med låg eller ingen nätverksbandbredd
 
Den här artikeln ger en översikt över data överförings lösningarna när du har begränsat till ingen nätverks bandbredd i din miljö och du planerar att överföra stora data mängder. Artikeln beskriver också rekommenderade alternativ för data överföring och respektive nyckel funktions mat ris för det här scenariot.

Om du vill veta en översikt över alla tillgängliga alternativ för data överföring går du till [Välj en Azure Data Transfer-lösning](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offline-överföring eller nätverks överföring

Stora data uppsättningar innebär att du har några TBs till några data för PBs. Du har begränsad till ingen nätverks bandbredd, nätverket är långsamt eller är inte tillförlitligt. Du kan också:

- Du är begränsad till kostnader för nätverks överföring från Internet leverantörer (ISP).
- Säkerhets-eller organisations principer tillåter inte utgående anslutningar vid hantering av känsliga data.

I alla instanser ovan använder du en fysisk enhet för att göra en engångs Mass överföring av data. Välj mellan Data Box Disk Data Box-enhet, Data Box Heavy enheter som tillhandahålls av Microsoft eller importera/exportera med hjälp av dina egna diskar.

För att bekräfta om en fysisk enhet är rätt alternativ, använder du följande tabell. Den visar den planerade tiden för nätverks data överföring för olika tillgängliga bandbredder (förutsatt 90% användning). Om nätverks överföringen projiceras för att bli långsam bör du använda en fysisk enhet.  

![Nätverks överföring eller offline-överföring](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Rekommenderade alternativ

Alternativen som är tillgängliga i det här scenariot är enheter för Azure Data Box offline-överföring eller Azure import/export.

- **Azure Data boxs familj för offline-överföringar** – Använd enheter från Microsoft-data Box-enhet enheter för att flytta stora mängder data till Azure när du är begränsad till tid, nätverks tillgänglighet eller kostnader. Kopiera lokala data med hjälp av verktyg som Robocopy. Beroende på hur mycket data du tänkt överföra kan du välja mellan Data Box Disk, Data Box eller Data Box Heavy.
- **Azure import/export** – Använd Azures import-/export tjänst genom att leverera egna disk enheter för att på ett säkert sätt importera stora mängder data till Azure Blob storage och Azure Files. Du kan också använda den här tjänsten till att överföra data från Azure Blob Storage till diskenheter som sedan levereras till dig lokalt.

## <a name="comparison-of-key-capabilities"></a>Jämförelse av viktiga funktioner

I den här tabellen sammanfattas skillnaderna mellan de viktigaste funktionerna.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box – tung              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Data storlek**                    |    Upp till 35 TBs                 |    Upp till 80 TBs per enhet                       |    Upp till 800 TB per enhet               |    Variabel                            |
|    **Datatyp**                    |    Azure-blobar                  |    Azure-blobar<br>Azure Files                    |    Azure-blobar<br>Azure Files            |    Azure-blobar<br>Azure Files          |
|    **Form faktor**                  |    5 SSD per order             |    1 X 50 – kg. enhet för Skriv bords storlek per beställning    |    1 X ~ 500 – kg. stor enhet per beställning    |    Upp till 10 hård diskar/SSD per beställning        |
|    **Första installations tid**           |    Låg <br>(15 minuter)            |    Låg till måttlig <br> (<30 minuter)               |    Medel<br>(1-2 timmar)               |    Måttligt till svårt<br>variabel |
|    **Skicka data till Azure**           |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    **Exportera data från Azure**       |    Inga                           |    Inga                                            |    Inga                                    |    Ja                                 |
|    **Kryptering**                   |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    **Maskinvara**                     |     Microsoft tillhandahålls          |    Microsoft tillhandahålls                            |    Microsoft tillhandahålls                    |    Kunden har tillhandahållit                   |
|    **Nätverksgränssnitt**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Partnerintegration**          |    Vissa                         |    [Hög](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hög](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Vissa                                |
|    **Frakt**                     |    Microsoft-hanterat            |    Microsoft-hanterat                             |    Microsoft-hanterat                     |    Kund hantering                    |
| **Använd när data flyttas**     |Inom en handels avgränsning|Inom en handels avgränsning|Inom en handels avgränsning|Över geografiska gränser, t. ex. USA till EU|
|    **Prissättning**                      |    [Prissättning](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prissättning](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Nästa steg

- Förstå hur du

    - [Överför data med data Box disk](../../databox/data-box-disk-quickstart-portal.md).
    - [Överför data med data Box-enhet](../../databox/data-box-quickstart-portal.md).
    - [Överför data med import/export](../../import-export/storage-import-export-data-to-blobs.md).