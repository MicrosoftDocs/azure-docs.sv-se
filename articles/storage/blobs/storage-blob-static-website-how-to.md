---
title: Vara värd för en statisk webbplats i Azure Storage
description: Lär dig att hantera statiskt innehåll (HTML, CSS, Java Script och bildfiler) direkt från en behållare i ett Azure Storage GPv2-konto.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2ba0e836f1be97681d677158f5cb81c5b033e915
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008509"
---
# <a name="host-a-static-website-in-azure-storage"></a>Vara värd för en statisk webbplats i Azure Storage

Du kan hantera statiskt innehåll (HTML, CSS, Java Script och bildfiler) direkt från en behållare i ett [General-Purpose v2](../common/storage-account-create.md) -eller [BlockBlobStorage](storage-blob-create-account-block-blob.md) -konto. Mer information finns i den [statiska webbplatsen som är värd för i Azure Storage](storage-blob-static-website.md).

Den här artikeln visar hur du aktiverar statisk webbplats värd med hjälp av Azure Portal, Azure CLI eller PowerShell.

## <a name="enable-static-website-hosting"></a>Aktivera statisk webbplats värd

En statisk webbplats värd är en funktion som du måste aktivera på lagrings kontot.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

2. Leta upp ditt lagringskonto och visa kontoöversikten.

3. Välj **Statisk webbplats** för att visa konfigurationssidan för statiska webbplatser.

4. Välj **Aktivera** för att aktivera hantering av statisk webbplats för lagringskontot.

5. I fältet **index dokument namn** anger du en standard index sida (till exempel: *index.html*). 

   Standardindexsidan visas när en användare navigerar till roten för den statiska webbplatsen.  

6. I fältet **fel dokument Sök väg** anger du en standard fel sida (till exempel: *404.html*). 

   Standardfelsidan visas när en användare försöker navigera till en sida som inte finns på den statiska webbplatsen.

