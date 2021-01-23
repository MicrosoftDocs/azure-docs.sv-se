---
title: Anslut en moln tjänst (klassisk) till en anpassad domänkontrollant | Microsoft Docs
description: Lär dig hur du ansluter dina webb-/Worker-roller till en anpassad AD-domän med PowerShell och AD-domän tillägg
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8c2c8377944caa7ad28f6b379531e6d5bf44c9e7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742513"
---
# <a name="connecting-azure-cloud-services-classic-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Ansluta Azure Cloud Services-roller (klassiska) till en anpassad AD-domänkontrollant som finns i Azure

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Vi konfigurerar först en Virtual Network (VNet) i Azure. Vi lägger sedan till en Active Directory-domän kontroll (som finns på en virtuell Azure-dator) i VNet. Nu ska vi lägga till befintliga Cloud Service-roller i det förskapade VNet-nätverket och sedan ansluta dem till domänkontrollanten.

Innan vi börjar kan du tänka på följande:

1. Den här självstudien använder PowerShell, så se till att du har Azure PowerShell installerat och är redo att sätta igång. Information om hur du konfigurerar Azure PowerShell finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).
2. AD-domänkontrollanten och webb-och arbets Rolls instanserna måste finnas i VNet.

Följ den här steg-för-steg-guiden och om du stöter på problem, lämna oss en kommentar i slutet av artikeln. Någon kommer tillbaka till dig (Ja, vi läser kommentarer).

Nätverket som moln tjänsten refererar till måste vara ett **klassiskt virtuellt nätverk**.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Du kan skapa en Virtual Network i Azure med hjälp av Azure Portal eller PowerShell. I den här självstudien används PowerShell. Information om hur du skapar ett virtuellt nätverk med hjälp av Azure Portal finns i [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md). Artikeln beskriver hur du skapar ett virtuellt nätverk (Resource Manager), men du måste skapa ett virtuellt nätverk (klassiskt) för Cloud Services. Det gör du genom att välja **skapa en resurs** i portalen, skriva ett *virtuellt nätverk* i **sökrutan och** sedan trycka på **RETUR**. I Sök resultaten under **allt** väljer du **virtuellt nätverk**. Välj **klassisk** under **Välj en distributions modell** och välj sedan **skapa**. Du kan sedan följa stegen i artikeln.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
När du har konfigurerat Virtual Network måste du skapa en AD-domänkontrollant. I den här självstudien kommer vi att konfigurera en AD-domänkontrollant på en virtuell Azure-dator.

Det gör du genom att skapa en virtuell dator via PowerShell med följande kommandon:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Flytta upp den virtuella datorn till en domänkontrollant
Om du vill konfigurera den virtuella datorn som en AD-domänkontrollant måste du logga in på den virtuella datorn och konfigurera den.

Om du vill logga in på den virtuella datorn kan du hämta RDP-filen via PowerShell, använda följande kommandon:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

När du har loggat in på den virtuella datorn konfigurerar du den virtuella datorn som en AD-domänkontrollant genom att följa steg-för-steg-guide om [hur du konfigurerar din kund AD](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)-domänkontrollant.

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Lägg till din moln tjänst i Virtual Network
Därefter måste du lägga till moln tjänst distributionen till det nya virtuella nätverket. Det gör du genom att ändra din moln tjänst cscfg genom att lägga till relevanta avsnitt i din cscfg med Visual Studio eller valfritt redigerings program.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Därefter skapar du ett Cloud Services-projekt och distribuerar det till Azure. Information om hur du distribuerar ditt Cloud Services-paket till Azure finns i [så här skapar och distribuerar du en moln tjänst](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Anslut dina webb-/arbets roller till domänen
När du har distribuerat ditt Cloud Service-projekt på Azure ansluter du roll instanserna till den anpassade AD-domänen med hjälp av AD-domän tillägget. Om du vill lägga till AD-domännamnet i din befintliga Cloud Services-distribution och ansluta till den anpassade domänen kör du följande kommandon i PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Och sedan är du klar.

Dina moln tjänster bör vara anslutna till den anpassade domänkontrollanten. Om du vill veta mer om de olika alternativen som är tillgängliga för hur du konfigurerar AD-domännamn, använder du PowerShell-hjälpen. Några exempel är följande:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



