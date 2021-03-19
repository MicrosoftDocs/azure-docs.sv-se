---
title: Anpassade data och Azure-Virtual Machines
description: Information om hur du använder anpassade data och Cloud-Init på Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87284922"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Anpassade data och Cloud-Init på Azure Virtual Machines

Du kan behöva mata in ett skript eller andra metadata i en Microsoft Azure virtuell dator vid etablerings tiden.  I andra moln kallas det här konceptet ofta användar data.  I Microsoft Azure har vi en liknande funktion som kallas anpassade data. 

Anpassade data görs bara tillgängliga för den virtuella datorn under den första starten/första installationen. vi kallar denna "etablering". Etablering är den process där virtuella dator parametrar (till exempel värdnamn, användar namn, lösen ord, certifikat, anpassade data, nycklar osv.) görs tillgängliga för den virtuella datorn och en etablerings agent bearbetar dem, till exempel [Linux-agenten](./extensions/agent-linux.md) och [Cloud-Init](./linux/using-cloud-init.md#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Skickar anpassade data till den virtuella datorn
Om du vill använda anpassade data måste du base64 koda innehållet först innan du skickar det till API: et, om du inte använder ett CLI-verktyg som gör konverteringen åt dig, till exempel AZ CLI. Storleken får inte överstiga 64 KB.

I CLI kan du skicka dina anpassade data som en fil och den kommer att konverteras till base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

I Azure Resource Manager (ARM) finns en Base64- [funktion](../azure-resource-manager/templates/template-functions-string.md#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Bearbetar anpassade data
De etablerings agenter som är installerade på de virtuella datorerna hanterar interaktivt med plattformen och placerar dem i fil systemet. 

### <a name="windows"></a>Windows
Anpassade data placeras i *%systemdrive%\AzureData\CustomData.bin* som en binär fil, men bearbetas inte. Om du vill bearbeta den här filen måste du skapa en anpassad avbildning och skriva kod för att bearbeta CustomData. bin.

### <a name="linux"></a>Linux  
På Linux-operativsystem skickas anpassade data till den virtuella datorn via ovf-env.xml-filen, som kopieras till */var/lib/waagent* -katalogen under etableringen.  Nyare versioner av Microsoft Azure Linux-agenten kommer också att kopiera base64-kodade data till */var/lib/waagent/CustomData* för bekvämlighet.

Azure har för närvarande stöd för två etablerings agenter:
* Linux-Agent – som standard kommer agenten inte att bearbeta anpassade data. du måste bygga en anpassad avbildning med den aktive rad. De relevanta inställningarna enligt [dokumentationen](https://github.com/Azure/WALinuxAgent#configuration) är:
    * Etablering. DecodeCustomData
    * Provisioning.ExecuteCustomData

När du aktiverar anpassade data och kör ett skript, kommer det att fördröja den VM-rapportering som är klar eller att etableringen har slutförts tills skriptet har slutförts. Om skriptet överskrider den totala tids gränsen för VM-etablering på 40 minuter kommer den virtuella datorns skapande att Miss klaras. Obs! Om skriptet inte kan köras eller fel under körningen, betraktas det inte som ett oåterkalleligt etablerings fel. du måste skapa en meddelande Sök väg för att varna dig om skriptets slut för ande tillstånd.

Om du vill felsöka anpassad data körning granskar du */var/log/waagent.log*

* Cloud-Init-som standard bearbetar anpassade data som standard, Cloud-Init accepterar [flera format](https://cloudinit.readthedocs.io/en/latest/topics/format.html) för anpassade data, till exempel Cloud-Init-konfiguration, skript osv. Liknar Linux-agenten, när Cloud-Init bearbetar anpassade data. Om det uppstår fel under körningen av konfigurations bearbetningen eller skripten, betraktas det inte som ett oåterkalleligt etablerings fel och du måste skapa en meddelande Sök väg för att varna dig om skriptets slut för ande tillstånd. Men annorlunda för Linux-agenten väntar Cloud-Init inte på användarens anpassade datakonfigurationer som ska slutföras innan rapportering till den plattform som den virtuella datorn är klar för. Läs [dokumentationen](./linux/using-cloud-init.md)om du vill ha mer information om Cloud-Init på Azure.


Om du vill felsöka anpassad data körning läser du fel söknings [dokumentationen](./linux/using-cloud-init.md#troubleshooting-cloud-init).


## <a name="faq"></a>Vanliga frågor
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Kan jag uppdatera anpassade data när den virtuella datorn har skapats?
För enskilda virtuella datorer går det inte att uppdatera anpassade data i VM-modellen, men för VMSS kan du uppdatera VMSS anpassade data via [REST API](/rest/api/compute/virtualmachinescalesets/update) (inte tillämpligt för PS-eller AZ CLI-klienter). När du uppdaterar anpassade data i VMSS-modellen:
* Befintliga instanser i VMSS kommer inte att hämta uppdaterade anpassade data, bara tills de har återställts på avbildningen.
* Befintliga instanser i VMSS som uppgraderas kommer inte att hämta uppdaterade anpassade data.
* Nya instanser får nya anpassade data.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Kan jag placera känsliga värden i anpassade data?
Vi rekommenderar att du **inte** lagrar känsliga data i anpassade data. Mer information finns i [metod tips för Azure-säkerhet och kryptering](../security/fundamentals/data-encryption-best-practices.md).


### <a name="is-custom-data-made-available-in-imds"></a>Är anpassade data som görs tillgängliga i IMDS?
Nej, den här funktionen är inte tillgänglig för tillfället.
