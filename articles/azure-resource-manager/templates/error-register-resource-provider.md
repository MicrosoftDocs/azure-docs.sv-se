---
title: Registrerings fel för resurs leverantör
description: Beskriver hur du löser registrerings fel i Azure Resource Provider när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 143cf03a33739f43a29af94fc2f8a336fb3aef8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89076675"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Åtgärda fel vid registreringen av resursprovidern

I den här artikeln beskrivs de fel som kan uppstå när du använder en resurs leverantör som du inte tidigare har använt i din prenumeration.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

När du distribuerar en resurs kan du få följande felkod och meddelande:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Eller så kan du få ett liknande meddelande som säger:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Fel meddelandet bör ge förslag på platser och API-versioner som stöds. Du kan ändra mallen till ett av de föreslagna värdena. De flesta leverantörer registreras automatiskt av Azure Portal eller kommando rads gränssnittet som du använder, men inte alla. Om du inte har använt en viss resurs leverantör tidigare kan du behöva registrera providern.

Eller, när du inaktiverar automatisk avstängning för virtuella datorer, kan du få ett fel meddelande som liknar:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Orsak

Du får de här felen av någon av följande orsaker:

* Den obligatoriska resurs leverantören har inte registrerats för din prenumeration
* API-versionen stöds inte för resurs typen
* Platsen stöds inte för resurs typen
* För automatisk avstängning av virtuella datorer måste Microsoft. DevTestLab-resurs leverantören vara registrerad.

## <a name="solution-1---powershell"></a>Lösning 1 – PowerShell

För PowerShell använder du **Get-AzResourceProvider** för att se din registrerings status.

```powershell
Get-AzResourceProvider -ListAvailable
```

Registrera en provider genom att använda **register-AzResourceProvider** och ange namnet på den resurs leverantör som du vill registrera.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Använd följande för att hämta platser som stöds för en viss typ av resurs:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Använd följande om du vill hämta API-versioner som stöds för en viss typ av resurs:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Lösning 2 – Azure CLI

Om du vill se om providern är registrerad använder du `az provider list` kommandot.

```azurecli-interactive
az provider list
```

Registrera en resurs leverantör genom att använda `az provider register` kommandot och ange *namn området* som ska registreras.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Om du vill se platser och API-versioner som stöds för en resurs typ använder du:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Lösning 3 – Azure Portal

Du kan se registrerings status och registrera ett namn område för en resurs leverantör via portalen.

1. Från portalen väljer du **alla tjänster**.

   ![Välj alla tjänster](./media/error-register-resource-provider/select-all-services.png)

1. Välj **Prenumerationer**.

   ![Välj prenumerationer](./media/error-register-resource-provider/select-subscriptions.png)

1. I listan över prenumerationer väljer du den prenumeration som du vill använda för att registrera resurs leverantören.

   ![Välj en prenumeration för att registrera Resource Provider](./media/error-register-resource-provider/select-subscription-to-register.png)

1. För din prenumeration väljer du **resurs leverantörer**.

   ![Välj resurs leverantörer](./media/error-register-resource-provider/select-resource-provider.png)

1. Titta på listan över resurs leverantörer och om det behövs väljer du länken **Registrera** för att registrera resurs leverantören av den typ som du försöker distribuera.

   ![Visa lista över resurs leverantörer](./media/error-register-resource-provider/list-resource-providers.png)
