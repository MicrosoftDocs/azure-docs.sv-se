---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88658470"
---
<!-- ### Upload files with the CLI -->

Följande kommando laddar upp en enda fil.  

Ändra följande värden:

1. Ändra `/path/to/file` till filens lokala sökväg.  
1. Ändra `MyContainer` till en till gång som du tidigare skapat med hjälp av till gångs-ID: t (inte namnet).
1. Ändra `MyBlob` till det namn som du vill använda för den överförda filen.
1. Ändra `MyStorageAccountName` till namnet på det lagrings konto som du använder.
1. Ändra `MyStorageAccountKey` till åtkomst nyckeln för ditt lagrings konto.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
