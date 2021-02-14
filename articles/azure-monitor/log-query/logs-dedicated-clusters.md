---
title: Azure Monitor loggar dedicerade kluster
description: Kunder som inhämtar mer än 1 TB en dag med övervaknings data kan använda dedikerade snarare än delade kluster
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: bcd89274b1f2ba7b31a10a481e3d73901eac2c60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375195"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor loggar dedicerade kluster

Azure Monitor loggar dedikerade kluster är ett distributions alternativ som gör det möjligt att använda avancerade funktioner för Azure Monitor loggar kunder. Kunder med dedikerade kluster kan välja vilka arbets ytor som ska finnas i dessa kluster.

De funktioner som kräver dedikerade kluster är:

- **[Kundhanterade nycklar](../platform/customer-managed-keys.md)** – kryptera kluster data med hjälp av nycklar som tillhandahålls och styrs av kunden.
- **[Säker databas](../platform/customer-managed-keys.md#customer-lockbox-preview)** kan styra åtkomst begär Anden från Microsofts support tekniker för data.
- **[Dubbel kryptering](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** skyddar mot ett scenario där en av krypteringsalgoritmer eller nycklar kan komprometteras. I det här fallet fortsätter det extra krypterings lagret att skydda dina data.
- **[Flera arbets ytor](../log-query/cross-workspace-query.md)** – om en kund använder mer än en arbets yta för produktion kan det vara bra att använda dedicerat kluster. Frågor över flera arbets ytor kommer att köras snabbare om alla arbets ytor finns i samma kluster. Det kan också vara mer kostnads effektivt att använda dedicerat kluster som tilldelade kapacitets nivåer för kapacitet, ta hänsyn till all kluster inmatning och gäller alla dess arbets ytor, även om några av dem är små och inte berättigade till kapacitets reservations rabatt.

Dedikerade kluster kräver att kunderna genomför med en kapacitet på minst 1 TB data inmatning per dag. Det är enkelt att migrera till ett dedikerat kluster. Ingen data förlust eller tjänst avbrott. 

## <a name="management"></a>Hantering 

Dedikerade kluster hanteras via en Azure-resurs som representerar Azure Monitor logg kluster. Alla åtgärder utförs på den här resursen med PowerShell eller REST API.

När klustret har skapats kan det konfigureras och arbets ytorna är länkade till den. När en arbets yta är länkad till ett kluster finns nya data som skickas till arbets ytan i klustret. Endast arbets ytor i samma region som klustret kan länkas till klustret. Arbets ytor kan vara till skillnad från ett kluster med vissa begränsningar. Mer information om dessa begränsningar finns i den här artikeln. 

Data som matas in på dedikerade kluster krypteras två gånger – en gång på tjänst nivå med hjälp av Microsoft-hanterade nycklar eller [kundhanterad nyckel](../platform/customer-managed-keys.md), och en gång på infrastruktur nivå med två olika krypteringsalgoritmer och två olika nycklar. [Dubbel kryptering](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) skyddar mot ett scenario där en av krypteringsalgoritmer eller nycklar kan komprometteras. I det här fallet fortsätter det extra krypterings lagret att skydda dina data. Med dedikerat kluster kan du också skydda dina data med [Lås](../platform/customer-managed-keys.md#customer-lockbox-preview) kontroll.

Alla åtgärder på kluster nivån kräver `Microsoft.OperationalInsights/clusters/write` behörigheten åtgärd i klustret. Den här behörigheten kan beviljas via den ägare eller deltagare som innehåller `*/write` åtgärden eller via rollen Log Analytics Contributor som innehåller `Microsoft.OperationalInsights/*` åtgärden. Mer information om Log Analytics behörigheter finns [i Hantera åtkomst till logg data och arbets ytor i Azure Monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Kluster pris modell

Log Analytics dedikerade kluster använder en pris modell för kapacitets reservationer som minst 1000 GB/dag. All användning ovanför reservations nivån debiteras enligt priset för betala per användning.  Pris informationen för kapacitets reservationen finns på [sidan Azure Monitor priser]( https://azure.microsoft.com/pricing/details/monitor/).  

Reservations nivån för kluster kapaciteten konfigureras via programmering med Azure Resource Manager med hjälp av `Capacity` parametern under `Sku` . `Capacity`Anges i enheter om GB och kan ha värden på 1000 GB/dag eller mer i steg om 100 GB/dag.

Det finns två fakturerings lägen för användning i ett kluster. Dessa kan anges av- `billingType` parametern när du konfigurerar klustret. 

1. **Kluster**: i det här fallet (som är standard) görs faktureringen för inmatade data på kluster nivå. De inmatade data mängderna från varje arbets yta som är kopplad till ett kluster sammanställs för att beräkna den dagliga fakturan för klustret. 

2. **Arbets ytor**: kostnaderna för kapacitets reservationen för klustret anges i proportion till arbets ytorna i klustret (efter redovisningen av tilldelningar per nod från [Azure Security Center](../../security-center/index.yml) för varje arbets yta.)

Om din arbets yta använder pris nivån bakåtkompatibelt per nod, kommer den att faktureras baserat på data som matas in mot klustrets kapacitets reservation och inte längre per nod. Data tilldelningar per nod från Azure Security Center fortsätter att gälla.

Mer information debiteras för Log Analytics dedikerade kluster finns [här]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).

## <a name="asynchronous-operations-and-status-check"></a>Asynkrona åtgärder och status kontroll

Vissa konfigurations steg körs asynkront eftersom de inte kan slutföras snabbt. Statusen i Response kan vara något av följande: "pågår", "uppdatering", "ta bort", "lyckades eller" misslyckades ", inklusive felkoden. När du använder REST returnerar svaret ursprungligen HTTP-statuskod 202 (accepterad) och rubrik med Azure-AsyncOperation egenskap:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Du kan kontrol lera statusen för den asynkrona åtgärden genom att skicka en GET-begäran till värdet Azure-AsyncOperation huvud:

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>Skapa ett kluster

Först skapar du kluster resurser för att börja skapa ett dedikerat kluster.

Följande egenskaper måste anges:

- **Kluster** namn: används i administrations syfte. Användare visas inte för det här namnet.
- **ResourceGroupName**: för alla Azure-resurser tillhör kluster en resurs grupp. Vi rekommenderar att du använder en central IT-resurs grupp eftersom kluster vanligt vis delas av många team i organisationen. Om du vill ha mer design överväganden kan du läsa om [hur du utformar Azure Monitor loggar distribution](../platform/design-logs-deployment.md)
- **Plats**: ett kluster finns i en angiven Azure-region. Endast arbets ytor i den här regionen kan länkas till det här klustret.
- **SkuCapacity**: du måste ange *kapacitets reservations* nivån (SKU) när du skapar en *kluster* resurs. *Kapacitets reservations* nivån kan vara inom INTERVALLET 1 000 gb till 3 000 GB per dag. Du kan uppdatera den i steg om 100 senare om det behövs. Om du behöver kapacitets reservations nivå över 3 000 GB per dag kan du kontakta oss på LAIngestionRate@microsoft.com . Mer information om kluster kostnader finns i [hantera kostnader för Log Analytics kluster](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

När du har skapat en *kluster* resurs kan du redigera ytterligare egenskaper som *SKU*, * keyVaultProperties eller *billingType*. Se mer information nedan.

Du kan ha upp till 2 aktiva kluster per prenumeration per region. Om klustret tas bort är det fortfarande reserverat i 14 dagar. Du kan ha upp till 4 reserverade kluster per prenumeration per region (aktiv eller nyligen borttagen).

> [!WARNING]
> Skapande av kluster utlöser resursallokering och etablering. Det kan ta några timmar att slutföra den här åtgärden. Vi rekommenderar att du kör det asynkront.

Användar kontot som skapar klustren måste ha standard behörigheten för Azure-resurser: `Microsoft.Resources/deployments/*` och kluster Skriv behörighet `Microsoft.OperationalInsights/clusters/write` genom att ha i deras roll tilldelningar den här åtgärden eller `Microsoft.OperationalInsights/*` eller `*/write` .

### <a name="create"></a>Skapa 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Anropa* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Response*

Ska vara 202 (accepterad) och ett sidhuvud.

### <a name="check-cluster-provisioning-status"></a>Kontrol lera etablerings statusen för klustret

Det tar en stund att slutföra etableringen av det Log Analytics klustret. Du kan kontrol lera etablerings statusen på flera sätt:

- Kör Get-AzOperationalInsightsCluster PowerShell-kommandot med namnet på resurs gruppen och kontrol lera egenskapen ProvisioningState. Värdet är *ProvisioningAccount* medan *etableringen och* slutförts.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Kopiera Azure-AsyncOperation URL-värdet från svaret och följ status kontrollen asynkrona åtgärder.

- Skicka en GET-begäran på *kluster* resursen och titta på *provisioningState* -värdet. Värdet är *ProvisioningAccount* medan *etableringen och* slutförts.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **Response**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*PrincipalId* GUID genereras av den hanterade identitets tjänsten för *kluster* resursen.

## <a name="link-a-workspace-to-cluster"></a>Länka en arbets yta till ett kluster

När en arbets yta är länkad till ett dedikerat kluster dirigeras nya data som matas in i arbets ytan till det nya klustret medan befintliga data behålls i det befintliga klustret. Om det dedikerade klustret är krypterat med Kundhanterade nycklar (CMK) krypteras endast nya data med nyckeln. Systemet sammanfattar den här skillnaden från användarna och användarna skickar bara frågor till arbets ytan som vanligt medan systemet utför kors kluster frågor på Server delen.

Ett kluster kan länkas till upp till 100 arbets ytor. Länkade arbets ytor finns i samma region som klustret. För att skydda system Server delen och undvika fragmentering av data kan en arbets yta inte länkas till ett kluster mer än två gånger i månaden.

Om du vill utföra länk åtgärden måste du ha Skriv behörighet till både arbets ytan och *kluster* resursen:

- I arbets ytan: *Microsoft. OperationalInsights/arbets ytor/Write*
- I *kluster* resursen: *Microsoft. OperationalInsights/kluster/Write*

Förutom fakturerings aspekterna behåller den länkade arbets ytan sina egna inställningar, till exempel längden på data kvarhållning.
Arbets ytan och klustret kan finnas i olika prenumerationer. Det är möjligt att arbets ytan och klustret finns i olika klienter om Azure-Lighthouse används för att mappa båda till en enda klient.

Som kluster åtgärd kan länkar av en arbets yta bara utföras när Log Analytics kluster etableringen har slutförts.

> [!WARNING]
> Att länka en arbets yta till ett kluster kräver synkronisering av flera Server dels komponenter och att tillåta cachelagring av hydrering. Den här åtgärden kan ta upp till två timmar att slutföra. Vi rekommenderar att du kör det asynkront.


**PowerShell**

Använd följande PowerShell-kommando för att länka till ett kluster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Använd följande REST-anrop för att länka till ett kluster:

*Skicka*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response*

202 (accepterad) och rubrik.

### <a name="check-workspace-link-status"></a>Kontrol lera länk status för arbets ytan
  
Om du använder Kundhanterade nycklar lagras inmatade data krypterade med din hanterade nyckel efter det att associationen har utförts, vilket kan ta upp till 90 minuter att slutföra. 

Du kan kontrol lera associerings tillstånd för arbets ytan på två sätt:

- Kopiera Azure-AsyncOperation URL-värdet från svaret och följ status kontrollen asynkrona åtgärder.

- Utför åtgärden Hämta på arbets ytan och observera om egenskapen *clusterResourceId* finns i svaret under *funktioner*.

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Anropa*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*Response*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>Ändra kluster egenskaper

När du har skapat *kluster* resursen och den är helt etablerad kan du redigera ytterligare egenskaper på kluster nivå med PowerShell eller REST API. Förutom de egenskaper som är tillgängliga när klustret skapas kan ytterligare egenskaper bara anges efter att klustret har etablerats:

- **keyVaultProperties** – uppdaterar nyckeln i Azure Key Vault. Se [Uppdatera kluster med information om nyckel identifierare](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details). Den innehåller följande parametrar: *KeyVaultUri*, *attributnamn*, *version*. 
- **billingType** – egenskapen *billingType* bestämmer fakturerings behörigheten för *kluster* resursen och dess data:
  - **Kluster** (standard) – kapacitets reservationens kostnader för klustret är attribut till *kluster* resursen.
  - **Arbets ytor** – kapacitets reservationens kostnader för klustret anges i proportion till arbets ytorna i klustret, där *kluster* resursen faktureras viss användning om den totala inmatade data för dagen är under kapacitets reservationen. Se [Log Analytics dedikerade kluster](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) för att lära dig mer om kluster pris modellen. 

> [!NOTE]
> Egenskapen *billingType* stöds inte i PowerShell.

### <a name="get-all-clusters-in-resource-group"></a>Hämta alla kluster i resurs gruppen
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Anropa*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Response*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>Hämta alla kluster i prenumerationen

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Anropa*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Response*
    
Samma som för "kluster i en resurs grupp", men i prenumerations omfånget.



### <a name="update-capacity-reservation-in-cluster"></a>Uppdatera kapacitets reservation i kluster

När data volymen till dina länkade arbets ytor ändras med tiden och du vill uppdatera kapacitets reservations nivån korrekt. Kapaciteten anges i enheter om GB och kan ha värden på 1000 GB/dag eller mer i steg om 100 GB/dag. Observera att du inte behöver ange en fullständig REST-begäran, men bör inkludera SKU: n.

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Anropa*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>Uppdatera billingType i kluster

Egenskapen *billingType* bestämmer fakturerings behörighet för klustret och dess data:
- *kluster* (standard)--faktureringen tillskrivs till den prenumeration som är värd för kluster resursen
- *arbets ytor* – faktureringen hänförs till prenumerationerna som är värdar för dina arbets ytor proportionellt

**REST**

*Anropa*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>Ta bort länken mellan en arbets yta och ett kluster

Du kan ta bort länken till en arbets yta från ett kluster. När du har länkat en arbets yta från klustret skickas inte nya data som är kopplade till den här arbets ytan till det dedikerade klustret. Dessutom görs inte längre arbets ytans fakturering via klustret. Gamla data för den olänkade arbets ytan kan vara kvar i klustret. Om dessa data är krypterade med Kundhanterade nycklar (CMK) behålls Key Vault hemligheterna. Systemet är en sammanfattning av den här ändringen från Log Analytics användare. Användare kan bara fråga arbets ytan som vanligt. Systemet utför kors kluster frågor på Server delen om det behövs utan att användaren tillfrågas.  

> [!WARNING] 
> Det finns en gräns på två länknings åtgärder för en angiven arbets yta inom en månad. Ta hänsyn till och planera att ta bort länkar från åtgärder enligt detta.

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

Använd följande PowerShell-kommando för att ta bort länken till en arbets yta från klustret:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>Ta bort klustret

En dedikerad kluster resurs kan tas bort. Du måste ta bort länken mellan alla arbets ytor från klustret innan du tar bort det. Du behöver Skriv behörighet för *kluster* resursen för att utföra den här åtgärden. 

När kluster resursen har tagits bort övergår det fysiska klustret till en rensnings-och borttagnings process. Borttagning av ett kluster tar bort alla data som lagrats i klustret. Data kan vara från arbets ytor som var länkade till klustret tidigare.

En *kluster* resurs som togs bort under de senaste 14 dagarna är i läget för tyst Borttagning och kan återställas med sina data. Eftersom alla arbets ytor har kopplats bort från *kluster* resursen med *kluster* resurs borttagning måste du associera dina arbets ytor igen efter återställningen. Det går inte att utföra återställnings åtgärden av användaren kontakta din Microsoft-kanal eller support för återställnings begär Anden.

Inom 14 dagar efter borttagningen är kluster resurs namnet reserverat och kan inte användas av andra resurser.

> [!WARNING] 
> Det finns en gräns på tre kluster per prenumeration. Både aktiva och mjuka, borttagna kluster räknas som en del av detta. Kunder bör inte skapa återkommande procedurer som skapar och tar bort kluster. Det har en betydande inverkan på Log Analytics backend-system.

**PowerShell**

Använd följande PowerShell-kommando för att ta bort ett kluster:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Använd följande REST-anrop för att ta bort ett kluster:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK

## <a name="limits-and-constraints"></a>Begränsningar och begränsningar

- Det högsta antalet aktiva kluster per region och prenumeration är 2

- Det maximala antalet reserverade kluster (aktiva eller nyligen borttagna) per region och prenumeration är 4 

- Det maximala antalet länkade arbets ytor till klustret är 1000

- Du kan länka en arbets yta till klustret och sedan ta bort länken till den. Antalet länk åtgärder på arbets ytan på en viss arbets yta är begränsat till 2 under en period på 30 dagar.

- Det finns för närvarande inte stöd för att flytta kluster till en annan resurs grupp eller prenumeration.

- Den säkra databasen är inte tillgänglig i Kina. 

- [Double Encryption](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) konfigureras automatiskt för kluster som skapats från oktober 2020 i regioner som stöds. Du kan kontrol lera om klustret har kon figurer ATS för Double Encryption genom att skicka en GET-begäran i klustret och Observera att `isDoubleEncryptionEnabled` värdet är `true` för kluster med dubbla krypterings aktiverat. 
  - Om du skapar ett kluster och får ett fel meddelande om att <region namn> inte stöder Double Encryption för kluster. "kan du fortfarande skapa klustret utan dubbel kryptering genom att lägga till `"properties": {"isDoubleEncryptionEnabled": false}` i rest-begäran.
  - Inställningen Double Encryption kan inte ändras efter att klustret har skapats.

## <a name="troubleshooting"></a>Felsökning

- Om du får ett konflikt fel när du skapar ett kluster, kan det bero på att du har tagit bort klustret under de senaste 14 dagarna och att det är i ett läge med mjuk borttagning. Kluster namnet förblir reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Namnet släpps efter den mjuka borttagnings perioden när klustret tas bort permanent.

- Om du uppdaterar klustret när klustret är i etablerings-eller uppdaterings tillstånd kommer uppdateringen att Miss förklaras.

- Vissa åtgärder är långa och kan ta en stund att slutföra – dessa är kluster skapa, kluster nyckel uppdatering och kluster borttagning. Du kan kontrol lera åtgärds statusen på två sätt:
  - När du använder REST kopierar du Azure-AsyncOperation URL-värdet från svaret och följer [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
  - Skicka GET-begäran till kluster eller arbets yta och observera svaret. Till exempel kan inte en länkad arbets yta ha *clusterResourceId* under *funktioner*.

- Arbets ytans länk till klustret Miss fungerar om den är länkad till ett annat kluster.

- Felmeddelanden
  
  Skapa kluster:
  -  400--kluster namnet är ogiltigt. Kluster namnet kan innehålla tecknen a – z, A-Z, 0-9 och längden 3-63.
  -  400 – bröd texten i begäran är null eller i felaktigt format.
  -  400--SKU-namnet är ogiltigt. Ange SKU-namn till capacityReservation.
  -  400--kapaciteten tillhandahölls men SKU: n är inte capacityReservation. Ange SKU-namn till capacityReservation.
  -  400--kapacitet saknas i SKU. Ange kapacitets värde till 1000 eller högre i steg om 100 (GB).
  -  400--kapacitet i SKU är inte inom intervallet. Bör vara minst 1000 och upp till den högsta tillåtna kapaciteten som är tillgänglig under "användning och uppskattad kostnad" i din arbets yta.
  -  400 – kapaciteten är låst i 30 dagar. Minskning av kapaciteten tillåts 30 dagar efter uppdateringen.
  -  400--inget SKU har angetts. Ange SKU-namnet till capacityReservation och kapacitets värde till 1000 eller högre i steg om 100 (GB).
  -  400--identiteten är null eller tom. Ange identitet med systemAssigned-typ.
  -  400--KeyVaultProperties anges när de skapas. Uppdatera KeyVaultProperties när klustret har skapats.
  -  400--åtgärden kan inte utföras nu. Den asynkrona åtgärden är i ett annat tillstånd än lyckades. Klustret måste utföra åtgärden innan en uppdaterings åtgärd utförs.

  Kluster uppdatering
  -  400--klustrets status tas bort. En asynkron åtgärd pågår. Klustret måste utföra åtgärden innan en uppdaterings åtgärd utförs.
  -  400--KeyVaultProperties är inte tomt men har ett felaktigt format. Se [uppdatering av nyckel identifierare](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400--det gick inte att verifiera nyckeln i Key Vault. Kan bero på otillräckliga behörigheter eller när nyckeln inte finns. Kontrol lera att du [ställer in nyckel-och åtkomst principer](../platform/customer-managed-keys.md#grant-key-vault-permissions) i Key Vault.
  -  400--det går inte att återskapa nyckeln. Key Vault måste anges som mjuk borttagning och rensnings skydd. Se [Key Vault-dokumentation](../../key-vault/general/soft-delete-overview.md)
  -  400--åtgärden kan inte utföras nu. Vänta tills den asynkrona åtgärden har slutförts och försök igen.
  -  400--klustrets status tas bort. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

  Hämta kluster:
    -  404--det gick inte att hitta klustret, klustret kan ha tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den eller använda ett annat namn för att skapa ett nytt kluster. 

  Ta bort kluster
    -  409--det går inte att ta bort ett kluster i etablerings tillstånd. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

  Arbets ytans länk:
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409---arbets ytans länk eller ta bort länk i processen.
  -  400--det gick inte att hitta klustret, det kluster du angav finns inte eller har tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den.

  Ta bort arbets yta:
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409---arbets ytans länk eller ta bort länk i processen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Log Analytics dedikerad kluster fakturering](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Lär dig om [rätt design av Log Analytics-arbetsytor](../platform/design-logs-deployment.md)
