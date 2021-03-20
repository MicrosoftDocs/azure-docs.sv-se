---
title: Fullständig säkerhets kopiering/återställning och selektiv återställning för Azure Managed HSM
description: Det här dokumentet förklarar fullständig säkerhets kopiering/återställning och selektiv återställning
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e926dcd4b05d137c7927bdfe5221923d25d4670c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100093496"
---
# <a name="full-backup-and-restore"></a>Fullständig säkerhets kopiering och återställning

> [!NOTE]
> Den här funktionen är endast tillgänglig för resurs typen hanterad HSM.

Hanterad HSM har stöd för att skapa en fullständig säkerhets kopia av hela innehållet i HSM, inklusive alla nycklar, versioner, attribut, taggar och roll tilldelningar. Säkerhets kopian krypteras med de kryptografiska nycklar som är associerade med HSM: s säkerhets domän.

Säkerhets kopiering är en data Plans åtgärd. Anroparen som initierar säkerhets kopierings åtgärden måste ha behörighet att utföra dataAction **Microsoft. nyckel valv/managedHsm/backup/start/Action**.

Endast följande inbyggda roller har behörighet att utföra en fullständig säkerhets kopiering:
- Hanterad HSM-administratör
- Hanterad HSM-säkerhetskopiering

Du måste ange följande information för att köra en fullständig säkerhets kopiering:
- HSM-namn eller URL
- Lagringskontonamn
- Lagrings kontots Blob Storage-behållare
- SAS-token för lagrings behållare med behörigheter `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Fullständig säkerhetskopia

Backup är en tids krävande åtgärd, men returnerar omedelbart ett jobb-ID. Du kan kontrol lera status för säkerhets kopierings processen med detta jobb-ID. Säkerhets kopierings processen skapar en mapp inuti den utsedda behållaren med ett mönster **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , där HSM_NAME är namnet på hanterad HSM som säkerhets kopie ras och åååå, mm, DD, hh, mm, mm, SS är året, månaden, datumet, timmen, minuter och sekunder för datum/tid i UTC när säkerhets kopierings kommandot mottogs.

När säkerhets kopieringen pågår fungerar inte HSM vid fullständig genom strömning eftersom vissa HSM-partitioner är upptagna med att utföra säkerhets kopierings åtgärden.

```azurecli-interactive
# time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Fullständig återställning

Med fullständig återställning kan du återställa innehållet i HSM fullständigt med en tidigare säkerhets kopia, inklusive alla nycklar, versioner, attribut, taggar och roll tilldelningar. Allt som för närvarande lagras i HSM rensas och kommer att återgå till samma tillstånd som den var i när käll säkerhets kopian skapades.

> [!IMPORTANT]
> Fullständig återställning är en mycket förstörande och störande åtgärd. Därför är det nödvändigt att ha slutfört en fullständig säkerhets kopiering inom de senaste 30 minuterna innan en `restore` åtgärd kan utföras.

Restore är en data Plans åtgärd. Anroparen som startar återställnings åtgärden måste ha behörighet att utföra dataAction **Microsoft. nyckel valv/managedHsm/Restore/start/Action**. Den HSM-källa där säkerhets kopian skapades och den HSM för mål platsen där återställningen ska utföras **måste** ha samma säkerhets domän. Läs mer [om hanterad HSM-säkerhets domän](security-domain.md).

Du måste ange följande information för att köra en fullständig återställning:
- HSM-namn eller URL
- Lagringskontonamn
- BLOB-behållare för lagrings konto
- SAS-token för lagrings behållare med behörigheter `rl`
- Namn på mapp för lagrings behållare där käll säkerhets kopian lagras

Restore är en tids krävande åtgärd, men returnerar omedelbart ett jobb-ID. Du kan kontrol lera status för återställnings processen med detta jobb-ID. När återställnings processen pågår, anger HSM ett återställnings läge och alla data planet kommando (förutom kontrol lera återställnings status) inaktive ras.

```azurecli-interactive
#### time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>Återställ HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Nästa steg
- Se [hantera en hanterad HSM med Azure CLI](key-management.md).
- Läs mer om [hanterad HSM-säkerhetsdomän](security-domain.md)
