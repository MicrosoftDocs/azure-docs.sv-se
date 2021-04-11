---
title: Distribuera Azure Arc data Controller | Direct Connect-läge
description: Förklarar hur du distribuerar datakontrollanten i Direct Connect-läge.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031496"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Distribuera Azure Arc data Controller | Direct Connect-läge

Den här artikeln beskriver hur du distribuerar Azure Arc-datakontrollanten i Direct Connect-läge under den aktuella för hands versionen av den här funktionen. 

För närvarande kan du skapa data styrenheten för Azure-bågen från Azure Portal. Andra verktyg för Azure Arc-aktiverade data tjänster har inte stöd för att skapa datakontrollanten i Direct Connect-läge. Mer information finns i [kända problem – Azure Arc-aktiverade data tjänster (för hands version)](known-issues.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Slutför nödvändiga komponenter

Innan du börjar ska du kontrol lera att du har slutfört kraven i [distribuera datakontrollant – Direct Connect-läge-krav](deploy-data-controller-direct-mode-prerequisites.md).

Från en hög nivå förklarar den här artikeln hur du utför dessa uppgifter:

1. Skapa ett Azure Arc-aktiverat Data Services-tillägg. 
1. Skapa en anpassad plats.
1. Distribuera data styrenheten från portalen.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Skapa ett Azure Arc-aktiverat Data Services-tillägg

Använd K8s-Extension CLI för att skapa ett Data Services-tillägg.

### <a name="set-environment-variables"></a>Ange miljövariabler

Ange följande miljövariabler som ska användas i nästa steg.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Skapa Arc Data Services-tillägget

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Det kan ta några minuter att slutföra installationen av Arc Data Services-tillägget.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Kontrol lera att Arc Data Services-tillägget har skapats

Du kan kontrol lera om ARC Enabled Data Services-tillägget skapas antingen från portalen eller genom att ansluta direkt till Arc-Kubernetes-klustret. 

#### <a name="azure-portal"></a>Azure Portal
1. Logga in på Azure Portal och bläddra till resurs gruppen där den anslutna kluster resursen Kubernetes finns.
1. Välj det Arc-aktiverade Kubernetes-klustret (Type = "Kubernetes-Azure Arc") där tillägget distribuerades.
1. I navigeringen till vänster går du till **Inställningar** och väljer "tillägg (förhands granskning)".
1. Du bör se tillägget som nyss skapades tidigare i ett "installerat" tillstånd.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Instrument panel för tillägg":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. Anslut till ditt Kubernetes-kluster via ett terminalfönster.
1. Kör kommandot nedan och se till att det (1) namnrymd som nämns ovan har skapats och (2) att `bootstrapper` pod är i läget "körs" innan du fortsätter till nästa steg.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Följande hämtar till exempel poddar från `arc` namn område.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Skapa en anpassad plats med anpassad CLI-tillägg för plats

En anpassad plats är en Azure-resurs som motsvarar ett namn område i ett Kubernetes-kluster.  Anpassade platser används som mål för att distribuera resurser till eller från Azure. Lär dig mer om anpassade platser i de [anpassade platserna ovanpå Azure Arc-aktiverade Kubernetes-dokumentation](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Ange miljövariabler

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Verifiera att den anpassade platsen har skapats

Från terminalen kör du kommandot nedan för att visa en lista med de anpassade platserna och kontrollerar att **etablerings statusen** har slutförts:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Skapa data styrenheten för Azure-bågen

När du har skapat tillägget och den anpassade platsen fortsätter du till Azure Portal för att distribuera data styrenheten för Azure-bågen.

1. Logga in på Azure-portalen.
1. Sök efter "Azure Arc data Controller" på Azure Marketplace och starta skapa flödet.
1. I avsnittet **krav** kontrollerar du att Azure-Arc-Kubernetes-klustret (direkt läge) har marker ATS och fortsätter till nästa steg.
1. I avsnittet **data styrenhets information** väljer du en prenumeration och en resurs grupp.
1. Ange ett namn för data kontrollen.
1. Välj en konfigurations profil som baseras på Kubernetes som du distribuerar till.
1. Välj den anpassade plats som du skapade i föregående steg.
1. Ange information för Administratörs inloggning och lösen ord för data hanterare.
1. Ange information om ClientId, TenantId och klient hemlighet för tjänstens huvud namn som ska användas för att skapa Azure-objekten. Mer information om hur du skapar ett tjänst huvud namns konto och de roller som behövs för kontot finns i [Ladda upp mått](upload-metrics-and-logs-to-azure-monitor.md) .
1. Klicka på **Nästa**, granska sammanfattnings sidan för all information och klicka på **skapa**.

## <a name="monitor-the-creation"></a>Övervaka skapandet

När distributions statusen för Azure Portal visar att distributionen lyckades kan du kontrol lera statusen för migreringen av Arc-datakontrollanten på klustret på följande sätt:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Nästa steg

[Skapa en Azure-Arc-aktiverad PostgreSQL Hyperskala-servergrupp](create-postgresql-hyperscale-server-group.md)

[Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md)
