---
title: StorageBlobSelector-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Storage. StorageBlobSelector UI för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754621"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. Storage. StorageBlobSelector

En kontroll för att välja en BLOB från ett Azure Storage-konto.

## <a name="ui-sample"></a>UI-exempel

Användaren visas med alternativet att bläddra efter tillgängliga lagrings blobbar.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft. Storage. StorageBlobSelector – Bläddra":::

När du har valt **Bläddra** kan användaren välja ett lagrings konto.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft. Storage. StorageBlobSelector – Välj lagring":::

Användaren ser behållare i lagrings kontot och kan välja ett.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft. Storage. StorageBlobSelector – Välj behållare":::

Användaren kan välja en fil från behållaren.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft. Storage. StorageBlobSelector – fil":::

Kontrollen uppdateras för att visa det valda fil namnet.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft. Storage. StorageBlobSelector – Visa Välj fil":::

## <a name="schema"></a>Schema

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Exempelutdata

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Kommentarer

Egenskapen **constraints. allowedFileExtensions** anger de tillåtna fil typerna.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
