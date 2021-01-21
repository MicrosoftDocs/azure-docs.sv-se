---
title: Fel i Windows Boot Manager-0xC0000428 status ogiltigt bild-hash
titlesuffix: Azure Virtual Machines
description: Den här artikeln innehåller steg för att lösa problem där en förhands gransknings bild användes och utvärderings perioden har upphört att gälla, vilket förhindrar start av en virtuell Azure-dator (VM).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: 22db9c7966b6f988ca0ea799104275f3f86c77ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629580"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Fel i Windows Boot Manager-0xC0000428 status ogiltigt bild-hash

Den här artikeln innehåller steg för att lösa problem där en förhands gransknings bild användes och utvärderings perioden har upphört att gälla, vilket förhindrar start av en virtuell Azure-dator (VM).

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar Windows Boot Manager med meddelandet:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![Bild 1 visar Windows Boot Manager med status "Ox0000428" och "information" det går inte att verifiera den digitala signaturen för den här filen ".](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

eller meddelandet:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![Bild 2 visar Windows Boot Manager med status "Ox0000428" och information "Det gick inte att verifiera den digitala signaturen för den här filen".](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Orsak

Avbildningen som användes för att bygga den virtuella datorn var en förhands gransknings avbildning med ett förfallo datum snarare än en RTM-avbildning (version till tillverkning). 

Förhands gransknings bilder har en angiven livs cykel och skärm bilden som visas visas när du skickar förfallo datumet, vilket innebär att bildens utvärderings version är över.

### <a name="example-of-preview-images"></a>Exempel på förhands gransknings bilder

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Du kan inte förlänga utgångsdatumet för en förhandsversionsavbildning. När förhandsversionen har upphört att gälla, går det inte längre att starta den virtuella datorn.

- Beroende på produkten kan utvärderings perioden variera. Till exempel har Windows Preview-avbildningar en 180-dagars utvärderings period.

- I Azure innehåller alla avbildningar för Windows som är för hands versioner en anteckning om att de inte är avsedda för produktion och att de endast är tillgängliga för användning under en angiven utvärderings period eller som en "för hands version".

## <a name="solution"></a>Lösning

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Om avbildningen är en förhands gransknings bild finns det inget sätt att förlänga förfallo datumet för den använda avbildningen. du måste [distribuera en ny virtuell dator](../windows/quick-create-portal.md) med en icke-förhands gransknings avbildning. Stegen nedan hjälper dig att identifiera om du har använt en förhands gransknings avbildning och ger resurser som hjälper dig att överföra data från den här virtuella datorn till en ny virtuell dator. Om du har identifierat avbildningen som en förhands gransknings bild går det inte att återställa bilden eftersom den nu har gått ut.

Beroende på din preferens kan du använda antingen Azure PowerShell eller Azure CLI för att fråga din avbildning för att avgöra om det är en förhands gransknings bild. Du kan använda dessa kommandon för att bekräfta att avbildningen är en förhands gransknings bild.

### <a name="query-using-azure-powershell"></a>Fråga med Azure PowerShell

1. Öppna Windows PowerShell-programmet.
1. Kör följande kommandon:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- I föregående kommandon ersätter,, `<LOCATION>` `<PUBLISHER NAME>` `<OFFER NAME>` och `<YEAR WHEN THIS IMAGE WAS RELEASED>` med den information som anges. Ta även bort symbolerna "<" och ">".

  Se följande exempel:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Fråga med Azure CLI

1. Om du inte redan har gjort det måste du [Installera Azure CLI](/cli/azure/install-azure-cli).
1. När du har hämtat använder du antingen kommando tolken eller PowerShell för att ange `az login` kommandot och sedan loggar du in med autentiseringsuppgifterna för ditt konto.
1. När du har loggat in anger du följande kommandon:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- I föregående kommandon ersätter,, `<LOCATION>` `<PUBLISHER NAME>` `<OFFER NAME>` och `<YEAR WHEN THIS IMAGE WAS RELEASED>` med den information som anges. Ta även bort symbolerna "<" och ">".

  Se följande exempel:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```