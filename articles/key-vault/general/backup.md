---
title: Back up a secret, key, or certificate stored in Azure Key Vault | Microsoft Docs
description: Använd det här dokumentet för att backa upp en hemlighet, nyckel eller ett certifikat som lagras i Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 399f55d379f99a784fed97d7e9f72c456eb1f914
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484820"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault säkerhetskopiering

Det här dokumentet visar hur du kan valva upp hemligheter, nycklar och certifikat som lagras i ditt nyckelvalv. En säkerhetskopia är avsedd att ge dig en offline-kopia av alla dina hemligheter om du sannolikt skulle förlora åtkomsten till ditt nyckelvalv.

## <a name="overview"></a>Översikt

Azure Key Vault automatiskt funktioner som hjälper dig att upprätthålla tillgängligheten och förhindra dataförlust. Back up secrets only if you have a critical business justification. Säkerhetskopiering av hemligheter i nyckelvalvet kan leda till driftutmaningar som att underhålla flera uppsättningar med loggar, behörigheter och säkerhetskopior när hemligheter upphör att gälla eller roteras.

Key Vault upprätthåller tillgängligheten i katastrofscenarier och redundanskopplar automatiskt begäranden till en länkad region utan att användaren behöver göra något. Mer information finns i [Azure Key Vault tillgänglighet och redundans.](./disaster-recovery-guidance.md)

Om du vill skydda mot oavsiktlig eller skadlig borttagning av dina hemligheter konfigurerar du funktioner för mjuk borttagning och rensning av skydd i nyckelvalvet. Mer information finns i [Azure Key Vault översikt över mjuk borttagning.](./soft-delete-overview.md)

## <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> Key Vault stöder inte möjligheten att säkerhetskopiera fler än 500 tidigare versioner av en nyckel, hemlighet eller ett certifikatobjekt. Försök att säkerhetskopiera en nyckel, hemlighet eller ett certifikatobjekt kan resultera i ett fel. Det går inte att ta bort tidigare versioner av en nyckel, hemlighet eller ett certifikat.

Key Vault för närvarande inte ett sätt att valva upp ett helt nyckelvalv i en enda åtgärd. Alla försök att använda kommandona som anges i det här dokumentet för att göra en automatisk säkerhetskopiering av ett nyckelvalv kan resultera i fel och stöds inte av Microsoft eller Azure Key Vault teamet. 

Tänk också på följande:

* När hemligheter som har flera versioner kan det leda till time out-fel.
* En säkerhetskopia skapar en tidpunktsögonblicksbild. Hemligheter kan förnyas under en säkerhetskopiering, vilket orsakar ett matchningsfel för krypteringsnycklar.
* Om du överskrider tjänstgränserna för nyckelvalvet för begäranden per sekund begränsas nyckelvalvet och säkerhetskopieringen misslyckas.

## <a name="design-considerations"></a>Designöverväganden

När du säkerhetskopierar ett nyckelvalvsobjekt, till exempel en hemlighet, en nyckel eller ett certifikat, laddar säkerhetskopieringsåtgärden ned objektet som en krypterad blob. Den här bloben kan inte dekrypteras utanför Azure. Om du vill hämta användbara data från den här bloben måste du återställa bloben till ett nyckelvalv inom samma Azure-prenumeration och [Azure-geografi.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Förutsättningar

Om du vill valva ett nyckelvalvsobjekt måste du ha: 

* Behörighet på deltagarnivå eller högre för en Azure-prenumeration.
* Ett primärt nyckelvalv som innehåller de hemligheter som du vill valva upp.
* Ett sekundärt nyckelvalv där hemligheter kommer att återställas.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Back up and restore from the Azure Portal

Följ stegen i det här avsnittet för att återställning och återställning av objekt med hjälp av Azure Portal.

### <a name="back-up"></a>Säkerhetskopiera

1. Gå till Azure-portalen.
2. Välj ditt nyckelvalv.
3. Gå till det objekt (hemlighet, nyckel eller certifikat) som du vill backa upp.

    ![Skärmbild som visar var du väljer inställningen Nycklar och ett objekt i ett nyckelvalv.](../media/backup-1.png)

4. Välj objektet .
5. Välj **Download Backup (Ladda ned säkerhetskopia).**

    ![Skärmbild som visar var du väljer knappen Ladda ned säkerhetskopiering i ett nyckelvalv.](../media/backup-2.png)
    
6. Välj **Hämta**.

    ![Skärmbild som visar var du väljer knappen Ladda ned i ett nyckelvalv.](../media/backup-3.png)
    
7. Lagra den krypterade bloben på en säker plats.

### <a name="restore"></a>Återställ

1. Gå till Azure-portalen.
2. Välj ditt nyckelvalv.
3. Gå till den typ av objekt (hemlighet, nyckel eller certifikat) som du vill återställa.
4. Välj **Återställ säkerhetskopiering.**

    ![Skärmbild som visar var du väljer Återställ säkerhetskopiering i ett nyckelvalv.](../media/backup-4.png)
    
5. Gå till den plats där du lagrade den krypterade bloben.
6. Välj **OK**.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Back up and restore from the Azure CLI or Azure PowerShell

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```azurepowershell
## Log in to Azure
Connect-AzAccount

## Set your subscription
Set-AzContext -Subscription '{AZURE SUBSCRIPTION ID}'

## Back up a certificate in Key Vault
Backup-AzKeyVaultCertificate -VaultName '{Certificate Name}' -Name '{Key Vault Name}'

## Back up a key in Key Vault
Backup-AzKeyVaultKey -VaultName '{Key Name}' -Name '{Key Vault Name}'

## Back up a secret in Key Vault
Backup-AzKeyVaultSecret -VaultName '{Key Vault Name}' -Name '{Secret Name}'

## Restore a certificate in Key Vault
Restore-AzKeyVaultCertificate -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a key in Key Vault
Restore-AzKeyVaultKey -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a secret in Key Vault
Restore-AzKeyVaultSecret -VaultName '{Key Vault Name}' -InputFile '{File Path}'
```
---

## <a name="next-steps"></a>Nästa steg

Aktivera [loggning och övervakning](./logging.md) för Key Vault.
