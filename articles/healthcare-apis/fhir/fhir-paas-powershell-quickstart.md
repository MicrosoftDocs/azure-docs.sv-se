---
title: 'Snabb start: Distribuera Azure API för FHIR med PowerShell'
description: I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR med hjälp av PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.openlocfilehash: ca20346f4c2968eac5e7308cb05b59f968a1c63e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019604"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Snabb start: Distribuera Azure API för FHIR med PowerShell

I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR med hjälp av PowerShell.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Registrera Azure API för FHIR Resource Provider

Om `Microsoft.HealthcareApis` resurs leverantören inte redan har registrerats för din prenumeration kan du registrera den med:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Skapa Azure-resurs grupp

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Distribuera Azure API för FHIR

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> Beroende på vilken version av `Az` PowerShell-modulen som du har installerat kan den etablerade FHIR-servern konfigureras för att använda [lokala RBAC](configure-local-rbac.md) och ha den inloggade PowerShell-användaren i listan över tillåtna identitets objekt-ID: n för den distribuerade FHIR-tjänsten. Vi rekommenderar att du [använder Azure RBAC](configure-azure-rbac.md) för att tilldela data Plans roller och du kan behöva ta bort användarens objekt-ID efter distributionen för att aktivera Azure RBAC-läge.


## <a name="fetch-capability-statement"></a>Hämta kapacitets instruktion

Du kan kontrol lera att Azure API för FHIR-kontot körs genom att hämta en FHIR-kapacitets sats:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort resurs gruppen med följande steg:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure API för FHIR i din prenumeration. Om du vill ange ytterligare inställningar i Azure-API: et för FHIR går du vidare till ytterligare inställningar instruktions guide. Läs mer om hur du registrerar program om du är redo att börja använda Azure API för FHIR.

>[!div class="nextstepaction"]
>[Ytterligare inställningar i Azure API för FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Översikt över program register](fhir-app-registration.md)
