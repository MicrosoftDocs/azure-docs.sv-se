---
title: Lägga Azure Storage (container)
description: Lär dig hur du kopplar en anpassad nätverksresurs i en containeriserad app i Azure App Service. Dela filer mellan appar, fjärrhantera statiskt innehåll och få åtkomst lokalt osv.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777625"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Använda Azure Storage (förhandsversion) som en nätverksresurs från en container i App Service

::: zone pivot="container-windows"

Den här guiden visar hur du kopplar Azure Storage Files som en nätverksresurs till en Windows-container i App Service. Endast [Azure Files-resurser](../storage/files/storage-how-to-use-files-cli.md) [och Premium-filresurser](../storage/files/storage-how-to-create-file-share.md) stöds. Fördelarna är skyddat innehåll, innehållsportabilitet, åtkomst till flera appar och flera överföringsmetoder.

> [!NOTE]
>Azure Storage i App Service är i **förhandsversion och** **stöds inte för** **produktionsscenarier**.

::: zone-end

::: zone pivot="container-linux"

Den här guiden visar hur du kopplar Azure Storage till en Linux-container App Service. Fördelarna är skyddat innehåll, innehållsportabilitet, beständig lagring, åtkomst till flera appar och flera överföringsmetoder.

> [!NOTE]
>Azure Storage finns App Service **förhandsversion för** App Service på Linux och Web App for Containers. Det stöds **inte för** **produktionsscenarier**.

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="container-windows"

- [En befintlig Windows-containerapp i Azure App Service](quickstart-custom-container.md)
- [Skapa En Azure-filresurs](../storage/files/storage-how-to-use-files-cli.md)
- [Ladda upp filer till Azure-filresursen](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- En befintlig [App Service på Linux app](index.yml).
- Ett [Azure Storage konto](../storage/common/storage-account-create.md?tabs=azure-cli)
- En [Azure-filresurs och katalog](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Files lagring som inte är standard och faktureras separat, ingår inte i webbappen. Det stöder inte användning av brandväggskonfiguration på grund av infrastrukturbegränsningar.
>

## <a name="limitations"></a>Begränsningar

::: zone pivot="container-windows"

- Azure Storage i App Service stöds för närvarande **inte** för bring your own code-scenarier (icke-containerbaserade Windows-appar).
- Azure Storage i App Service stöder **inte användning av** storage **firewall-konfigurationen** på grund av infrastrukturbegränsningar.
- Azure Storage med App Service kan du ange **upp till fem** monteringspunkter per app.
- Azure Storage som monterats i en app kan inte nås via ftp App Service/FTPs-slutpunkter. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Azure Storage i App Service har stöd **för Azure Files** (läsa/skriva) och **Azure Blob-containrar** (skrivskyddade)
- Azure Storage i App Service kan du ange **upp till fem monteringspunkter** per app.
- Azure Storage som monterats i en app kan inte nås via ftp App Service/FTPs-slutpunkter. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Länka lagring till din app

::: zone pivot="container-windows"

När du har skapat ditt [Azure Storage-konto, filresurs](#prerequisites)och katalog kan du nu konfigurera din app med Azure Storage.

Om du vill Azure Files en resurs till en katalog i App Service-appen använder du [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) kommandot . Lagringstyp måste vara AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Du bör göra detta för alla andra kataloger som du vill ska länkas till en Azure Files resurs.

::: zone-end

::: zone pivot="container-linux"

När du har skapat ditt [Azure Storage-konto, filresurs](#prerequisites)och katalog kan du nu konfigurera din app med Azure Storage.

Om du vill montera ett lagringskonto till en katalog App Service din app använder du [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) kommandot . Lagringstyp kan vara AzureBlob eller AzureFiles. AzureFiles används i det här exemplet. Inställningen för monteringssökväg motsvarar mappen i containern som du vill montera till Azure Storage. Om du ställer in den på "/" monteras hela containern för att Azure Storage.


> [!CAUTION]
> Katalogen som anges som monteringssökväg i webbappen ska vara tom. Allt innehåll som lagras i den här katalogen tas bort när en extern montering läggs till. Om du migrerar filer för en befintlig app gör du en säkerhetskopia av din app och dess innehåll innan du börjar.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Du bör göra detta för alla andra kataloger som du vill ska länkas till ett lagringskonto.

::: zone-end

## <a name="verify-linked-storage"></a>Verifiera länkad lagring

När resursen är länkad till appen kan du kontrollera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Nästa steg

::: zone pivot="container-windows"

- [Migrera anpassad programvara till Azure App Service med en anpassad container](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Konfigurera en anpassad container.](configure-custom-container.md?pivots=platform-linux)

::: zone-end