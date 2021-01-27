---
title: Red Hat Enterprise Linux Azure-avbildningar med egen prenumeration | Microsoft Docs
description: Lär dig mer om att överföra prenumerations avbildningar för Red Hat Enterprise Linux på Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 7006096154d8958e13e7a86783bd07067b95d293
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872900"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux guld-avbildningar för prenumerationer i Azure

Red Hat Enterprise Linux-avbildningar (RHEL) är tillgängliga i Azure via en "betala per användning"-eller "BYOS (Red Hat Gold image)-modell. Den här artikeln innehåller en översikt över de Red Hat Gold-avbildningarna i Azure.

>[!NOTE]
> RHEL BYOS guld-avbildningar är tillgängliga i Azures offentliga (kommersiella) och Azure Government moln. De är inte tillgängliga i Azure Kina-eller Azure Blackforest-moln.

## <a name="important-points-to-consider"></a>Viktiga saker att tänka på

- De guld-avbildningar i Red Hat som tillhandahålls i det här programmet är produktions klara RHEL-avbildningar som liknar de RHEL som du betalar per användning i Azure Marketplace.
- Bilderna följer de aktuella principerna som beskrivs i [Red Hat Enterprise Linux avbildningar på Azure](./redhat-images.md).
- Standard Support policys gäller för virtuella datorer som skapats från de här avbildningarna.
- De virtuella datorer som tillhandahålls från Red Hat Gold-avbildningar har inte RHEL-avgifter som är kopplade till RHEL-avbildningar enligt principen betala per användning.
- Bilderna är inte berättigade. Du måste använda Red Hat-Subscription-Manager för att registrera och prenumerera på de virtuella datorerna för att få uppdateringar från Red Hat direkt.
- Det går att växla från avbildningar med betala per användning till BYOS med hjälp av [Azure Hybrid-förmån](../../linux/azure-hybrid-benefit-linux.md). Det är dock inte möjligt att växla från en ursprungligen distribuerad BYOS till fakturerings modeller enligt principen betala per användning för Linux-avbildningar. Om du vill byta fakturerings modell från BYOS till "betala per användning" måste du distribuera om den virtuella datorn från respektive avbildning.

