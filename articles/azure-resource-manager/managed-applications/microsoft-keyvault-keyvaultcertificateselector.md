---
title: KeyVaultCertificateSelector-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. KeyVaultCertificateSelector-gränssnitt för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101229"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. nyckel valv. KeyVaultCertificateSelector

En kontroll för att välja ett Key Vault-certifikat.

## <a name="ui-sample"></a>UI-exempel

Användaren visas med alternativet att välja ett tillgängligt certifikat.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. KeyVaultCertificateSelector":::

När du har valt **Välj ett certifikat** kan användaren ange ett nyckel valv och certifikat från nyckel valvet.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft. KeyVaultCertificateSelector Välj certifikat":::

Kontrollen uppdateras för att visa det valda nyckel valvet och certifikat namnet.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. KeyVaultCertificateSelector. Visa markerat certifikat":::

## <a name="schema"></a>Schema

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Exempelutdata

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
