---
title: Konfigurera Kundhanterade nycklar för ditt Azure Batch-konto med Azure Key Vault och hanterad identitet
description: Lär dig hur du krypterar batch-data med hjälp av nycklar
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 2ed19846209d098d9eba8dba991e08d1fc57f185
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678017"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Konfigurera Kundhanterade nycklar för ditt Azure Batch-konto med Azure Key Vault och hanterad identitet

Som standard använder Azure Batch plattforms hanterade nycklar för att kryptera alla kund data som lagras i Azure Batch-tjänsten, t. ex. certifikat, metadata för jobb/aktivitet. Du kan också använda egna nycklar, t. ex. Kundhanterade nycklar, för att kryptera data som lagras i Azure Batch.

De nycklar som du anger måste genereras i [Azure Key Vault](../key-vault/general/basic-concepts.md)och de batch-konton som du vill konfigurera med Kundhanterade nycklar måste aktive ras med [Azure-hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Stöd för Kundhanterade nycklar i Azure Batch är för närvarande en offentlig för hands version för Västeuropa, norra Europa, Schweiz, norra, centrala USA, södra centrala USA, västra centrala USA, östra USA, östra USA 2, västra USA 2, US Gov, Virginia och US Gov, Arizona regioner.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Skapa ett batch-konto med systemtilldelad hanterad identitet

### <a name="azure-portal"></a>Azure Portal

När du skapar batch-konton i [Azure Portal](https://portal.azure.com/)väljer du **system som tilldelats** i identitets typen under fliken **Avancerat** .

![Nytt batch-konto med tilldelad identitets typ](./media/batch-customer-managed-key/create-batch-account.png)

När kontot har skapats kan du hitta ett unikt GUID i fältet **identitetens huvud-ID** under **egenskaps** avsnittet. **Identitets typen** visas `SystemAssigned` .

![Unikt GUID i fältet identitetens huvud namn](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Azure CLI

När du skapar ett nytt batch-konto anger du `SystemAssigned` för- `--identity` parametern.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

När kontot har skapats kan du kontrol lera att systemtilldelad hanterad identitet har Aktiver ATS för det här kontot. Tänk på att Observera att det `PrincipalId` här värdet krävs för att bevilja det här batch-kontot åtkomst till Key Vault.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Den systemtilldelade hanterade identiteten som skapats i ett batch-konto används bara för att hämta Kundhanterade nycklar från Key Vault. Den här identiteten är inte tillgänglig i batch-pooler.

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurera Azure Key Vault-instansen

### <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

När du skapar en Azure Key Vault-instans med Kundhanterade nycklar för Azure Batch, se till att både **mjuk borttagning** och **rensning** är aktiverade.

![Skärmen för att skapa Key Vault](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Lägg till en åtkomst princip till Azure Key Vault-instansen

När Key Vault har skapats i Azure Portal lägger du till batch-kontot åtkomst med hanterad identitet i **åtkomst principen** under **inställning**. Under **nyckel behörigheter** väljer du **Hämta**, **Radbryt nyckel** och **packa upp nyckel**. 

![Lägg till åtkomst princip](./media/batch-customer-managed-key/key-permissions.png)

I **Välj** -fältet under **huvud namn** fyller du i den `principalId` som du har hämtat tidigare eller namnet på batch-kontot.

![Ange principalId](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Generera en nyckel i Azure Key Vault

I Azure Portal går du till Key Vault-instansen i avsnittet **nyckel** och väljer **generera/importera**. Välj den **nyckel typ** som ska vara `RSA` och **RSA-nyckel storleken** måste vara minst `2048` bitar. `EC` nyckel typer stöds för närvarande inte som en kundhanterad nyckel på ett batch-konto.

![Skapa en nyckel](./media/batch-customer-managed-key/create-key.png)

När nyckeln har skapats klickar du på den nya nyckeln och den aktuella versionen, kopierar **nyckel identifieraren** under **Egenskaper** -avsnittet.  Se till att både **rad brytning** och **unwrap-nyckeln** är markerade under **tillåtna åtgärder**.

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>Aktivera Kundhanterade nycklar på Azure Batch konto

### <a name="azure-portal"></a>Azure Portal

Gå till sidan för batch-kontot i [Azure Portal](https://portal.azure.com/). Aktivera **kundhanterad nyckel** under avsnittet **kryptering** . Du kan använda nyckel identifieraren direkt eller så kan du välja nyckel valvet och sedan klicka på **Välj ett nyckel valv och nyckel**.

![Aktivera kundhanterad nyckel under kryptering](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

När batch-kontot har skapats med systemtilldelad hanterad identitet och åtkomsten till Key Vault beviljas, uppdaterar du batch-kontot med `{Key Identifier}` URL-adressen under `keyVaultProperties` parametern. Ange också **encryption_key_source** som `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>Uppdatera den kund hanterade nyckel versionen

När du skapar en ny version av en nyckel uppdaterar du batch-kontot för att använda den nya versionen. Följ de här stegen:

1. Navigera till ditt batch-konto i Azure Portal och Visa krypterings inställningarna.
2. Ange URI för den nya nyckel versionen. Alternativt kan du välja nyckel valvet och nyckeln igen för att uppdatera versionen.
3. Spara ändringarna.

Du kan också använda Azure CLI för att uppdatera versionen.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>Använd en annan nyckel för batch-kryptering

Följ dessa steg om du vill ändra den nyckel som används för batch-kryptering:

1. Navigera till ditt batch-konto och Visa krypterings inställningarna.
2. Ange URI för den nya nyckeln. Alternativt kan du välja nyckel valvet och välja en ny nyckel.
3. Spara ändringarna.

Du kan också använda Azure CLI för att använda en annan nyckel.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
  * **Stöds kund hanterade nycklar för befintliga batch-konton?** Nej. Kundhanterade nycklar stöds bara för nya batch-konton.
  * **Kan jag välja RSA-nyckel storlekar som är större än 2048 bitar?** Ja, RSA-nyckel storlekar `3072` och `4096` BITS stöds också.
  * **Vilka åtgärder är tillgängliga när en kundhanterad nyckel har återkallats?** Den enda åtgärd som tillåts är borttagning av konto om batch förlorar åtkomsten till den Kundhanterade nyckeln.
  * **Hur ska jag återställa åtkomsten till mitt batch-konto om jag råkar ta bort Key Vault nyckeln?** Eftersom rensnings skyddet och mjuk borttagning är aktiverat kan du återställa befintliga nycklar. Mer information finns i [återställa en Azure Key Vault](../key-vault/general/key-vault-recovery.md).
  * **Kan jag inaktivera Kundhanterade nycklar?** Du kan när som helst ange krypterings typen för batch-kontot tillbaka till "Microsoft Managed Key". Därefter är du kostnads fri att ta bort eller ändra nyckeln.
  * **Hur kan jag rotera mina nycklar?** Kundhanterade nycklar roteras inte automatiskt. Om du vill rotera nyckeln uppdaterar du nyckel identifieraren som kontot är associerat med.
  * **När jag har återställt åtkomst hur lång tid tar det för batch-kontot att fungera igen?** Det kan ta upp till 10 minuter innan kontot kan nås igen när åtkomsten har återställts.
  * **När batch-kontot inte är tillgängligt vad händer med mina resurser?** Alla pooler som körs när batch-åtkomst till Kundhanterade nycklar går förlorade kommer att fortsätta att köras. Noderna övergår dock till ett otillgängligt tillstånd och aktiviteterna kommer att sluta köras (och köas). När åtkomsten återställs blir noderna tillgängliga igen och aktiviteter kommer att startas om.
  * **Gäller den här krypterings funktionen för VM-diskar i en batch-pool?** Nej. För konfigurations pooler för moln tjänster tillämpas ingen kryptering för operativ systemet och den temporära disken. För konfigurations pooler för virtuella datorer krypteras operativ systemet och de angivna data diskarna med en hanterad Microsoft Platform-nyckel som standard. För närvarande kan du inte ange din egen nyckel för de här diskarna. Om du vill kryptera den temporära disken för virtuella datorer för en batch-pool med en hanterad nyckel för Microsoft-plattform måste du aktivera egenskapen [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) i den [virtuella datorns konfigurations](/rest/api/batchservice/pool/add#virtualmachineconfiguration) uppsättning. För mycket känsliga miljöer rekommenderar vi att du aktiverar temporär disk kryptering och undviker att lagra känsliga data på operativ system och data diskar. Mer information finns i [skapa en pool med disk kryptering aktiverat](./disk-encryption.md)
  * **Är den systemtilldelade hanterade identiteten på batch-kontot som är tillgängligt på Compute-noderna?** Nej. Den här hanterade identiteten används för närvarande endast för att få åtkomst till Azure Key Vault för den Kundhanterade nyckeln.
