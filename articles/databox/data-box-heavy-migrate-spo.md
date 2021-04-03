---
title: Använd Azure Data Box Heavy för att flytta fil resurs innehåll till SharePoint Online
description: Använd den här självstudien för att lära dig hur du migrerar fil resurs innehåll till delnings platsen online med din Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: b00a944b69b8492d4aa00258154bf00f1c66ad33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92127122"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Använd Azure Data Box Heavy för att migrera ditt fil resurs innehåll till SharePoint Online

Använd Azure Data Box Heavy och verktyget för SharePoint-migrering (SPMT) för att enkelt migrera ditt fil resurs innehåll till SharePoint Online och OneDrive. Med hjälp av Data Box Heavy kan du ta bort beroendet av WAN-länken (Wide Area Network) för att överföra data.

Microsoft Azure Data Box-enhet är en tjänst som gör det möjligt att beställa en enhet från Microsoft Azure-portalen. Du kan sedan kopiera terabyte av data från dina servrar till enheten. När den har levererats tillbaka till Microsoft kopieras dina data till Azure. Beroende på storleken på de data som du vill överföra kan du välja mellan:

- [Data Box disk](./data-box-disk-overview.md) med 35-TB användbar kapacitet per order för små till medel stora data mängder.
- [Data Box-enhet](./data-box-overview.md) med 80-TB användbar kapacitet per enhet för medel stora till stora data uppsättningar.
- [Data Box Heavy](./data-box-heavy-overview.md) med 770-TB användbar kapacitet per enhet för stora data mängder.

Den här artikeln innehåller information om hur du använder Data Box Heavy för att migrera ditt fil resurs innehåll till SharePoint Online.

## <a name="requirements-and-costs"></a>Krav och kostnader

### <a name="for-data-box-heavy"></a>För Data Box Heavy

- Data Box Heavy är bara tillgängligt för Enterprise-avtal (EA), Cloud Solution Provider (CSP) eller Azure sponsring-erbjudanden. Om din prenumeration inte ingår i någon av typerna ovan kontaktar du Microsoft Support för att uppgradera din prenumeration eller se [priser för Azure-prenumeration](https://azure.microsoft.com/pricing/).
- Det finns en avgift för att använda Data Box Heavy. Se till att granska [data Box Heavy priserna](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>För SharePoint Online

- Granska [minimi kraven för SPMT (SharePoint Migration Tool)](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Översikt över arbets flöde

Det här arbets flödet kräver att du utför steg på Azure Data Box Heavy och på SharePoint Online.
Följande steg gäller för din Azure Data Box Heavy.

1. Beställ Azure Data Box Heavy.
2. Ta emot och konfigurera din enhet.
3. Kopiera data från din lokala fil resurs till en mapp för Azure Files på enheten.
4. När kopieringen är klar skickar du tillbaka enheten enligt anvisningarna.
5. Vänta tills data har laddats upp fullständigt till Azure.

Följande steg rör SharePoint Online.

6. Skapa en virtuell dator i Azure Portal och montera Azure-filresursen på den.
7. Installera SPMT-verktyget på den virtuella Azure-datorn.
8. Kör SPMT-verktyget med Azure-filresursen som *källa*.
9. Slutför de sista stegen i verktyget.
10. Verifiera och bekräfta dina data.

## <a name="use-data-box-heavy-to-copy-data"></a>Använd Data Box Heavy för att kopiera data

Utför följande steg för att kopiera data till din Data Box Heavy.

1. [Beställ dina data Box Heavy](data-box-heavy-deploy-ordered.md).
2. När du har fått Data Box Heavy [konfigurerar du data Box Heavy](data-box-heavy-deploy-set-up.md). Du ska kablar och konfigurera båda noderna på enheten.
3. [Kopiera data till Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). När du kopierar, se till att:

    - Använd endast mappen *StorageAccountName_AzFile* i data Box Heavy för att kopiera data. Detta beror på att du vill att data ska slutanvändas i en Azure-filresurs, inte i block-blobbar eller sid-blobar.
    - Kopiera filer till en mapp i *StorageAccountName_AzFile* mapp. En undermapp i *StorageAccountName_AzFile* mapp skapar en fil resurs. Filer som kopierats direkt till *StorageAccountName_AzFile* mapp avbryts och överförs som block-blobar. Detta är fil resursen som du monterar på den virtuella datorn i nästa steg.
    - Kopiera data till båda noderna i Data Box Heavy.
3. Kör [Förbered för att skicka](data-box-heavy-deploy-picked-up.md#prepare-to-ship) på enheten. En lyckad förberedelse för att leverera säkerställer att filer överförs till Azure.
4. [Returnera enheten](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verifiera data överföringen till Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Använda SPMT för att migrera data

När du har fått en bekräftelse från Azure Data team som data kopieringen har slutförts fortsätter du med att migrera dina data till SharePoint Online.

För bästa prestanda och anslutning rekommenderar vi att du skapar en virtuell Azure-dator (VM).

1. Logga in på Azure Portal och skapa sedan [en virtuell dator](../virtual-machines/windows/quick-create-portal.md).
2. [Montera Azure-filresursen på den virtuella datorn](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Hämta verktyget för SharePoint-migrering](https://spmtreleasescus.blob.core.windows.net/install/default.htm) och installera det på den virtuella Azure-datorn.
4. Starta verktyget SharePoint-migrering. Klicka på **Logga** in och ange användar namn och lösen ord för ditt arbets-eller skol konto.
5. När du tillfrågas **om var data finns?** väljer du **fil resurs**. Ange sökvägen till Azure-filresursen där dina data finns.
6. Följ de återstående prompterna som vanligt, inklusive mål platsen. Mer information finns i så här [använder du verktyget för SharePoint-migrering](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Den hastighet med vilken data matas in i SharePoint Online påverkas av flera faktorer, oavsett om du redan har dina data i Azure. Genom att förstå dessa faktorer kan du planera och maximera effektiviteten hos migreringen.  Mer information finns i [SharePoint Online och migrerings hastigheten för OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Det finns risk för att du förlorar befintliga behörigheter för filer när du migrerar data till SharePoint Online. Du kan också förlora vissa metadata, till exempel *skapade av* och senast *ändrad av*.

## <a name="next-steps"></a>Nästa steg

[Beställ dina Data Box Heavy](./data-box-heavy-deploy-ordered.md)