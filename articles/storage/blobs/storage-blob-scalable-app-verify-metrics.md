---
title: Kontrollera måtten för dataflöde och svarstid för ett lagringskonto i Azure Portal | Microsoft Docs
description: Lär dig hur du kontrollerar måtten för dataflöde och svarstid för ett lagringskonto i Portal.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 1bec42dabde64fed8126e47e39f19536b28014f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714841"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Kontrollera måtten för dataflöde och svarstid för ett lagringskonto

Den här kursen är den fjärde och sista delen i en serie. I tidigare självstudiekurser har du lärt dig hur du laddar upp och hämtar stora mängder slumpmässiga data till ett Azure-lagringskonto. I den här kursen visar vi hur du kan använda mått för att visa dataflöde och svarstid i Azure Portal.

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Konfigurera diagram i Azure Portal
> * Verifiera mått för dataflöde och svarstid

För [Azure-lagringsmått](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) använder du Övervaka i Azure som ger en enhetlig vy över prestanda och tillgänglighet för ditt lagringskonto.

## <a name="configure-metrics"></a>Konfigurera mått

Navigera till **mått** under **Inställningar** i ditt lagrings konto.

Välj Blob i listrutan **UNDERORDNAD TJÄNST**.

Under **MÅTT** väljer du något av de mått som finns i följande tabell:

Följande mått ger dig en uppfattning om programmets svarstid och dataflöde. De mått som du konfigurerar i Portal har minutmedelvärden. Om en transaktion har varit i mitten av en minut som minut data är hälften av genomsnittet. I programmet registreras tiden det tar att ladda upp och hämta data och detta visas i utdata så att du kan se den faktiska tid det tog att ladda upp och ladda ned filerna. Du kan använda den här informationen med måtten i portalen för att få en full förståelse av dataflödet.

|Metric|Definition|
|---|---|
|**Lyckad E2E-svarstid**|Den genomsnittliga svarstiden från slutpunkt till slutpunkt för lyckade begäranden som gjorts till en lagringstjänst eller för en angiven API-åtgärd. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|
|**Lyckad Server svars tid**|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency. |
|**Transaktioner**|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. I det här exemplet angavs blockstorleken till 100 MB. I det här fallet betraktas varje block på 100 MB som en transaktion.|
|**Ingress**|Mängden inkommande data. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. |
|**Utgående**|Mängden utgående data. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. |

Välj **Senaste 24 timmarna (automatisk)** bredvid **Tid**. Välj **Senaste timmen** och **Minut** för **Tidskornighet** och klicka sedan på **Tillämpa**.

![Mått för lagringskonto](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Diagram kan ha fler än ett mått tilldelade till dem, men om du tilldelar fler än ett mått inaktiveras möjligheten till gruppering efter dimension.

## <a name="dimensions"></a>Dimensioner

[Dimensioner](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) används för att få en djupare inblick i diagrammen och få mer detaljerad information. Olika mått har olika dimensioner. En dimension som du kan använda är **API-namn**. Den här dimensionen delar upp diagrammet i varje separat API-anrop. Den första bilden nedan visar ett exempeldiagram över det totala antalet transaktioner för ett lagringskonto. Den andra bilden visar samma diagram, men med dimensionen API-namn vald. Som du ser visas varje transaktion med mer detaljerad information om hur många anrop som har gjorts per API-namn.

![Mått för lagringskonto – transaktioner utan dimension](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Mått för lagringskonto – transaktioner](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Det gör du genom att välja resursgruppen för den virtuella datorn och klicka på Ta bort.

## <a name="next-steps"></a>Nästa steg

I den fjärde delen i serien lärde dig hur du visar mått för exempellösningen. Du fick till exempel lära dig att:

> [!div class="checklist"]
> * Konfigurera diagram i Azure Portal
> * Verifiera mått för dataflöde och svarstid

Följ den här länken om du vill se inbyggda lagringsexempel.

> [!div class="nextstepaction"]
> [Azure Storage-skriptexempel](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md