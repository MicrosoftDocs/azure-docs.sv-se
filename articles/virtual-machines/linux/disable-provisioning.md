---
title: Inaktivera eller ta bort etableringsagenten
description: Lär dig hur du inaktiverar eller tar bort etableringsagenten på virtuella Linux-datorer och avbildningar.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c70b02bdc554c723f53ad5f8c0d36c5eca87811e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774376"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Inaktivera eller ta bort Linux-agenten från virtuella datorer och avbildningar

Innan du tar bort Linux-agenten måste du förstå vad den virtuella datorn inte kan göra när Linux-agenten har tagits bort.

Tillägg för virtuella [Azure-datorer](../extensions/overview.md) är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distribution på virtuella Azure-datorer, tillägg installeras och hanteras av Azure-kontrollplanet. Det är Azure [Linux-agentens uppgift att](../extensions/agent-linux.md) bearbeta plattformstilläggskommandona och se till att tilläggets tillstånd är korrekt i den virtuella datorn.

Azure-plattformen är värd för många tillägg som sträcker sig från VM-konfiguration, övervakning, säkerhet och verktygsprogram. Det finns ett stort urval av tillägg från första och tredje part, exempel på viktiga scenarier som tillägg används för:
* Stöd för Azure-tjänster från första part, Azure Backup, övervakning, diskkryptering, säkerhet, platsreplikering med mera.
* SSH/lösenordsåterställning
* VM-konfiguration – Köra anpassade skript, installera Chef, Puppet-agenter osv.
* Produkter från tredje part, till exempel AV-produkter, sårbarhetsverktyg för virtuella datorer, virtuella datorer och appövervakningsverktyg.
* Tillägg kan paketeras med en ny VM-distribution. De kan till exempel ingå i en större distribution, konfigurera program på VM-etablering eller köras mot tilläggssystem som stöds efter distributionen.

## <a name="disabling-extension-processing"></a>Inaktivera tilläggsbearbetning

Det finns flera sätt att inaktivera tilläggsbearbetning, beroende på  dina behov, men innan du fortsätter måste du ta bort alla tillägg som distribueras till den virtuella datorn, till exempel med hjälp av Azure CLI, kan du lista [och](/cli/azure/vm/extension#az_vm_extension_list) [ta bort](/cli/azure/vm/extension#az_vm_extension_delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Om du inte gör ovanstående försöker plattformen skicka tilläggskonfigurationen och tidsgränsen efter 40 minuter.

### <a name="disable-at-the-control-plane"></a>Inaktivera på kontrollplanet
Om du inte är säker på om du behöver tillägg i framtiden kan du låta Linux-agenten vara installerad på den virtuella datorn och sedan inaktivera tilläggets bearbetningskapacitet från plattformen. Det här alternativet är tillgängligt i `Microsoft.Compute` API-versionen `2018-06-01` eller senare och är inte beroende av den linux-agentversion som är installerad.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Du kan enkelt återerablema den här tilläggsbearbetningen från plattformen med kommandot ovan, men ange den till "true".

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Ta bort Linux-agenten från en virtuell dator som körs

Se till att **du har** tagit bort alla befintliga tillägg från den virtuella datorn tidigare, enligt ovan.

### <a name="step-1-remove-the-azure-linux-agent"></a>Steg 1: Ta bort Azure Linux-agenten

Om du bara tar bort Linux-agenten, och inte de associerade konfigurationsartefakter, kan du installera om vid ett senare tillfälle. Kör något av följande, som rot, för att ta bort Azure Linux-agenten:

#### <a name="for-ubuntu-1804"></a>För Ubuntu >=18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>För Redhat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>För SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Steg 2: (Valfritt) Ta bort Azure Linux-agentartefakter
> [!IMPORTANT] 
>
> Du kan ta bort alla associerade artefakter för Linux-agenten, men det innebär att du inte kan installera om den vid ett senare tillfälle. Därför rekommenderar vi starkt att du inaktiverar Linux-agenten först, och endast tar bort Linux-agenten med ovanstående. 

Om du vet att du aldrig kommer att installera om Linux-agenten igen kan du köra följande:

#### <a name="for-ubuntu-1804"></a>För Ubuntu >=18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>För Redhat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>För SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Förbereda en avbildning utan Linux-agenten
Om du har en avbildning som redan innehåller cloud-init och du vill ta bort Linux-agenten, men ändå etablera med cloud-init, kör du stegen i steg 2 (och eventuellt steg 3) som rot för att ta bort Azure Linux-agenten. Därefter tar följande bort cloud-init-konfigurationen och cachelagrade data och förbereder den virtuella datorn för att skapa en anpassad avbildning.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Avetablera och skapa en avbildning
Linux-agenten kan rensa vissa befintliga bildmetadata med steget "waagent -deprovision+user", men när den har tagits bort måste du utföra åtgärder som nedan och ta bort andra känsliga data från den.

- Ta bort alla befintliga ssh-värdnycklar

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Ta bort administratörskontot

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Ta bort rotlösenordet

   ```bash
   passwd -d root
   ```

När du har slutfört ovanstående kan du skapa den anpassade avbildningen med hjälp av Azure CLI.


**Skapa en vanlig hanterad avbildning**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Skapa en avbildningsversion i en Shared Image Gallery**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Skapa en virtuell dator från en avbildning som inte innehåller en Linux-agent
När du skapar den virtuella datorn från avbildningen utan Linux-agent måste du se till att VM-distributionskonfigurationen anger att tillägg inte stöds på den här virtuella datorn.

> [!NOTE] 
> 
> Om du inte gör ovanstående försöker plattformen skicka tilläggskonfigurationen och tidsgränsen efter 40 minuter.

Om du vill distribuera den virtuella datorn med tillägg inaktiverade kan du använda Azure CLI [med --enable-agent](/cli/azure/vm#az_vm_create).

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Du kan också göra detta med arm Azure Resource Manager mallar genom att ange `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Etablera Linux.](provisioning.md)
