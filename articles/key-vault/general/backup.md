---
title: Säkerhetskopiera en hemlighet, nyckel eller ett certifikat som lagras i Azure Key Vault | Microsoft Docs
description: Använd det här dokumentet för att säkerhetskopiera en hemlighet, nyckel eller ett certifikat som lagras i Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b148ac83b89850cad66bcd7254d385e655cc2fb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968755"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault säkerhets kopiering

Det här dokumentet visar hur du säkerhetskopierar hemligheter, nycklar och certifikat som lagras i ditt nyckel valv. En säkerhets kopia är avsedd för att ge dig en offlinekopia av alla dina hemligheter i förmodad händelse att du förlorar åtkomst till ditt nyckel valv.

## <a name="overview"></a>Översikt

Azure Key Vault tillhandahåller automatiskt funktioner som hjälper dig att underhålla tillgänglighet och förhindra data förlust. Säkerhetskopiera endast hemligheter om du har en viktig affärs motivering. Att säkerhetskopiera hemligheter i ditt nyckel valv kan leda till drifts utmaningar, till exempel att underhålla flera uppsättningar av loggar, behörigheter och säkerhets kopior när hemligheterna upphör att gälla eller roteras.

Key Vault bibehåller tillgänglighet i katastrof scenarier och växlar automatiskt över begär anden till en kopplad region utan att användaren behöver göra något. Mer information finns i [Azure Key Vault tillgänglighet och redundans](./disaster-recovery-guidance.md).

Om du vill skydda mot oavsiktlig eller skadlig borttagning av dina hemligheter konfigurerar du funktionerna för mjuk borttagning och rensning av skydd i nyckel valvet. Mer information finns i [Översikt över Azure Key Vault mjuk borttagning](./soft-delete-overview.md).

## <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> Key Vault stöder inte möjligheten att säkerhetskopiera fler än 500 tidigare versioner av ett nyckel-, hemligt eller certifikat objekt. Försök att säkerhetskopiera en nyckel, ett hemligt objekt eller ett certifikat objekt kan resultera i ett fel. Det går inte att ta bort tidigare versioner av en nyckel, hemlighet eller certifikat.

Key Vault tillhandahåller för närvarande inte ett sätt att säkerhetskopiera hela nyckel valvet i en enda åtgärd. Alla försök att använda de kommandon som anges i det här dokumentet för att göra en automatiserad säkerhets kopiering av ett nyckel valv kan resultera i fel och stöds inte av Microsoft eller Azure Key Vaults teamet. 

Tänk också på följande konsekvenser:

* Att säkerhetskopiera hemligheter som har flera versioner kan orsaka timeout-fel.
* En säkerhets kopiering skapar en tidpunkts ögonblicks bild. Hemligheter kan förnyas under en säkerhets kopiering, vilket orsakar ett matchnings fel i krypterings nycklarna.
* Om du överskrider gränserna för Key Vault-tjänsten för begär Anden per sekund kommer ditt nyckel valv att begränsas och säkerhets kopieringen kommer att Miss kopie ras.

## <a name="design-considerations"></a>Designöverväganden

När du säkerhetskopierar ett nyckel valvs objekt, t. ex. en hemlighet, nyckel eller certifikat, laddar säkerhets kopierings åtgärden objektet som en krypterad blob. Denna BLOB kan inte dekrypteras utanför Azure. Om du vill hämta användbara data från denna BLOB måste du återställa blobben till ett nyckel valv i samma Azure-prenumeration och [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="prerequisites"></a>Förutsättningar

Om du vill säkerhetskopiera ett Key Vault-objekt måste du ha: 

* Deltagar nivå eller högre behörigheter för en Azure-prenumeration.
* Ett primärt nyckel valv som innehåller de hemligheter som du vill säkerhetskopiera.
* Ett sekundärt nyckel valv där hemligheter kommer att återställas.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Säkerhetskopiera och återställa från Azure Portal

Följ stegen i det här avsnittet för att säkerhetskopiera och återställa objekt med hjälp av Azure Portal.

### <a name="back-up"></a>Säkerhetskopiera

1. Gå till Azure-portalen.
2. Välj ditt nyckel valv.
3. Gå till objektet (hemligt, nyckel eller certifikat) som du vill säkerhetskopiera.

    ![Skärm bild som visar var du väljer inställningen nycklar och ett objekt i ett nyckel valv.](../media/backup-1.png)

4. Markera objektet.
5. Välj **Hämta säkerhets kopia**.

    ![Skärm bild som visar var du väljer knappen Hämta säkerhets kopia i ett nyckel valv.](../media/backup-2.png)
    
6. Välj **Hämta**.

    ![Skärm bild som visar var du väljer nedladdnings knappen i ett nyckel valv.](../media/backup-3.png)
    
7. Lagra den krypterade blobben på en säker plats.

### <a name="restore"></a>Återställ

1. Gå till Azure-portalen.
2. Välj ditt nyckel valv.
3. Gå till den typ av objekt (hemlighet, nyckel eller certifikat) som du vill återställa.
4. Välj **Återställ säkerhets kopia**.

    ![Skärm bild som visar var du väljer Återställ säkerhets kopia i ett nyckel valv.](../media/backup-4.png)
    
5. Gå till den plats där du sparade den krypterade blobben.
6. Välj **OK**.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Säkerhetskopiera och återställa från Azure CLI eller Azure PowerShell

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