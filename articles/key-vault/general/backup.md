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
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: a1c07432dcf90759662e8f4aaedc760abd18157c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585941"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault säkerhets kopiering

Det här dokumentet visar hur du säkerhetskopierar hemligheter, nycklar och certifikat som lagras i ditt nyckel valv. En säkerhets kopia är avsedd för att ge dig en offlinekopia av alla dina hemligheter i förmodad händelse att du förlorar åtkomst till ditt nyckel valv.

## <a name="overview"></a>Översikt

Azure Key Vault tillhandahåller automatiskt funktioner som hjälper dig att underhålla tillgänglighet och förhindra data förlust. Säkerhetskopiera endast hemligheter om du har en viktig affärs motivering. Att säkerhetskopiera hemligheter i ditt nyckel valv kan leda till drifts utmaningar, till exempel att underhålla flera uppsättningar av loggar, behörigheter och säkerhets kopior när hemligheterna upphör att gälla eller roteras.

Key Vault bibehåller tillgänglighet i katastrof scenarier och växlar automatiskt över begär anden till en kopplad region utan att användaren behöver göra något. Mer information finns i [Azure Key Vault tillgänglighet och redundans](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).

Om du vill skydda mot oavsiktlig eller skadlig borttagning av dina hemligheter konfigurerar du funktionerna för mjuk borttagning och rensning av skydd i nyckel valvet. Mer information finns i [Översikt över Azure Key Vault mjuk borttagning](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview).

## <a name="limitations"></a>Begränsningar

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

## <a name="back-up-and-restore-from-the-azure-cli"></a>Säkerhetskopiera och återställa från Azure CLI

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

## <a name="next-steps"></a>Nästa steg

Aktivera [loggning och övervakning](https://docs.microsoft.com/azure/key-vault/general/logging) för Key Vault.
