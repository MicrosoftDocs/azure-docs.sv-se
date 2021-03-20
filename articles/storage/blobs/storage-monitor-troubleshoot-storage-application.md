---
title: Övervaka och felsöka ett molnlagringsprogram i Azure | Microsoft Docs
description: Använda diagnostiska verktyg, mått och aviseringar för att felsöka och övervaka ett molnprogram.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95544793"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Övervaka och felsöka ett molnlagringsprogram

Den här kursen är den fjärde och sista delen i en serie. Du får lära dig hur du övervakar och felsöker ett molnlagringsprogram.

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Aktivera loggning och mått
> * Aktivera aviseringar för auktoriseringsfel
> * Köra testtrafik med felaktiga SAS-token
> * Ladda ned och analysera loggar

Med [Azure Storage Analytics](../common/storage-analytics.md) kan du logga och visa statistik för ett lagringskonto. Dessa data ger insikter om hälsotillståndet för ditt lagringskonto. Om du vill samla in data från Azure-lagringsanalys kan du konfigurera loggning, mått och aviseringar. Den här processen innefattar att aktivera loggning, konfigurera mått och aktivera aviseringar.

Loggning och mått från lagringskonton aktiveras på fliken **Diagnostik** i Azure Portal. Med loggningen i Storage kan du registrera information för både lyckade och misslyckade begäranden i ditt lagringskonto. I loggarna kan du se information om läs-, skriv- och borttagningsåtgärder för dina tabeller, köer och blobar i Azure. Du kan också se orsaker till varför begäranden har misslyckats, till exempel tidsgränser som uppnåtts, nätverksbegränsning och auktoriseringsfel.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Aktivera loggning och mått

På menyn till vänster väljer du **Resursgrupper**, **myResourceGroup** och sedan ditt lagringskonto i resurslistan.

Under **Diagnostikinställningar (klassisk)** anger du **Status** till **På**. Kontrollera att alla alternativ under **Blobegenskaper** är aktiverade.

När du är klar klickar du på **Spara**

![Skärm bild som visar avsnittet som innehåller konfigurations inställningarna för att aktivera loggning och mått.](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Aktivera aviseringar

E-postadministratörer kan använda aviseringar för att utlösa en webhook när ett tröskelvärde uppnås för ett mått. I det här exemplet aktiverar du en avisering för måttet `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navigera till lagringskontot i Azure Portal

Under avsnittet **Övervakning** väljer du **Aviseringar (klassisk)**.

Välj **Lägg till måttavisering (klassisk)** och slutför formuläret **Lägg till regel** genom att fylla i nödvändig information. Från listrutan **Mått** väljer du `SASClientOtherError`. För att tillåta att aviseringen utlöses vid det första felet går du till listrutan **Villkor** och väljer **Större än eller lika med**.

![Fönstret Diagnostik](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Simulera ett fel

För att simulera en giltig avisering kan du försöka begära en blob som inte finns från ditt lagringskonto. Följande kommando kräver ett namn på lagringscontainer. Du kan använda antingen namnet på en befintlig container eller skapa ett nytt för det här exemplet.

Ersätt platshållarna med riktiga värden (se till att `<INCORRECT_BLOB_NAME>` anges till ett värde som inte finns) och kör kommandot.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

På bilden nedan visas ett exempel på hur det kan se ut när ett sådant simulerat fel körs.

 ![Exempelavisering](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Ladda ned och visa loggar

Storage-loggar lagrar data i en uppsättning blobar i en blobcontainer med namnet **$logs** i ditt lagringskonto. Den här containern visas inte om du visar alla blobcontainrar i ditt konto i en lista, men du kan se innehållet om du går direkt till containern.

I det här scenariot använder du [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) för att interagera med ditt Azure-lagringskonto.

### <a name="download-microsoft-message-analyzer"></a>Ladda ned Microsoft Message Analyzer

Ladda ned [Microsoft Message Analyzer](/message-analyzer/installing-and-upgrading-message-analyzer) och installera programmet.

Starta programmet **och välj**  >  **Öppna**  >  **från andra fil källor**.

I dialogrutan **File Selector** (Filväljaren) väljer du **+ Add Azure Connection** (Lägg till Azure-anslutning). Ange **namnet på ditt lagringskonto** och **kontonyckeln** och klicka sedan på **OK**.

![Microsoft Message Analyzer – Dialogrutan för Azure Storage-anslutning](media/storage-monitor-troubleshoot-storage-application/figure3.png)

När du är ansluten expanderar du containrarna i lagringsträdvyn så att loggblobarna visas. Markera den senaste loggen och klicka på **OK**.

![Skärm bild som visar Microsoft Message Analyzer och markerar den valda logg filen.](media/storage-monitor-troubleshoot-storage-application/figure4.png)

I dialogrutan **New Session** (Ny session) klickar du på **Start** för att visa loggen.

När loggen har öppnats kan du se lagringshändelserna. Som du ser på följande bild utlöstes `SASClientOtherError` på lagringskontot. Mer information om lagringsloggning finns i [Lagringsanalys](../common/storage-analytics.md).

![Microsoft Message Analyzer – Visa händelser](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är ett annat verktyg som du kan använda för att interagera med dina lagringskonton, till exempel **$logs**-containern och de loggar som finns i containern.

## <a name="next-steps"></a>Nästa steg

I den fjärde och sista delen i serien har du lärt dig hur du övervakar och felsöker ditt lagringskonto, till exempel hur du gör för att:

> [!div class="checklist"]
> * Aktivera loggning och mått
> * Aktivera aviseringar för auktoriseringsfel
> * Köra testtrafik med felaktiga SAS-token
> * Ladda ned och analysera loggar

Följ den här länken om du vill se inbyggda lagringsexempel.

> [!div class="nextstepaction"]
> [Azure Storage-skriptexempel](storage-samples-blobs-cli.md)