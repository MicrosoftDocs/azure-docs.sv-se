---
title: Kundhanterad nyckel i Azure Monitor
description: Information och steg för att konfigurera kundhanteringsnyckel för att kryptera data i Dina Log Analytics-arbetsytor med hjälp av Azure Key Vault nyckel.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 4033421095ead47e2bd1e97c4f2f42672644d7df
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364863"
---
# <a name="azure-monitor-customer-managed-key"></a>Kundhanterad nyckel i Azure Monitor 

Data i Azure Monitor krypteras med Microsoft-hanterade nycklar. Du kan använda din egen krypteringsnyckel för att skydda data och sparade frågor på dina arbetsytor. När du anger en kund hanterad nyckel, den används för att skydda och styra åtkomsten till dina data och när den har konfigurerats krypteras alla data som skickas till dina arbetsytor med din Azure Key Vault nyckel. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomstkontroller.

Vi rekommenderar att du granskar [Begränsningar och begränsningar](#limitationsandconstraints) nedan före konfigurationen.

## <a name="customer-managed-key-overview"></a>Översikt över kund hanterad nyckel

[Kryptering i vila](../../security/fundamentals/encryption-atrest.md) är ett vanligt sekretess- och säkerhetskrav i organisationer. Du kan låta Azure hantera kryptering i vila helt och hållet, medan du har olika alternativ för att noggrant hantera krypterings- och krypteringsnycklar.

Azure Monitor ser till att alla data och sparade frågor krypteras i vila med microsoft-hanterade nycklar (MMK). Azure Monitor också ett alternativ för kryptering med hjälp av din egen nyckel som lagras i [din Azure Key Vault](../../key-vault/general/overview.md), vilket ger dig kontroll att återkalla åtkomsten till dina data när som helst. Azure Monitor användningen av kryptering är identisk med det [sätt Azure Storage kryptering](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) fungerar.

Kund hanterad nyckel levereras i [dedikerade kluster som](./logs-dedicated-clusters.md) ger högre skyddsnivå och kontroll. Data som matas in till dedikerade kluster krypteras två gånger – en gång på tjänstnivå med Microsoft-hanterade nycklar eller kund hanterade nycklar, och en gång på infrastrukturnivå med två olika krypteringsalgoritmer och två olika nycklar. [Dubbel kryptering](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) skyddar mot ett scenario där en av krypteringsalgoritmerna eller nycklarna kan komprometteras. I det här fallet fortsätter det ytterligare krypteringslagret att skydda dina data. Med dedikerat kluster kan du även skydda dina data med [Lockbox-kontroll.](#customer-lockbox-preview)

Data som har matats in under de senaste 14 dagarna lagras också i cacheminnet (SSD-backad) för effektiv frågemotoråtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett kund hanterad nyckelkonfiguration, men din kontroll över SSD-data följer på [återkallning av nycklar.](#key-revocation) Vi arbetar med att kryptera SSD-data med kund hanterad nyckel under den första halvan av 2021.

Dedikerade Log Analytics-kluster använder en prismodell [för kapacitetsreservation](./logs-dedicated-clusters.md#cluster-pricing-model) som börjar på 1 000 GB/dag.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Så här fungerar kund hanterad nyckel i Azure Monitor

Azure Monitor använder hanterad identitet för att bevilja åtkomst till Azure Key Vault. Identiteten för Log Analytics-klustret stöds på klusternivå. För att tillåta kund-hanterat nyckelskydd på  flera arbetsytor fungerar en ny Log Analytics-klusterresurs som en mellanliggande identitetsanslutning mellan din Key Vault och dina Log Analytics-arbetsytor. Klustrets lagring använder den hanterade identitet som är associerad med klusterresursen för att autentisera till \' din Azure Key Vault via Azure Active Directory.  

Efter konfigurationen av den kund hanterade nyckeln krypteras nya indata till arbetsytor som är länkade till ditt dedikerade kluster med din nyckel. Du kan när som helst ta bort länken till arbetsytor från klustret. Nya data matas sedan in i Log Analytics-lagringen och krypteras med Microsoft-nyckeln, medan du kan köra sömlösa frågor mot dina nya och gamla data.

> [!IMPORTANT]
> Funktionen kund-hanterad nyckel är regional. Dina Azure Key Vault, kluster och länkade Log Analytics-arbetsytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

![Översikt över kund hanterad nyckel](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log *Analytics-klusterresurs* med hanterad identitet med behörighet Key Vault – Identiteten sprids till den dedikerade Log Analytics-klusterlagringen under lager
3. Dedikerat Log Analytics-kluster
4. Arbetsytor som är länkade till *klusterresursen* 

### <a name="encryption-keys-operation"></a>Åtgärden Krypteringsnycklar

Det finns tre typer av nycklar som ingår i Kryptering av lagringsdata:

- **KEK** – nyckelkrypteringsnyckel (din kund hanterade nyckel)
- **AEK –** kontokrypteringsnyckel
- **DEK –** Datakrypteringsnyckel

Följande regler gäller:

- Log Analytics-klusterlagringskontona genererar en unik krypteringsnyckel för varje lagringskonto, vilket kallas för AEK.
- AEK används för att härleda DEK:er, som är de nycklar som används för att kryptera varje block med data som skrivs till disk.
- När du konfigurerar din nyckel i Key Vault och refererar till den i klustret skickar Azure Storage begäranden till din Azure Key Vault för att omsluta och packa upp AEK för att utföra datakrypterings- och dekrypteringsåtgärder.
- Din kek lämnar aldrig din Key Vault.
- Azure Storage använder den hanterade identitet som är associerad med *klusterresursen* för att autentisera och få åtkomst Azure Key Vault via Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed viktiga etableringssteg

1. Skapa Azure Key Vault och lagra nyckel
1. Skapa kluster
1. Bevilja behörigheter till Key Vault
1. Uppdatera kluster med information om nyckelidentifierare
1. Länka Log Analytics-arbetsytor

Konfiguration av kund hanterad nyckel stöds inte i Azure Portal och etablering kan utföras via [PowerShell,](/powershell/module/az.operationalinsights/) [CLI eller](/cli/azure/monitor/log-analytics) [REST-begäranden.](/rest/api/loganalytics/)

### <a name="asynchronous-operations-and-status-check"></a>Asynkrona åtgärder och statuskontroll

Vissa konfigurationssteg körs asynkront eftersom de inte kan slutföras snabbt. Svaret kan vara något av `status` följande: "InProgress", "Uppdatera", "Ta bort", "Lyckades" eller "Misslyckades" med felkod.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ej tillämpligt

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ej tillämpligt

# <a name="rest"></a>[REST](#tab/rest)

När du använder REST returnerar svaret inledningsvis http-statuskoden 202 (accepterad) och huvudet med *egenskapen Azure-AsyncOperation:*
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Du kan kontrollera statusen för den asynkrona åtgärden genom att skicka en GET-begäran till slutpunkten i *Azure-AsyncOperation-huvudet:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Lagra krypteringsnyckel (KEK)

Skapa eller använd en Azure Key Vault som du redan måste generera eller importera en nyckel som ska användas för datakryptering. Den Azure Key Vault måste konfigureras som återställningsbar för att skydda din nyckel och åtkomsten till dina data i Azure Monitor. Du kan kontrollera den här konfigurationen under egenskaper i Key Vault bör både *mjuk borttagning* och rensning *vara* aktiverat.

![Inställningar för mjuk borttagning och rensningsskydd](media/customer-managed-keys/soft-purge-protection.png)

De här inställningarna kan uppdateras i Key Vault via CLI och PowerShell:

- [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
- [Rensningsskydd skyddar mot](../../key-vault/general/soft-delete-overview.md#purge-protection) framtuffad borttagning av hemligheten/valvet även efter mjuk borttagning

## <a name="create-cluster"></a>Skapa kluster

Kluster stöder två [hanterade](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)identitetstyper: System-tilldelade och Användar-tilldelade, medan en enda identitet kan definieras i ett kluster beroende på ditt scenario. 
- Systemtilldelade hanterade identiteter är enklare och genereras automatiskt när klustret skapas när identiteten är `type` inställd på "*SystemAssigned*". Den här identiteten kan användas senare för att ge lagringsåtkomst till Key Vault för omslutning och packa upp. 
  
  Identitetsinställningar i kluster för system tilldelad hanterad identitet
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Om du vill konfigurera kundhanterad nyckel när klustret skapas bör du ha en nyckel och användartilldelade identitet beviljad i din Key Vault i förväg. Skapa sedan klustret med följande inställningar: identitet som `type` "*UserAssigned*" med resurs-ID:t för din `UserAssignedIdentities` identitet. 

  Identitetsinställningar i kluster för användar tilldelad hanterad identitet
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Du kan inte använda användar tilldelad hanterad identitet om din Key Vault finns i Private-Link (vNet). Du kan använda system tilldelad hanterad identitet i det här scenariot.

Följ proceduren som illustreras i [artikeln Dedikerade kluster.](./logs-dedicated-clusters.md#creating-a-cluster) 

## <a name="grant-key-vault-permissions"></a>Bevilja Key Vault behörigheter

Skapa åtkomstprincip i Key Vault att bevilja behörigheter till klustret. Dessa behörigheter används av den Azure Monitor lagringen. Öppna ditt Key Vault i Azure Portal klicka på *"Åtkomstprinciper"* och sedan på "+ Lägg till *åtkomstprincip"* för att skapa en princip med dessa inställningar:

- Nyckelbehörigheter: *välj Hämta,* *Omslut nyckel och* Packa upp *nyckel.*
- Välj huvudnamn: Beroende på vilken identitetstyp som används i klustret (system- eller användar tilldelad hanterad identitet) anger du antingen klusternamnet eller klustrets huvudnamn-ID för den system tilldelade hanterade identiteten eller namnet på den användar tilldelade hanterade identiteten.

![bevilja Key Vault behörigheter](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

*Behörigheten* Hämta krävs för att verifiera att Key Vault har konfigurerats som återställningsbar för att skydda din nyckel och åtkomsten till dina Azure Monitor data.

## <a name="update-cluster-with-key-identifier-details"></a>Uppdatera kluster med information om nyckelidentifierare

Alla åtgärder i klustret kräver `Microsoft.OperationalInsights/clusters/write` åtgärdsbehörighet. Den här behörigheten kan beviljas via den ägare eller deltagare som `*/write` innehåller åtgärden eller via rollen Log Analytics-deltagare som innehåller `Microsoft.OperationalInsights/*` åtgärden.

Det här steget Azure Monitor Storage med den nyckel och version som ska användas för datakryptering. När den uppdateras används den nya nyckeln för att omsluta och packa upp lagringsnyckeln (AEK).

Välj den aktuella versionen av din nyckel i Azure Key Vault för att hämta information om nyckelidentifieraren.

![Bevilja Key Vault behörigheter](media/customer-managed-keys/key-identifier-8bit.png)

Uppdatera KeyVaultProperties i klustret med information om nyckelidentifierare.

>[!NOTE]
>Nyckelrotation har stöd för två lägen: automatisk rotation eller uppdatering av explicit nyckelversion, se [Nyckelrotation](#key-rotation) för att fastställa den bästa metoden för dig.

Åtgärden är asynkron och kan ta en stund att slutföra.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Response**

Det tar några minuter att sprida nyckeln. Du kan kontrollera uppdateringstillståndet på två sätt:
1. Kopiera Azure-AsyncOperation URL-värdet från svaret och följ [statuskontrollen för asynkrona åtgärder.](#asynchronous-operations-and-status-check)
2. Skicka en GET-begäran i klustret och titta på *egenskaperna KeyVaultProperties.* Din nyligen uppdaterade nyckel bör returnera i svaret.

Ett svar på GET-begäran bör se ut så här när nyckeluppdateringen är klar: 202 (godkänt) och sidhuvud
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Länka arbetsyta till kluster

> [!IMPORTANT]
> Det här steget bör endast utföras när Log Analytics-klusteretablering har slutförts. Om du länkar arbetsytor och matar in data före etableringen kommer indata att tas bort och kan inte återställas.

Du måste ha skrivbehörighet till både din arbetsyta och ditt kluster för att utföra den här åtgärden, inklusive `Microsoft.OperationalInsights/workspaces/write` och `Microsoft.OperationalInsights/clusters/write` .

Följ proceduren som illustreras i [artikeln Dedikerade kluster.](./logs-dedicated-clusters.md#link-a-workspace-to-cluster)

## <a name="key-revocation"></a>Nyckelåterkallning

> [!IMPORTANT]
> - Det rekommenderade sättet att återkalla åtkomsten till dina data är att inaktivera din nyckel eller ta bort åtkomstprincipen i Key Vault.
> - Om du anger klustrets till "Ingen" återkallas även åtkomsten till dina data, men den här metoden rekommenderas inte eftersom du inte kan återställa återkallningen när du ombeter i klustret utan att öppna `identity` `type` `identity` en supportbegäran.

Klusterlagringen respekterar alltid ändringar i nyckelbehörigheter inom en timme eller tidigare och lagringen blir otillgänglig. Alla nya data som matas in till arbetsytor som är länkade till klustret tas bort och kan inte återställas, data blir otillgängliga och frågor på dessa arbetsytor misslyckas. Data som matats in tidigare finns kvar i lagringen så länge klustret och dina arbetsytor inte tas bort. Otillgängliga data styrs av principen för databevarande och tas bort när kvarhållningen uppnås. Indata som matats in under de senaste 14 dagarna lagras också i hot-cache (SSD-backad) för effektiv frågemotordrift. Detta tas bort vid återkallning av nycklar och blir otillgängligt.

Klustrets lagring kontrollerar regelbundet dina Key Vault försöker packa upp krypteringsnyckeln och när den har åtkomst återupptas datainmatningen och frågan inom 30 minuter.

## <a name="key-rotation"></a>Nyckelrotation

Nyckelrotation har två lägen: 
- Automatisk rotation – när du uppdaterar klustret med men utelämnar egenskapen eller anger den till använder lagringen automatiskt ```"keyVaultProperties"``` de ```"keyVersion"``` senaste ```""``` versionerna.
- Explicit uppdatering av nyckelversion – när du uppdaterar klustret och anger nyckelversion i egenskapen kräver alla nya nyckelversioner en explicit uppdatering i klustret, se Uppdatera kluster med information ```"keyVersion"``` ```"keyVaultProperties"``` om [nyckelidentifierare.](#update-cluster-with-key-identifier-details) Om du genererar en ny nyckelversion i Key Vault men inte uppdaterar den i klustret fortsätter Log Analytics-klusterlagringen att använda din tidigare nyckel. Om du inaktiverar eller tar bort den gamla nyckeln innan du uppdaterar den nya nyckeln i klustret, kommer du att få ett [återkallelsetillstånd för](#key-revocation) nyckeln.

Alla dina data förblir tillgängliga efter nyckelrotationsåtgärden, eftersom data alltid krypteras med kontokrypteringsnyckeln (AEK) medan AEK nu krypteras med din nya nyckelkrypteringsnyckelversion (KEK) i Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Kund hanterad nyckel för sparade frågor

Frågespråket som används i Log Analytics är uttrycksfullt och kan innehålla känslig information i kommentarer som du lägger till i frågor eller i frågesyntaxen. Vissa organisationer kräver att sådan information skyddas under principen för kund hanterad nyckel och du behöver spara dina frågor krypterade med din nyckel. Azure Monitor kan du lagra sparade  sökningar och  loggaviseringsfrågor krypterade med din nyckel i ditt eget lagringskonto när du är ansluten till arbetsytan. 

> [!NOTE]
> Log Analytics-frågor kan sparas i olika butiker beroende på vilket scenario som används. Frågor förblir krypterade med Microsoft-nyckel (MMK) i följande scenarier oavsett konfiguration av kundhanteringsnyckel: Arbetsböcker i Azure Monitor, Azure-instrumentpaneler, Azure Logic App, Azure Notebooks och Automation-runbooks.

När du tar med din egen lagring (BYOS) och  länkar den till din arbetsyta laddar tjänsten upp sparade sökningar och *loggaviseringar till* ditt lagringskonto. Det innebär att du styr lagringskontot och kryptering i vila-principen antingen med samma nyckel som du använder för att kryptera data i Log [Analytics-klustret](../../storage/common/customer-managed-keys-overview.md) eller en annan nyckel. Du kommer dock att ansvara för de kostnader som är kopplade till det lagringskontot. 

**Att tänka på innan du ställer in kund hanterad nyckel för frågor**
* Du måste ha skrivbehörighet till både din arbetsyta och ditt lagringskonto
* Se till att skapa ditt lagringskonto i samma region som din Log Analytics-arbetsyta finns
* De *sparade sökningarna* i lagringen betraktas som tjänstartefakter och deras format kan ändras
* Befintliga *sparade sökningar* tas bort från arbetsytan. Kopiera och spara *sökningar* som du behöver före konfigurationen. Du kan visa dina *sparade sökningar med* Hjälp av  [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* Frågehistorik stöds inte och du kommer inte att kunna se frågor som du har kört
* Du kan länka ett enda lagringskonto till arbetsytan för att spara  frågor, men det kan användas både för sparade sökningar och *loggaviseringsfrågor*
* Fäst på instrumentpanelen stöds inte

**Konfigurera BYOS för sparade sökningar-frågor**

Länka ett lagringskonto *för* Fråga till din *arbetsyta* – sparade sökningar sparas i ditt lagringskonto. 

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Efter konfigurationen sparas alla *nya sparade sökfrågor* i lagringen.

**Konfigurera BYOS för loggaviseringsfrågor**

Länka ett lagringskonto *för aviseringar* till din arbetsyta – *loggaviseringsfrågor* sparas i ditt lagringskonto. 

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Ej tillämpligt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Efter konfigurationen sparas alla nya aviseringsfrågor i lagringen.

## <a name="customer-lockbox-preview"></a>Customer Lockbox (förhandsversion)

Lockbox ger dig kontroll över att godkänna eller avvisa Microsoft-teknikers begäran om åtkomst till dina data under en supportbegäran.

I Azure Monitor har du den här kontrollen över data i arbetsytor som är länkade till ditt dedikerade Log Analytics-kluster. Lockbox-kontrollen gäller för data som lagras i ett dedikerat Log Analytics-kluster där de hålls isolerade i klustrets lagringskonton under din Lockbox-skyddade prenumeration.  

Läs mer om [Customer Lockbox för Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed nyckelåtgärder

Customer-Managed nyckel tillhandahålls i dedikerat kluster och dessa åtgärder refereras i [artikeln dedikerade kluster](./logs-dedicated-clusters.md#change-cluster-properties)

- Hämta alla kluster i resursgruppen  
- Hämta alla kluster i prenumerationen
- Uppdatera *kapacitetsreservation* i kluster
- Uppdatera *billingType* i klustret
- Ta bort länk till en arbetsyta från kluster
- Ta bort klustret

## <a name="limitations-and-constraints"></a>Begränsningar och restriktioner

- Det maximala antalet kluster per region och prenumeration är 2

- Det maximala antalet arbetsytor som kan länkas till ett kluster är 1 000

- Du kan länka en arbetsyta till klustret och sedan ta bort länken till den. Antalet länkåtgärder för arbetsytan på en viss arbetsyta är begränsat till 2 under en period på 30 dagar.

- Kund hanterad nyckelkryptering gäller för nyligen inadelade data efter konfigurationstiden. Data som matades in före konfigurationen förblir krypterade med Microsoft-nyckeln. Du kan fråga data som matats in före och efter konfigurationen av kund hanterad nyckel sömlöst.

- Den Azure Key Vault måste konfigureras som återställningsbar. Dessa egenskaper är inte aktiverade som standard och bör konfigureras med HJÄLP av CLI eller PowerShell:<br>
  - [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
  - [Rensningsskydd ska](../../key-vault/general/soft-delete-overview.md#purge-protection) aktiveras för att skydda mot framtrngsborttagning av hemligheten/valvet även efter mjuk borttagning.

- Klusterflyttning till en annan resursgrupp eller prenumeration stöds inte för närvarande.

- Dina Azure Key Vault, kluster och arbetsytor måste finnas i samma region och i samma Azure Active Directory-klientorganisation (Azure AD), men de kan finnas i olika prenumerationer.

- Lockbox är inte tillgängligt i Kina för närvarande. 

- [Dubbel kryptering](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) konfigureras automatiskt för kluster som skapats från oktober 2020 i regioner som stöds. Du kan kontrollera om klustret har konfigurerats för dubbel kryptering genom att skicka en GET-begäran i klustret och observera att värdet är för kluster med `isDoubleEncryptionEnabled` `true` Dubbel kryptering aktiverat. 
  - Om du skapar ett kluster och får felet "<regionnamn> stöder inte dubbel kryptering för kluster", kan du fortfarande skapa klustret utan dubbel kryptering genom att lägga till i `"properties": {"isDoubleEncryptionEnabled": false}` REST-begärandetexten.
  - Inställningen dubbel kryptering kan inte ändras när klustret har skapats.

  - Om klustret har angetts med användar tilldelad hanterad identitet inaktiverar inställningen med klustret och förhindrar åtkomst till dina data, men du kan inte återställa återkallningen och aktivera klustret utan att öppna `UserAssignedIdentities` `None` supportbegäran. Den här begränsningen gäller system tilldelad hanterad identitet.

  - Du kan inte använda kund hanterad nyckel med användartilldejterade hanterade identiteter om din Key Vault finns i Private-Link (vNet). Du kan använda system tilldelad hanterad identitet i det här scenariot.

## <a name="troubleshooting"></a>Felsökning

- Beteende med Key Vault tillgänglighet
  - I normal drift – Lagring cachelagrar AEK under korta tidsperioder och går tillbaka till Key Vault att packa upp regelbundet.
    
  - Tillfälliga anslutningsfel – Lagring hanterar tillfälliga fel (tidsgränser, anslutningsfel, DNS-problem) genom att tillåta nycklar att stanna kvar i cacheminnet en kort stund längre och detta övervinner eventuella tillgänglighetsproblem. Fråge- och inmatningsfunktionerna fortsätter utan avbrott.
    
  - Live-webbplats – otillgänglighet på cirka 30 minuter gör att lagringskontot blir otillgängligt. Frågekapaciteten är inte tillgänglig och indata cachelagras i flera timmar med hjälp av Microsoft-nyckeln för att undvika dataförlust. När åtkomsten Key Vault återställs blir frågan tillgänglig och de tillfälliga cachelagrade data matas in i datalagret och krypteras med kund hanterad nyckel.

  - Key Vault åtkomstfrekvens – Den frekvens som Azure Monitor Storage Key Vault för omslutnings- och packa upp-åtgärder är mellan 6 och 60 sekunder.

- Om du uppdaterar klustret när klustret är i etablerings- eller uppdateringstillstånd misslyckas uppdateringen.

- Om du får ett konfliktfel när du skapar ett kluster – Det kan vara så att du har tagit bort klustret under de senaste 14 dagarna och att det är i en period för mjuk borttagning. Klusternamnet förblir reserverat under mjuk borttagningsperioden och du kan inte skapa ett nytt kluster med det namnet. Namnet släpps efter mjuk borttagningsperioden när klustret tas bort permanent.

- Arbetsytans länk till klustret misslyckas om den är länkad till ett annat kluster.

- Om du skapar ett kluster och anger KeyVaultProperties direkt kan åtgärden misslyckas eftersom åtkomstprincipen inte kan definieras förrän systemidentiteten har tilldelats klustret.

- Om du uppdaterar ett befintligt kluster med KeyVaultProperties och "Hämta" nyckelåtkomstprincip saknas i Key Vault, misslyckas åtgärden.

- Om du inte kan distribuera klustret kontrollerar du att Azure Key Vault, klustret och länkade Log Analytics-arbetsytor finns i samma region. kan finnas i olika prenumerationer.

- Om du uppdaterar din nyckelversion i Key Vault och inte uppdaterar den nya nyckelidentifierarinformationen i klustret fortsätter Log Analytics-klustret att använda din tidigare nyckel och dina data blir otillgängliga. Uppdatera information om ny nyckelidentifierare i klustret för att återuppta datainmatningen och möjligheten att köra frågor mot data.

- Vissa åtgärder är långa och kan ta en stund att slutföra – det här är kluster skapa, uppdatera klusternyckel och ta bort kluster. Du kan kontrollera åtgärdsstatusen på två sätt:
  1. När du använder REST kopierar du Azure-AsyncOperation URL-värdet från svaret och följer [statuskontrollen för asynkrona åtgärder.](#asynchronous-operations-and-status-check)
  2. Skicka GET-begäran till klustret eller arbetsytan och observera svaret. Till exempel har inte den olänkade arbetsytan *klustretResourceId* under *funktioner*.

- Felmeddelanden
  
  **Skapa kluster**
  -  400 – Klusternamnet är inte giltigt. Klusternamnet kan innehålla tecknen a–z, A–Z, 0–9 och längden 3–63.
  -  400 – Brödtexten i begäran är null eller i felaktigt format.
  -  400 – SKU-namnet är ogiltigt. Ange SKU-namn till capacityReservation.
  -  400 – Kapaciteten har angetts men SKU:n är inte capacityReservation. Ange SKU-namn till capacityReservation.
  -  400 – Kapacitet saknas i SKU. Ange kapacitetsvärdet till 1 000 eller högre i steg om 100 (GB).
  -  400 – Kapaciteten i SKU är inte inom räckhåll. Bör vara minst 1 000 och upp till den högsta tillåtna kapaciteten som är tillgänglig under "Användning och beräknad kostnad" på din arbetsyta.
  -  400 – Kapaciteten är låst i 30 dagar. Att minska kapaciteten tillåts 30 dagar efter uppdateringen.
  -  400 – Ingen SKU har angetts. Ange SKU-namnet till capacityReservation och Capacity till 1 000 eller högre i steg om 100 (GB).
  -  400 – Identiteten är null eller tom. Ange Identity med typen systemAssigned.
  -  400 – KeyVaultProperties ställs in när de skapas. Uppdatera KeyVaultProperties när klustret har skapats.
  -  400 – Åtgärden kan inte köras nu. Asynkron åtgärd är i ett annat tillstånd än lyckades. Klustret måste slutföra åtgärden innan någon uppdateringsåtgärd utförs.

  **Klusteruppdatering**
  -  400 – Klustret är i borttagningstillstånd. Asynkron åtgärd pågår. Klustret måste slutföra åtgärden innan någon uppdateringsåtgärd utförs.
  -  400 – KeyVaultProperties är inte tomt men har ett felaktigt format. Se [uppdatera nyckelidentifierare.](#update-cluster-with-key-identifier-details)
  -  400 – Det gick inte att verifiera nyckeln i Key Vault. Kan bero på brist på behörigheter eller när nyckeln inte finns. Kontrollera att du [anger nyckel och åtkomstprincip](#grant-key-vault-permissions) i Key Vault.
  -  400 – Nyckeln kan inte återställas. Key Vault måste vara inställt på Mjuk borttagning och Rensningsskydd. Läs [Key Vault dokumentation](../../key-vault/general/soft-delete-overview.md)
  -  400 – Åtgärden kan inte köras nu. Vänta tills Async-åtgärden har slutförts och försök igen.
  -  400 – Klustret är i borttagningstillstånd. Vänta tills Async-åtgärden har slutförts och försök igen.

  **Hämta kluster**
    -  404 – Klustret hittades inte, klustret kan ha tagits bort. Om du försöker skapa ett kluster med det namnet och får en konflikt är klustret i mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den eller använda ett annat namn för att skapa ett nytt kluster. 

  **Ta bort kluster**
    -  409 – Det går inte att ta bort ett kluster när det är i etableringstillstånd. Vänta tills Async-åtgärden har slutförts och försök igen.

  **Länk till arbetsyta**
  -  404 – Arbetsytan hittades inte. Arbetsytan som du angav finns inte eller har inte tagits bort.
  -  409 – Arbetsytelänk eller avlänkning pågår.
  -  400 – Klustret hittades inte, det kluster som du angav finns inte eller har inte tagits bort. Om du försöker skapa ett kluster med det namnet och får en konflikt är klustret i mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den.

  **Ta bort länk till arbetsyta**
  -  404 – Arbetsytan hittades inte. Arbetsytan som du angav finns inte eller har inte tagits bort.
  -  409 – Arbetsytelänk eller avlänkning pågår.
## <a name="next-steps"></a>Nästa steg

- Läs mer om [fakturering av dedikerade Log Analytics-kluster](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Lär dig [mer om korrekt design av Log Analytics-arbetsytor](./design-logs-deployment.md)