7. Klicka på **Spara**. Azure-portalen visar nu slutpunkten för den statiska webbplatsen. 

    ![Aktivera hantering av statisk webbplats för ett lagringskonto](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

Du kan aktivera statisk webbplats värd med hjälp av [Azure Command-Line Interface (CLI)](/cli/azure/).

1. Öppna först [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du har [installerat](/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

3. Aktivera statisk webbplats värd.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

   * Ersätt `<error-document-name>` plats hållaren med namnet på fel dokumentet som visas för användarna när en webbläsare begär en sida på din webbplats som inte finns.

   * Ersätt `<index-document-name>` plats hållaren med namnet på index dokumentet. Det här dokumentet är vanligt vis "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Du kan aktivera statisk webbplats värd med hjälp av Azure PowerShell-modulen.

1. Öppna ett Windows PowerShell-kommando fönster.

2. Kontrol lera att du har Azure PowerShell modul AZ version 0,7 eller senare.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

3. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

4. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

5. Hämta lagrings konto kontexten som definierar det lagrings konto som du vill använda.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersätt `<resource-group-name>` placeholder-värdet med namnet på din resurs grupp.

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

6. Aktivera statisk webbplats värd.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Ersätt `<error-document-name>` plats hållaren med namnet på fel dokumentet som visas för användarna när en webbläsare begär en sida på din webbplats som inte finns.

   * Ersätt `<index-document-name>` plats hållaren med namnet på index dokumentet. Det här dokumentet är vanligt vis "index.html".

---

## <a name="upload-files"></a>Ladda upp filer 

### <a name="portal"></a>[Portal](#tab/azure-portal)

De här anvisningarna visar hur du överför filer med hjälp av den version av Storage Explorer som visas i Azure Portal. Du kan dock också använda den version av [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) som körs utanför Azure Portal. Du kan använda [AzCopy](../common/storage-use-azcopy-v10.md), POWERSHELL, CLI eller anpassade program som kan ladda upp filer till **$Web** behållare för ditt konto. En steg-för-steg-självstudie som laddar upp filer med hjälp av Visual Studio Code finns i [självstudie: vara värd för en statisk webbplats på Blob Storage](./storage-blob-static-website-host.md).

1. Välj **Storage Explorer (förhandsversion)**.

2. Expandera noden **BLOB containers** och välj sedan **$Web** container.

3. Välj knappen **Ladda upp** för att ladda upp filer.

   ![Ladda upp filer](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Om du vill att webbläsaren ska visa innehållet i filen kontrollerar du att innehålls typen för filen är inställt på `text/html` . 

   ![Kontrol lera innehålls typer](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer anger automatiskt den här egenskapen till `text/html` för vanliga tillägg som `.html` . I vissa fall måste du dock ange detta själv. Om du inte anger den här egenskapen till `text/html` uppmanas användarna att ladda ned filen i stället för att återge innehållet. Om du vill ange den här egenskapen högerklickar du på filen och klickar sedan på **Egenskaper**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ladda upp objekt till *$Web* containern från en käll katalog.

I det här exemplet förutsätter vi att du kör kommandon från Azure Cloud Shell-sessionen.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Om webbläsaren gör det möjligt för användare att ladda ned filen i stället för att återge innehållet, kan du lägga till `--content-type 'text/html; charset=utf-8'` i kommandot. 

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt `<source-path>` plats hållaren med en sökväg till platsen för de filer som du vill ladda upp.

> [!NOTE]
> Om du använder en plats installation av Azure CLI kan du använda sökvägen till valfri plats på den lokala datorn (till exempel: `C:\myFolder` .
>
> Om du använder Azure Cloud Shell måste du referera till en fil resurs som är synlig för Cloud Shell. Den här platsen kan vara fil resursen för själva moln resursen eller en befintlig fil resurs som du monterar från Cloud Shell. Information om hur du gör detta finns i [Spara filer i Azure Cloud Shell](../../cloud-shell/persisting-shell-storage.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ladda upp objekt till *$Web* containern från en käll katalog.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Om webbläsaren gör det möjligt för användare att ladda ned filen i stället för att återge innehållet, kan du lägga till `-Properties @{ ContentType = "text/html; charset=utf-8";}` i kommandot.

* Ersätt `<path-to-file>` placeholder-värdet med den fullständigt kvalificerade sökvägen till den fil som du vill överföra (till exempel: `C:\temp\index.html` ).

* Ersätt `<blob-name>` placeholder-värdet med det namn som du vill ge den resulterande blobben (till exempel: `index.html` ).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Hitta webbplatsens URL

Du kan visa sidorna på din webbplats från en webbläsare med hjälp av den offentliga URL: en för webbplatsen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

I rutan som visas bredvid sidan konto översikt för ditt lagrings konto väljer du **statisk webbplats**. URL: en för din webbplats visas i fältet **primär slut punkt** .

![Mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Hitta den offentliga URL: en för din statiska webbplats med hjälp av följande kommando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt `<resource-group-name>` placeholder-värdet med namnet på din resurs grupp.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hitta den offentliga URL: en för din statiska webbplats genom att använda följande kommando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Ersätt `<resource-group-name>` placeholder-värdet med namnet på din resurs grupp.

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Aktivera mått på statiska webbplats sidor

När du har aktiverat mått rapporteras trafik statistik på filer i **$Web** -behållaren på instrument panelen mått.

1. Klicka på **mått** under **Monitor** -avsnittet på menyn lagrings konto.

   > [!div class="mx-imgBorder"]
   > ![Mått länk](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Mått data genereras genom att ansluta till olika mått-API: er. Portalen visar bara de API-medlemmar som används inom en bestämd tidsram för att endast fokusera på medlemmar som returnerar data. Det första steget är att expandera tids ramen för att säkerställa att du kan välja den nödvändiga API-medlemmen.

2. Klicka på knappen tidsram, Välj en tidsram och klicka sedan på **Använd**.

   ![Tidsintervallet för mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Välj **BLOB** från List rutan *namn område* .

   ![Namn område för Azure Storage statiska webbplats mått](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Välj sedan **utgående** mått.

   ![Skärm bild som visar mått för Azure Storage statiska webbplatser.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Välj **Sum** från *agg regerings* väljaren.

   ![Agg regerings mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klicka på knappen **Lägg till filter** och välj **API-namn** från *egenskaps* väljaren.

   ![Azure Storage statiska webbplatsers mått-API-namn](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Markera kryss rutan bredvid **GetWebContent** i *värde* väljaren för att fylla i mått rapporten.

   ![Azure Storage statiska webbplats mått GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Kryss rutan **GetWebContent** visas bara om denna API-medlem användes inom en bestämd tidsram. Portalen visar bara de API-medlemmar som används inom en bestämd tidsram för att endast fokusera på medlemmar som returnerar data. Om du inte kan hitta en speciell API-medlem i den här listan expanderar du tids ramen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar en anpassad domän med din statiska webbplats. Se [Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md).