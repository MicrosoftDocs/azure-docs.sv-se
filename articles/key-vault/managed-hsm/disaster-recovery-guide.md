---
title: Vad du gör om ett avbrott i Azure-tjänsten påverkar hanterad HSM-Azure Key Vault | Microsoft Docs
description: Lär dig vad du ska göra f det uppstår ett avbrott i Azure-tjänsten som påverkar hanterad HSM.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8c284e9993002f6e05e41ca00d00b388feef8f82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376011"
---
# <a name="managed-hsm-disaster-recovery"></a>Katastrof återställning av hanterad HSM

Du kanske vill skapa en exakt replik av HSM om originalet tappas bort eller inte är tillgängligt på grund av någon av följande orsaker:

- Den togs bort och rensades sedan.
- Ett oåterkalleligt haveri i regionen resulterade i att alla medlemsvariabler förstördes.

Du kan återskapa HSM-instansen i samma eller en annan region om du har följande:
- Käll-HSM: en [säkerhets domän](security-domain.md) .
- De privata nycklarna (minst kvorum) som krypterar säkerhets domänen.
- Den senaste fullständiga HSM- [säkerhetskopieringen](backup-restore.md) från käll-HSM.

Här följer stegen i processen för haveri beredskap:

1. Skapa en ny HSM-instans.
1. Aktivera återställning av säkerhets domän. Ett nytt RSA-nyckelpar (säkerhets domänens Exchange-nyckel) skapas för överföring av säkerhets domän och skickas som svar, som kommer att laddas ned som en SecurityDomainExchangeKey (offentlig nyckel).
1. Skapa och ladda upp "säkerhets domän överförings filen". Du kommer att behöva de privata nycklar som krypterar säkerhets domänen. Privata nycklar används lokalt och överförs aldrig var som helst i den här processen.
1. Gör en säkerhets kopia av den nya HSM-filen. En säkerhets kopia krävs före eventuell återställning, även om HSM är tom. Säkerhets kopiorna är enkla att återställa.
1. Återställa den senaste HSM-säkerhetskopieringen från käll-HSM

Med de här stegen kan du replikera innehållet i HSM manuellt till en annan region. HSM-namnet (och tjänstens slut punkts-URI) är olika, så du kan behöva ändra program konfigurationen för att använda dessa nycklar från en annan plats.

## <a name="create-a-new-managed-hsm"></a>Skapa en ny hanterad HSM

Använd `az keyvault create` kommandot för att skapa en hanterad HSM. Det här skriptet har tre obligatoriska parametrar: ett resurs grupps namn, ett HSM-namn och den geografiska platsen.

Du måste ange följande indata för att skapa en hanterad HSM-resurs:

- Namnet på HSM.
- Resurs gruppen där den kommer att placeras i din prenumeration.
- Azure-platsen.
- En lista med inledande administratörer.

Exemplet nedan skapar en HSM med namnet **ContosoMHSM** i resurs gruppen  **ContosoResourceGroup**, som finns på platsen **östra USA 2** , med **den aktuella inloggade användaren** som administratör.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Kommandot CREATE kan ta några minuter. När den har returnerats är du redo att aktivera HSM.

Utdata från det här kommandot visar egenskaper för den hanterade HSM som du har skapat. De två viktigaste egenskaperna är:

* **namn**: i exemplet är namnet ContosoMHSM. Du kommer att använda det här namnet för andra Key Vault-kommandon.
* **hsmUri**: i exemplet är URI: n " https://contosohsm.managedhsm.azure.net ." Program som använder din HSM via sin REST API måste använda denna URI.

Ditt Azure-konto har nu behörighet att utföra alla åtgärder på den här hanterade HSM. Från och med har ingen annan behörighet.

## <a name="activate-the-security-domain-recovery-mode"></a>Aktivera återställnings läget för säkerhets domän

I den normala skapa-processen initierar vi och laddar ned en ny säkerhets domän i det här läget. Men eftersom vi kör en katastrof återställnings procedur begär vi HSM att gå in i säkerhets domänens återställnings läge och ladda ned en säkerhets domän utbytes nyckel. Säkerhets domänens Exchange-nyckel är en offentlig RSA-nyckel som används för att kryptera säkerhets domänen innan den laddas upp till HSM. Motsvarande privata nyckel skyddas i HSM för att skydda ditt säkerhets domän innehåll under överföringen.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Överför säkerhets domän till mål-HSM

För det här steget behöver du följande:
- Den säkerhets domän utbytes nyckel som du laddade ned i föregående steg.
- Käll-HSM: en säkerhets domän.
- Minst antal privata nycklar som användes för att kryptera säkerhets domänen.

`az keyvault security-domain upload`Kommandot utför följande åtgärder:

- Dekryptera käll-HSM: s säkerhets domän med de privata nycklar som du anger. 
- Skapa en säkerhets domän överförings-BLOB krypterad med den säkerhets domän som vi laddade ned i föregående steg och sedan
- Ladda upp den säkerhetsdomän som överför blobben till HSM för att slutföra säkerhets domän återställning

I exemplet nedan använder vi säkerhets domänen från **ContosoMHSM**, 2 av motsvarande privata nycklar och överför den till **ContosoMHSM2**, vilket väntar på att ta emot en säkerhets domän. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Nu har både ContosoMHSM (Source HSM) och mål-HSM (ContosoMHSM2) samma säkerhets domän. Vi kan nu återställa en fullständig säkerhets kopia från källans HSM till mål-HSM.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Skapa en säkerhets kopia (som en återställnings punkt) för din nya HSM

Det är alltid en bra idé att göra en fullständig säkerhets kopia innan du kör en fullständig HSM-återställning, så att du har en återställnings punkt om något går fel med återställningen.

Om du vill skapa en HSM-säkerhetskopiering behöver du följande
- Ett lagrings konto där säkerhets kopian ska lagras
- En Blob Storage-behållare i det här lagrings kontot där säkerhets kopieringen ska skapa en ny mapp för att lagra en krypterad säkerhets kopia

Vi använder `az keyvault backup` kommandot för HSM-säkerhetskopieringen i lagrings behållaren **mhsmbackupcontainer**, som finns i **ContosoBackup** för lagrings kontot i exemplet nedan. Vi skapar en SAS-token som upphör att gälla om 30 minuter och anger att en hanterad HSM ska skriva säkerhets kopian.

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Återställ säkerhets kopia från källa HSM

I det här steget behöver du följande:

- Lagrings kontot och blob-behållaren där käll-HSM: s säkerhets kopior lagras.
- Mappnamnet som du vill återställa säkerhets kopian från. Om du skapar vanliga säkerhets kopieringar kommer det att finnas många mappar i den här behållaren.


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Nu har du slutfört en fullständig katastrof återställnings process. Innehållet i käll-HSM när säkerhets kopieringen gjordes kopieras till mål-HSM, inklusive alla nycklar, versioner, attribut, taggar och roll tilldelningar.

## <a name="next-steps"></a>Nästa steg

- Läs mer om säkerhets domän se [om hanterad HSM-säkerhetsdomän](security-domain.md)
- Följ [metod tips för hanterad HSM](best-practices.md)