>[!NOTE]
> Generation 2 RHEL BYOS-avbildningar är inte tillgängliga via Marketplace-erbjudandet. Om du behöver en generation 2 RHEL BYOS-avbildning kan du gå till instrument panelen för moln åtkomst i Red Hat-prenumerations hantering. Mer information finns i [Red Hat-dokumentationen](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Krav och villkor för åtkomst till Red Hat Gold-avbildningar

1. Bekanta dig med villkoren för [program med Red Hat Cloud Access-program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . Aktivera Red Hat-prenumerationer för moln åtkomst i [Red Hat-prenumeration – chef](https://access.redhat.com/management/cloud). Du måste ha i hand de Azure-prenumerationer som ska registreras för moln åtkomst.

1. Om de Red Hat-prenumerationer som du har aktiverat för moln åtkomst uppfyller behörighets kraven är dina Azure-prenumerationer automatiskt aktiverade för åtkomst till Gold-avbildning.

### <a name="expected-time-for-image-access"></a>Förväntad tid för bild åtkomst

När du har slutfört stegen för att aktivera moln åtkomst, validerar Red Hat din behörighet för de röda hat Gold-avbildningarna. Om verifieringen lyckas får du åtkomst till guld avbildningarna inom tre timmar.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Använd Red Hat Gold-avbildningarna från Azure Portal

1. När din Azure-prenumeration får åtkomst till de röda guld-avbildningarna kan du hitta dem i [Azure Portal](https://portal.azure.com). Gå till **skapa en resurs**  >  **Se alla**.

1. Längst upp på sidan ser du att du har privata erbjudanden.

    ![Privata erbjudanden för Marketplace](./media/rhel-byos-privateoffers.png)

1. Välj den lila länken eller bläddra längst ned på sidan för att se dina privata erbjudanden.

1. Resten av etableringen i användar gränssnittet är ingen annan befintlig Red Hat-bild. Välj din RHEL-version och följ anvisningarna för att etablera den virtuella datorn. Med den här processen kan du också acceptera villkoren för avbildningen i det sista steget.

>[!NOTE]
>Med de här stegen kan du inte aktivera Red Hat guld-avbildningen för programmerings distribution. Ett ytterligare steg krävs enligt beskrivningen i avsnittet "Ytterligare information".

Resten av det här dokumentet fokuserar på CLI-metoden för att etablera och godkänna villkoren på avbildningen. Användar gränssnittet och CLI är helt utbytbara så långt som det slutliga resultatet (en etablerad virtuell RHEL Gold image VM) berörs.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Använd Red Hat Gold-avbildningar från Azure CLI

Följande instruktioner vägleder dig genom den första distributions processen för en virtuell RHEL-dator med hjälp av Azure CLI. Dessa instruktioner förutsätter att du har [installerat Azure CLI](/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Se till att du använder alla små bokstäver i referenserna utgivare, erbjudande, plan och bild för alla följande kommandon.

1. Kontrol lera att du har den önskade prenumerationen.

    ```azurecli
    az account show -o=json
    ```

1. Skapa en resurs grupp för din Red Hat Gold-avbildning av virtuella datorer.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Godkänn avbildnings villkoren.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Dessa villkor måste godkännas *en gång per Azure-prenumeration, per avbildnings-SKU*.

1. Valfritt Verifiera distributionen av virtuella datorer med följande kommando:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Etablera den virtuella datorn genom att köra samma kommando som visas i föregående exempel utan `--validate` argumentet.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. SSH till den virtuella datorn och kontrol lera att du har en berättigad avbildning. Om du vill göra det här steget kör du `sudo yum repolist` . För RHEL 8 använder du `sudo dnf repolist` . Utdata uppmanar dig att använda Subscription-Manager för att registrera den virtuella datorn med Red Hat.

>[!NOTE]
>På RHEL 8 `dnf` och `yum` är utbytbara. Mer information finns i [Administratörs hand boken för RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Använd Red Hat Gold-avbildningar från PowerShell

Följande skript är ett exempel. Ersätt resurs gruppen, platsen, det virtuella dator namnet, inloggnings informationen och andra variabler med valfri konfiguration. Information om utgivare och plan måste vara gemener.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Kryptera Red Hat Enterprise Linux Hämta guld bilder i din egen prenumeration

Red Hat Enterprise Linux guld bilder i BYOS kan säkras genom användning av [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Prenumerationen *måste* registreras innan du kan aktivera kryptering. Mer information om hur du registrerar en RHEL BYOS Gold-avbildning finns i [så här registrerar och prenumererar du på ett system till Red Hat-kundportalen med Red Hat-prenumeration-Manager](https://access.redhat.com/solutions/253273). Om du har en aktiv Red Hat-prenumeration kan du också läsa [skapa Red Hat kund Portal aktiverings nycklar](https://access.redhat.com/articles/1378093).

Azure Disk Encryption stöds inte för [anpassade Red Hat-bilder](../../linux/redhat-create-upload-vhd.md). Ytterligare Azure Disk Encryption krav och nödvändiga komponenter finns dokumenterade i [Azure Disk Encryption för virtuella Linux-datorer](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Anvisningar för hur du använder Azure Disk Encryption finns [Azure Disk Encryption scenarier för virtuella Linux-datorer](../../linux/disk-encryption-linux.md) och relaterade artiklar.

## <a name="additional-information"></a>Ytterligare information

- Om du försöker etablera en virtuell dator för en prenumeration som inte är aktive rad för det här erbjudandet får du följande meddelande:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    I det här fallet kontaktar du Microsoft eller Red Hat för att aktivera din prenumeration.

- Om du ändrar en ögonblicks bild från en RHEL BYOS-avbildning och försöker publicera den anpassade avbildningen i det [delade bild galleriet](../../shared-image-galleries.md)måste du ange plan information som matchar den ursprungliga källan till ögonblicks bilden. Kommandot kan till exempel se ut så här:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Observera plan parametrarna på den sista raden.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) stöds inte för anpassade avbildningar.

- Om du använder Automation för att etablera virtuella datorer från RHEL BYOS-avbildningar måste du ange plan parametrar som liknar vad som visas i exempel kommandona. Om du till exempel använder terraform anger du plan informationen i ett [plan block](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Nästa steg

- Mer information om Red Hat Cloud Access finns i dokumentationen för det [offentliga molnet i Red Hat](https://access.redhat.com/public-cloud)
- Steg-för-steg-guider och program information för moln åtkomst finns i [dokumentationen till moln åtkomst för Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Mer information om Red Hats uppdaterings infrastruktur finns i [Azure Red Hat-uppdaterings infrastruktur](./redhat-rhui.md).
- Mer information om de Red Hat-avbildningarna i Azure finns på [dokumentations sidan](./redhat-images.md).
- Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .
- Mer dokumentation om RHEL Gold-avbildningar finns i [Red Hat-dokumentationen](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).