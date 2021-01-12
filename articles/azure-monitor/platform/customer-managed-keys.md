---
title: Kundhanterad nyckel i Azure Monitor
description: Information och steg för att konfigurera kundhanterad nyckel för att kryptera data i dina Log Analytics arbets ytor med hjälp av en Azure Key Vault nyckel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 6c1f323828eb48b61b38370bc2fe56d4c93bf036
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127217"
---
# <a name="azure-monitor-customer-managed-key"></a>Kundhanterad nyckel i Azure Monitor 

Data i Azure Monitor krypteras med Microsoft-hanterade nycklar. Du kan använda din egen krypterings nyckel för att skydda data och sparade frågor i dina arbets ytor. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrol lera åtkomsten till dina data och när de har kon figurer ATS krypteras alla data som skickas till dina arbets ytor med din Azure Key Vault nyckel. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomstkontroller.

Vi rekommenderar att du granskar [begränsningar och](#limitationsandconstraints) begränsningar nedan före konfigurationen.

## <a name="customer-managed-key-overview"></a>Översikt över kundhanterad nyckel

[Kryptering i vila](../../security/fundamentals/encryption-atrest.md) är ett gemensamt sekretess-och säkerhets krav i organisationer. Du kan låta Azure helt hantera kryptering i vila, medan du har olika alternativ för att hantera krypterings-och krypterings nycklar.

Azure Monitor säkerställer att alla data och sparade frågor krypteras i vila med hjälp av Microsoft-hanterade nycklar (MMK). Azure Monitor innehåller också ett alternativ för kryptering med hjälp av din egen nyckel som lagras i [Azure Key Vault](../../key-vault/general/overview.md), vilket ger dig kontrollen att återkalla åtkomsten till dina data när som helst. Azure Monitor krypterings användningen är identisk med hur [Azure Storage kryptering](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) fungerar.

Kundhanterad nyckel levereras på [dedikerade kluster](../log-query/logs-dedicated-clusters.md) som ger högre skydds nivå och kontroll. Data som matas in på dedikerade kluster krypteras två gånger – en gång på tjänst nivå med hjälp av Microsoft-hanterade nycklar eller Kundhanterade nycklar, och en gång på infrastruktur nivån med två olika krypteringsalgoritmer och två olika nycklar. [Dubbel kryptering](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) skyddar mot ett scenario där en av krypteringsalgoritmer eller nycklar kan komprometteras. I det här fallet fortsätter det extra krypterings lagret att skydda dina data. Med dedikerat kluster kan du också skydda dina data med [Lås](#customer-lockbox-preview) kontroll.

Data som matats in under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett kundhanterad nyckel konfiguration, men kontrollen över SSD-data följer [nyckel återkallning](#key-revocation). Vi arbetar med att ha SSD-data krypterade med kundhanterad nyckel under första hälften av 2021.

Log Analytics dedikerade kluster använder en kapacitets reservation [pris modell](../log-query/logs-dedicated-clusters.md#cluster-pricing-model) som börjar med 1000 GB/dag.

> [!IMPORTANT]
> På grund av tillfälliga kapacitets begränsningar kräver vi att du förregistrerar dig innan du skapar ett kluster. Använd dina kontakter i Microsoft eller öppna support förfrågan för att registrera dina prenumerations-ID: n.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Hur kundhanterad nyckel fungerar i Azure Monitor

Azure Monitor använder hanterad identitet för att bevilja åtkomst till din Azure Key Vault. Identiteten för Log Analytics-klustret stöds på kluster nivå. För att tillåta kundhanterat nyckel skydd på flera arbets ytor, fungerar en ny Log Analytics *kluster* resurs som mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor. Klustrets lagrings utrymme använder den hanterade identitet som \' är associerad med *kluster* resursen för att autentisera till din Azure Key Vault via Azure Active Directory. 

Efter den Kundhanterade nyckel konfigurationen krypteras nya inmatade data till arbets ytor som är länkade till ditt dedikerade kluster med din nyckel. Du kan när som helst avlänka arbets ytor från klustret. Nya data hämtas sedan till Log Analytics lagring och krypteras med Microsoft-nyckel, medan du kan fråga dina nya och gamla data sömlöst.

> [!IMPORTANT]
> Kund-hanterad nyckel funktion är regional. Dina Azure Key Vault-, kluster-och länkade Log Analytics-arbetsytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

![Översikt över kundhanterad nyckel](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics *kluster* resurs som har hanterad identitet med behörighet att Key Vault--identiteten sprids till Underlay dedikerade Log Analytics kluster lagring
3. Dedikerat Log Analytics kluster
4. Arbets ytor som är länkade till *kluster* resursen 

### <a name="encryption-keys-operation"></a>Åtgärd för krypterings nycklar

Det finns tre typer av nycklar som ingår i kryptering av lagrings data:

- Nyckel krypterings nyckel för **KEK** (din Kundhanterade nyckel)
- Krypterings nyckel för **AEK** -konto
- **DEK** – data krypterings nyckel

Följande regler gäller:

- Log Analytics kluster lagrings konton genererar unik krypterings nyckel för varje lagrings konto, vilket kallas för AEK.
- AEK används för att härleda DEKs, vilket är de nycklar som används för att kryptera varje data block som skrivs till disk.
- När du konfigurerar din nyckel i Key Vault och refererar till den i klustret, Azure Storage skickar begär anden till din Azure Key Vault att omsluta och packa upp AEK för att utföra data kryptering och dekrypterings åtgärder.
- Din KEK lämnar aldrig Key Vault och om det finns en HSM-nyckel lämnar den aldrig maskin varan.
- Azure Storage använder den hanterade identitet som är kopplad till *kluster* resursen för att autentisera och komma åt Azure Key Vault via Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Steg för Customer-Managed nyckel etablering

1. Registrera din prenumeration så att klustret kan skapas
1. Skapa Azure Key Vault och lagra nyckel
1. Kluster skapas
1. Bevilja behörighet till din Key Vault
1. Uppdaterar kluster med information om nyckel identifierare
1. Länkar Log Analytics arbets ytor

Kundhanterad nyckel konfiguration stöds inte i Azure Portal för närvarande och etableringen kan utföras via [PowerShell](/powershell/module/az.operationalinsights/)-, [CLI](/cli/azure/monitor/log-analytics) -eller [rest](/rest/api/loganalytics/) -begäranden.

### <a name="asynchronous-operations-and-status-check"></a>Asynkrona åtgärder och status kontroll

Vissa konfigurations steg körs asynkront eftersom de inte kan slutföras snabbt. `status`Som svar kan vara något av följande: "pågår", "uppdatering", "ta bort", "lyckades eller" misslyckades "med felkoden.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Saknas

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Saknas

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Saknas

# <a name="rest"></a>[REST](#tab/rest)

När du använder REST returnerar svaret ursprungligen en HTTP-statuskod 200 (OK) och rubriken med *Azure-AsyncOperation-* egenskapen när den godkänns:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Du kan kontrol lera statusen för den asynkrona åtgärden genom att skicka en GET-begäran till slut punkten i *Azure-AsyncOperation-* huvudet:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

### <a name="allowing-subscription"></a>Tillåter prenumeration

Använd dina kontakter i Microsoft eller öppna support förfrågan i Log Analytics för att tillhandahålla dina prenumerations-ID: n.

## <a name="storing-encryption-key-kek"></a>Lagra krypterings nyckel (KEK)

Skapa eller Använd en Azure Key Vault som du redan har för att skapa eller importera en nyckel som ska användas för data kryptering. Azure Key Vault måste konfigureras som rekonstruerbar för att skydda din nyckel och åtkomsten till dina data i Azure Monitor. Du kan kontrol lera den här konfigurationen under egenskaper i Key Vault, både *mjuk borttagnings* -och *rensnings skydd* ska vara aktiverat.

![Mjuk borttagning och rensning av skydds inställningar](media/customer-managed-keys/soft-purge-protection.png)

De här inställningarna kan uppdateras i Key Vault via CLI och PowerShell:

- [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
- [Rensa skydds skydd](../../key-vault/general/soft-delete-overview.md#purge-protection) mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

## <a name="create-cluster"></a>Skapa kluster

> [!NOTE]
> Kluster har stöd för två [hanterade identitets typer](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types): systemtilldelade och tilldelade användare, och var och en kan baseras beroende på ditt scenario. Systemtilldelad hanterad identitet är enklare och skapas automatiskt med klustret när identiteten `type` anges som "*SystemAssigned*". den här identiteten kan användas senare för att ge klustret åtkomst till din Key Vault. Om du vill skapa ett kluster medan en kundhanterad nyckel definieras vid skapande av klustret, bör du ha en definierad nyckel och tilldelad identitet som beviljats i Key Vault i förväg och sedan skapa klustret med följande inställningar: identitet `type` som "*UserAssigned*", `UserAssignedIdentities` med identitetens resurs-ID och `keyVaultProperties` med viktig information.

> [!IMPORTANT]
> För närvarande kan du inte definiera kundhanterad nyckel med användardefinierad hanterad identitet om Key Vault finns i Private-Link (vNet) och du kan använda systemtilldelad hanterad identitet i det här fallet.

Följ proceduren som illustreras i [artikeln om dedikerade kluster](../log-query/logs-dedicated-clusters.md#creating-a-cluster). 

## <a name="grant-key-vault-permissions"></a>Bevilja Key Vault behörigheter

Skapa åtkomst princip i Key Vault för att bevilja behörighet till klustret. Dessa behörigheter används av Underlay-Azure Monitor-lagringen. Öppna din Key Vault i Azure Portal och klicka på *"åtkomst principer"* och sedan *"+ Lägg till åtkomst princip"* för att skapa en princip med följande inställningar:

- Nyckel behörigheter: Välj *Get*-, *wrap-tangenten* och *unwrap Key*.
- Välj huvud konto: beroende på vilken identitets typ som används i klustret (system eller användare som tilldelats en hanterad identitet) anger du antingen kluster namn eller klustrets huvud namns-ID för systemtilldelad hanterad identitet eller användaren tilldelas hanterad identitet.

![bevilja Key Vault behörigheter](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Get-behörighet krävs för att verifiera att Key Vault har kon figurer ATS för att kunna *återskapas* för att skydda din nyckel och till gång till dina Azure Monitor data.

## <a name="update-cluster-with-key-identifier-details"></a>Uppdatera kluster med information om nyckel identifierare

Alla åtgärder i klustret måste ha `Microsoft.OperationalInsights/clusters/write` behörigheten åtgärd. Den här behörigheten kan beviljas via den ägare eller deltagare som innehåller `*/write` åtgärden eller via rollen Log Analytics Contributor som innehåller `Microsoft.OperationalInsights/*` åtgärden.

Det här steget uppdaterar Azure Monitor lagring med den nyckel och version som ska användas för data kryptering. När den uppdateras används den nya nyckeln för att omsluta och packa upp lagrings nyckeln (AEK).

Välj den aktuella versionen av nyckeln i Azure Key Vault för att hämta information om nyckel identifieraren.

![Bevilja Key Vault behörigheter](media/customer-managed-keys/key-identifier-8bit.png)

Uppdatera KeyVaultProperties i kluster med information om nyckel identifierare.

Åtgärden är asynkron och kan ta en stund att slutföra.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Saknas

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

Det tar flera minuter att slutföra spridningen av nyckeln. Du kan kontrol lera uppdaterings statusen på två sätt:
1. Kopiera Azure-AsyncOperation URL-värdet från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran i klustret och titta på egenskaperna för *KeyVaultProperties* . Den nyligen uppdaterade nyckeln bör returneras i svaret.

Ett svar på GET-begäran bör se ut så här när nyckel uppdateringen är klar: 200 OK och sidhuvud
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

## <a name="link-workspace-to-cluster"></a>Länka arbets ytan till kluster

> [!IMPORTANT]
> Det här steget bör endast utföras när Log Analytics kluster etableringen har slutförts. Om du länkar arbets ytor och matar in data före etableringen tas inmatade data bort och går inte att återskapa.

Du måste ha Skriv behörighet till både din arbets yta och ditt kluster för att kunna utföra den här åtgärden, inklusive `Microsoft.OperationalInsights/workspaces/write` och `Microsoft.OperationalInsights/clusters/write` .

Följ proceduren som illustreras i [artikeln om dedikerade kluster](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-cluster).

## <a name="key-revocation"></a>Återkallande av nyckel

Du kan återkalla åtkomsten till data genom att inaktivera nyckeln eller ta bort klustrets åtkomst princip i Key Vault. 

> [!IMPORTANT]
> - Om klustret har angetts med användardefinierad hanterad identitet ställer `UserAssignedIdentities` du in med `None` pausa klustret och förhindrar åtkomst till dina data, men du kan inte återställa åter kallelsen och aktivera klustret utan att öppna support förfrågan. Den här begränsningen gäller inte för systemtilldelad hanterad identitet.
> - Den rekommenderade åtgärden för nyckel återkallning är genom att inaktivera nyckeln i Key Vault.

Kluster lagringen kommer alltid att respektera ändringar i nyckel behörigheter inom en timme eller snart och lagring kommer att bli otillgänglig. Alla nya data som läggs till i arbets ytorna som är länkade till klustret tas bort och kan inte återställas, data blir otillgängliga och det går inte att hitta frågor på dessa arbets ytor. Tidigare inmatade data finns kvar i lagrings utrymmet så länge klustret och arbets ytorna inte har tagits bort. Otillgängliga data regleras av data bevarande principen och kommer att rensas när kvarhållning har nåtts. Inmatade data under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Detta tas bort vid nyckel återkallnings åtgärden och blir oåtkomlig även.

Klustrets lagring avsöker regelbundet din Key Vault för att försöka att packa upp krypterings nyckeln och en gång, så fortsätter data inmatningen och frågan att återupptas inom 30 minuter.

## <a name="key-rotation"></a>Nyckelrotation

För kundhanterad nyckel rotation krävs en explicit uppdatering av klustret med den nya nyckel versionen i Azure Key Vault. [Uppdatera kluster med information om nyckel identifierare](#update-cluster-with-key-identifier-details). Om du inte uppdaterar den nya nyckel versionen i klustret, fortsätter Log Analytics kluster lagring att använda din tidigare nyckel för kryptering. Om du inaktiverar eller tar bort din gamla nyckel innan du uppdaterar den nya nyckeln i klustret kommer du att få statusen för [nyckel återkallning](#key-revocation) .

Alla dina data är tillgängliga efter nyckel rotations åtgärden, eftersom data alltid krypteras med konto krypterings nyckeln (AEK) medan AEK nu krypteras med din nya KEK-version (Key Encryption Key) i Key Vault.

## <a name="customer-managed-key-for-queries"></a>Kundhanterad nyckel för frågor

Frågespråket som används i Log Analytics är lättfattliga programspecifika och kan innehålla känslig information i kommentarer som du lägger till i frågor eller i frågesyntaxen. Vissa organisationer kräver att sådan information hålls skyddad under kundhanterad nyckel princip och du måste spara dina frågor krypterade med din nyckel. Med Azure Monitor kan du lagra *sparade sökningar* och *Logga aviserings* frågor som är krypterade med din nyckel i ditt eget lagrings konto när du är ansluten till din arbets yta. 

> [!NOTE]
> Log Analytics frågor kan sparas i olika butiker beroende på vilket scenario som används. Frågorna förblir krypterade med Microsoft Key (MMK) i följande scenarier, oavsett kundhanterad nyckel konfiguration: arbets böcker i Azure Monitor, Azure-instrumentpaneler, Azure Logic app, Azure Notebooks och Automation-runbooks.

När du tar med din egen lagring (BYOS) och länkar den till din arbets yta överförs frågor till ditt lagrings konto via tjänsten för *sparade sökningar* och *logg aviseringar* . Det innebär att du styr lagrings kontot och [principen för kryptering vid vila](../../storage/common/customer-managed-keys-overview.md) antingen med samma nyckel som du använder för att kryptera data i Log Analytics kluster eller en annan nyckel. Du kommer dock att vara ansvarig för kostnaderna som är kopplade till det lagrings kontot. 

**Att tänka på innan du ställer in kundhanterad nyckel för frågor**
* Du måste ha Skriv behörighet till både din arbets yta och ditt lagrings konto
* Se till att skapa ditt lagrings konto i samma region som din Log Analytics arbets yta finns
* *Spara sökningar* i lagring anses som tjänst artefakter och deras format kan ändras
* Befintliga *sparade sökningar* tas bort från din arbets yta. Kopiera och *Spara sökningar* som du behöver före konfigurationen. Du kan visa dina *sparade sökningar* med  [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* Frågans historik stöds inte och du kommer inte att kunna se frågor som du körde
* Du kan länka ett enskilt lagrings konto till arbets ytan för att kunna spara frågor, men det kan användas för både *sparade sökningar* och *logg aviserings* frågor
* Fäst på instrument panelen stöds inte

**Konfigurera BYOS för sparade söknings frågor**

Länka ett lagrings konto för *fråga* till din arbets yta – *sparade – sökningar* frågor sparas i ditt lagrings konto. 

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Saknas

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

Efter konfigurationen sparas alla nya *sparade Sök* frågor i ditt lagrings utrymme.

**Konfigurera BYOS för logg aviserings frågor**

Länka ett lagrings konto för *aviseringar* till arbets ytan – *logg aviserings* frågor sparas i ditt lagrings konto. 

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Saknas

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

Efter konfigurationen sparas alla nya aviserings frågor i din lagrings plats.

## <a name="customer-lockbox-preview"></a>Customer Lockbox (för hands version)

Med lås får du kontrollen att godkänna eller avvisa Microsoft Engineer-begäran för att få åtkomst till dina data under en support förfrågan.

I Azure Monitor har du den här kontrollen på data i arbets ytor som är länkade till ditt Log Analytics-dedikerade kluster. Den säkra kontrollen gäller för data som lagras i ett Log Analytics dedikerat kluster där den hålls isolerad i klustrets lagrings konton under den skyddade din säkra prenumeration.  

Läs mer om [Customer lockbox för Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed nyckel åtgärder

Customer-Managed nyckel anges i ett dedikerat kluster och dessa åtgärder hänvisas till i [dedikerat kluster artikel](../log-query/logs-dedicated-clusters.md#change-cluster-properties)

- Hämta alla kluster i resurs gruppen  
- Hämta alla kluster i prenumerationen
- Uppdatera *kapacitets reservation* i kluster
- Uppdatera *billingType* i kluster
- Ta bort länken mellan en arbets yta och ett kluster
- Ta bort klustret

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

- Kundhanterad nyckel stöds på dedikerade Log Analytics kluster och lämpar sig för kunder som skickar 1 TB per dag eller mer.

- Det högsta antalet kluster per region och prenumeration är 2

- Det maximala antalet länkade arbets ytor till klustret är 1000

- Du kan länka en arbets yta till klustret och sedan ta bort länken till den. Antalet länk åtgärder på arbets ytan på en viss arbets yta är begränsat till 2 under en period på 30 dagar.

- Arbets ytans länk till kluster ska endast utföras när du har kontrollerat att Log Analytics kluster etableringen har slutförts. Data som skickas till din arbets yta innan slut för Ande kommer att tas bort och går inte att återskapa.

- Kundhanterad nyckel kryptering gäller nyligen inmatade data efter konfigurations tiden. Data som matats in före konfigurationen är fortfarande krypterade med Microsoft-nyckeln. Du kan fråga efter data som matats in före och efter den Kundhanterade nyckel konfigurationen sömlöst.

- Azure Key Vault måste konfigureras som återställnings Bart. Dessa egenskaper är inte aktiverade som standard och ska konfigureras med CLI eller PowerShell:<br>
  - [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
  - [Rensnings skyddet](../../key-vault/general/soft-delete-overview.md#purge-protection) bör vara aktiverat för att skydda mot Force borttagning av hemligheten/valvet även efter mjuk borttagning.

- Det finns för närvarande inte stöd för att flytta kluster till en annan resurs grupp eller prenumeration.

- Dina Azure Key Vault-, kluster-och länkade arbets ytor måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer.

- Arbets ytans länk till klustret Miss fungerar om den är länkad till ett annat kluster.

- Den säkra databasen är inte tillgänglig i Kina. 

- [Double Encryption](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) konfigureras automatiskt för kluster som skapats från oktober 2020 i regioner som stöds. Du kan kontrol lera om klustret har kon figurer ATS för Double Encryption med en GET-begäran på klustret och att `"isDoubleEncryptionEnabled"` det är `true` för kluster med dubbel kryptering aktiverat. 
  - Om du skapar ett kluster och får ett fel meddelande om att <region namn> inte stöder Double Encryption för kluster. "kan du fortfarande skapa klustret utan Double Encryption. Lägg till `"properties": {"isDoubleEncryptionEnabled": false}` egenskap i rest Request-texten.
  - Inställningen Double Encryption kan inte ändras efter att klustret har skapats.

  - Om klustret har angetts med användardefinierad hanterad identitet ställer `UserAssignedIdentities` du in med `None` pausa klustret och förhindrar åtkomst till dina data, men du kan inte återställa åter kallelsen och aktivera klustret utan att öppna support förfrågan. Den här begränsningen inte är ' tillämpas på systemtilldelad hanterad identitet.

  - För närvarande kan du inte definiera kundhanterad nyckel med användardefinierad hanterad identitet om Key Vault finns i Private-Link (vNet) och du kan använda systemtilldelad hanterad identitet i det här fallet.

## <a name="troubleshooting"></a>Felsökning

- Beteende med Key Vault tillgänglighet
  - I normal drift – Storage cache-AEK under korta tids perioder och går tillbaka till Key Vault för att packa med jämna mellanrum.
    
  - Tillfälliga anslutnings fel – lagring hanterar tillfälliga fel (timeout, anslutnings fel, DNS-problem) genom att tillåta att nycklar hålls kvar i cacheminnet för kort, samtidigt som den överkommer till alla små signaler i tillgänglighet. Funktionerna för fråga och inmatning fortsätter utan avbrott.
    
  - Live site – otillgänglig cirka 30 minuter kommer lagrings kontot att bli otillgängligt. Fråge funktionen är inte tillgänglig och inmatade data cachelagras i flera timmar med Microsoft-nyckeln för att undvika data förlust. När åtkomsten till Key Vault återställs blir frågan tillgänglig och temporära cachelagrade data matas in i data lagringen och krypteras med kundhanterad nyckel.

  - Key Vault åtkomst frekvens – den frekvens som Azure Monitor lagrings åtkomst Key Vault för omslutning och unwrap-åtgärder är mellan 6 och 60 sekunder.

- Om du skapar ett kluster och anger KeyVaultProperties omedelbart kan åtgärden Miss lyckas eftersom det inte går att definiera åtkomst principen förrän system identiteten tilldelas till klustret.

- Om du uppdaterar ett befintligt kluster med KeyVaultProperties och get nyckel åtkomst principen saknas i Key Vault Miss kan åtgärden utföras.

- Om du får ett konflikt fel när du skapar ett kluster, kan det bero på att du har tagit bort klustret under de senaste 14 dagarna och att det är i en mjuk borttagnings period. Kluster namnet förblir reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Namnet släpps efter den mjuka borttagnings perioden när klustret tas bort permanent.

- Om du uppdaterar klustret medan en åtgärd pågår, fungerar inte åtgärden.

- Om du inte kan distribuera klustret kontrollerar du att arbets ytorna Azure Key Vault, Cluster och Linked Log Analytics finns i samma region. Kan finnas i olika prenumerationer.

- Om du uppdaterar din nyckel version i Key Vault och inte uppdaterar informationen om den nya nyckel identifieraren i klustret, kommer Log Analytics-klustret att fortsätta använda din tidigare nyckel och dina data blir otillgängliga. Uppdatera informationen om den nya nyckel identifieraren i klustret för att återuppta data inmatning och möjlighet att fråga data.

- Vissa åtgärder är långa och kan ta en stund att slutföra – dessa är kluster skapa, kluster nyckel uppdatering och kluster borttagning. Du kan kontrol lera åtgärds statusen på två sätt:
  1. När du använder REST kopierar du Azure-AsyncOperation URL-värdet från svaret och följer [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
  2. Skicka GET-begäran till kluster eller arbets yta och observera svaret. Till exempel kan inte en länkad arbets yta ha *clusterResourceId* under *funktioner*.

- Felmeddelanden
  
  **Skapa kluster**
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

  **Kluster uppdatering**
  -  400--klustrets status tas bort. En asynkron åtgärd pågår. Klustret måste utföra åtgärden innan en uppdaterings åtgärd utförs.
  -  400--KeyVaultProperties är inte tomt men har ett felaktigt format. Se [uppdatering av nyckel identifierare](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400--det gick inte att verifiera nyckeln i Key Vault. Kan bero på otillräckliga behörigheter eller när nyckeln inte finns. Kontrol lera att du [ställer in nyckel-och åtkomst principer](../platform/customer-managed-keys.md#grant-key-vault-permissions) i Key Vault.
  -  400--det går inte att återskapa nyckeln. Key Vault måste anges som mjuk borttagning och rensnings skydd. Se [Key Vault-dokumentation](../../key-vault/general/soft-delete-overview.md)
  -  400--åtgärden kan inte utföras nu. Vänta tills den asynkrona åtgärden har slutförts och försök igen.
  -  400--klustrets status tas bort. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

  **Hämta kluster**
    -  404--det gick inte att hitta klustret, klustret kan ha tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den eller använda ett annat namn för att skapa ett nytt kluster. 

  **Ta bort kluster**
    -  409--det går inte att ta bort ett kluster i etablerings tillstånd. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

  **Arbets ytans länk**
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409---arbets ytans länk eller ta bort länk i processen.
  -  400--det gick inte att hitta klustret, det kluster du angav finns inte eller har tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den.

  **Ta bort arbets yta**
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409---arbets ytans länk eller ta bort länk i processen.
## <a name="next-steps"></a>Nästa steg

- Läs mer om [Log Analytics dedikerad kluster fakturering](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Lär dig om [rätt design av Log Analytics-arbetsytor](../platform/design-logs-deployment.md)