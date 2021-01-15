---
title: Skapa en virtuell Azure-dator med accelererat nätverk med Azure CLI
description: Lär dig hur du skapar en virtuell Linux-dator med accelererat nätverk aktiverat.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 5b91d6e58f4ae93bbf020f202991f878e7773114
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222963"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Skapa en virtuell Linux-dator med accelererat nätverk med Azure CLI

I den här självstudien får du lära dig hur du skapar en virtuell Linux-dator (VM) med accelererat nätverk. Information om hur du skapar en virtuell Windows-dator med accelererat nätverk finns i [skapa en virtuell Windows-dator med accelererat nätverk](create-vm-accelerated-networking-powershell.md). Accelererat nätverk möjliggör SR-IOV (Single root I/O Virtualization) till en virtuell dator, vilket avsevärt förbättrar nätverkets prestanda. Den här högpresterande sökvägen kringgår värden från Datapath, minskar svars tiden, skakningarna och CPU-användningen, för användning med de mest krävande nätverks belastningarna på VM-typer som stöds. Följande bild visar kommunikationen mellan två virtuella datorer med och utan accelererat nätverk:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan accelererat nätverk måste all nätverks trafik i och från den virtuella datorn passera värden och den virtuella växeln. Den virtuella växeln tillhandahåller all princip tillämpning, till exempel nätverks säkerhets grupper, åtkomst kontrol listor, isolering och andra virtualiserade nätverks tjänster till nätverks trafik. Läs mer om virtuella växlar i artikeln om [Hyper-V-nätverksvirtualisering och virtuell växel](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134230(v=ws.11)) .

Med accelererat nätverk anländer nätverks trafiken till den virtuella datorns nätverks gränssnitt (NIC) och vidarebefordras sedan till den virtuella datorn. Alla nätverks principer som den virtuella växeln gäller avlastas nu och används i maskin vara. Genom att tillämpa principen i maskin vara kan NÄTVERKSKORTet vidarebefordra nätverks trafik direkt till den virtuella datorn, kringgå värden och den virtuella växeln, samtidigt som all princip som används i värden upprätthålls.

Fördelarna med accelererade nätverk gäller endast den virtuella dator som den är aktive rad på. För bästa resultat är det idealiskt att aktivera den här funktionen på minst två virtuella datorer som är anslutna till samma virtuella Azure-nätverk (VNet). När du kommunicerar via virtuella nätverk eller ansluter lokalt har den här funktionen minimal påverkan på den totala svars tiden.

## <a name="benefits"></a>Fördelar
* **Lägre latens/högre paket per sekund (PPS):** Att ta bort den virtuella växeln från Datapath tar bort de tid paket som ägnas åt värden för princip bearbetning och ökar antalet paket som kan bearbetas inuti den virtuella datorn.
* **Reducerade Darr:** Bearbetning av virtuella växlar beror på den mängd princip som måste tillämpas och arbets belastningen för den processor som utför bearbetningen. Genom att avlasta den tvingande principen till maskin varan tar du bort den variabiliteten genom att leverera paket direkt till den virtuella datorn, ta bort värden till kommunikation mellan virtuella datorer och alla program avbrott och kontext byten.
* **Minskad processor användning:** Om den virtuella växeln kringgås i värden går det snabbare att bearbeta nätverks trafiken.

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds i rutan från Azure-galleriet: 
* **Ubuntu 14,04 med Linux-Azure-kernel**
* **Ubuntu 16,04 eller senare** 
* **SLES12 SP3 eller senare** 
* **RHEL 7,4 eller senare**
* **CentOS 7,4 eller senare**
* **CoreOS Linux**
* **Debian "Sträck ut" med backports-kernel, Debian "Buster" eller senare**
* **Oracle Linux 7,4 och senare med Red Hat-kompatibel kernel (RHCK)**
* **Oracle Linux 7,5 och senare med UEK version 5**
* **FreeBSD 10,4, 11,1 & 12,0 eller senare**

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

### <a name="supported-vm-instances"></a>Virtuella dator instanser som stöds
Accelererat nätverk stöds i de flesta generella syftes-och beräknings optimerade instans storlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/FS

På instanser som stöder hyperthreading stöds accelererat nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien som stöds är: D/Dsv3, D/Dsv4, DD/Ddv4, da/Dasv4, E/Esv3, E/Esv4, Ed/Edsv4, EA/Easv4, Fsv2, Lsv2, MS/MMS och MS/Mmsv2.

Mer information om VM-instanser finns i [storlekar för virtuella Linux-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Anpassade avbildningar
Om du använder en anpassad avbildning, och din avbildning har stöd för accelererat nätverk, måste du se till att de nödvändiga driv rutinerna fungerar med Mellanox ConnectX-3-och ConnectX-4 LX-nätverkskort på Azure.

### <a name="regions"></a>Regioner
Tillgängligt i alla offentliga Azure-regioner och Azure Government moln.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivera accelererat nätverk på en virtuell dator som körs
En virtuell dator storlek som stöds utan accelererat nätverk kan bara aktivera funktionen när den stoppas och frigörs.  
### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager
Virtuella datorer (klassisk) kan inte distribueras med accelererat nätverk.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Skapa en virtuell Linux-dator med Azure-accelererat nätverk
## <a name="portal-creation"></a>Skapa Portal
Även om den här artikeln innehåller steg för att skapa en virtuell dator med accelererat nätverk med hjälp av Azure CLI, kan du också [skapa en virtuell dator med accelererat nätverk med hjälp av Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du skapar en virtuell dator i portalen väljer du fliken **nätverk** i bladet **skapa en virtuell dator** .  På den här fliken finns ett alternativ för **accelererat nätverk**.  Om du har valt ett [operativ system som stöds](#supported-operating-systems) och storleken på den [virtuella datorn](#supported-vm-instances), fylls det här alternativet i automatiskt på "på".  Om inte, kommer den att fylla i alternativet "av" för accelererat nätverk och ge användaren en anledning till varför det inte är aktiverat.   

* *Obs:* Endast operativ system som stöds kan aktive ras via portalen.  Om du använder en anpassad avbildning och din avbildning har stöd för accelererat nätverk skapar du en virtuell dator med CLI eller PowerShell. 

När den virtuella datorn har skapats kan du bekräfta att accelererat nätverk har Aktiver ATS genom att följa anvisningarna i [kontrol lera att accelererat nätverk är aktiverat](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Skapa CLI
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) och logga in på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index). Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn som ingår *myResourceGroup*, *myNic* och *myVm*.

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för den *centrala* platsen:

```azurecli
az group create --name myResourceGroup --location centralus
```

Välj en Linux-region som stöds i [Linux-accelererat nätverk](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med ett undernät:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Nätverks säkerhets gruppen innehåller flera standard regler, varav en inaktiverar all inkommande åtkomst från Internet. Öppna en port för att tillåta SSH-åtkomst till den virtuella datorn med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverks gränssnitt med accelererat nätverk

Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). En offentlig IP-adress krävs inte om du inte planerar åtkomst till den virtuella datorn från Internet, men du måste utföra stegen i den här artikeln.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Skapa ett nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic) med accelererat nätverk aktiverat. I följande exempel skapas ett nätverks gränssnitt med namnet *myNic* i *under nätet undernät för* det virtuella *myVnet* -nätverket och associerar *myNetworkSecurityGroup* nätverks säkerhets grupp till nätverks gränssnittet:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Skapa en virtuell dator och koppla NÄTVERKSKORTet
När du skapar den virtuella datorn anger du det nätverkskort som du skapade med `--nics` . Välj en storlek och distribution som visas i ett [snabbare nätverk i Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM* med UbuntuLTS-avbildningen och en storlek som har stöd för accelererat nätverk (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

En lista över alla storlekar och egenskaper för virtuella datorer finns i [storlekar för virtuella Linux-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

När den virtuella datorn har skapats returneras utdata som liknar följande exempel utdata. Anteckna **publicIpAddress**. Den här adressen används för att få åtkomst till den virtuella datorn i efterföljande steg.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Bekräfta att accelererat nätverk har Aktiver ATS

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt `<your-public-ip-address>` med den offentliga IP-adress som tilldelats den virtuella datorn som du skapade och Ersätt *azureuser* om du använde ett annat värde för `--admin-username` när du skapade den virtuella datorn.

```bash
ssh azureuser@<your-public-ip-address>
```

Från bash-gränssnittet anger `uname -r` och bekräftar du att kernel-versionen är en av följande versioner eller större:

* **Ubuntu 16,04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Bekräfta att Mellanox VF-enheten exponeras för den virtuella datorn med `lspci` kommandot. De utdata som returneras liknar följande utdata:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sök efter aktivitet på VF (virtuell funktion) med `ethtool -S eth0 | grep vf_` kommandot. Om du får utdata som liknar följande exempel på utdata är accelererat nätverk aktiverat och fungerar.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Accelererat nätverk har nu Aktiver ATS för den virtuella datorn.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Hantera dynamisk bindning och återkallande av virtuell funktion 
Programmen måste köras via det syntetiska NÄTVERKSKORTet som exponeras i den virtuella datorn. Om programmet körs direkt över VF-NÄTVERKSKORTet får det inte **alla** paket som är avsedda för den virtuella datorn, eftersom vissa paket visas över det syntetiska gränssnittet.
Om du kör ett program över det syntetiska NÄTVERKSKORTet garanterar det att programmet tar emot **alla** paket som är avsedda för det. Det ser också till att programmet fortsätter att köras, även om VF-objektet återkallas när värden betjänas. Program bindningar till det syntetiska NÄTVERKSKORTet är ett **obligatoriskt** krav för alla program som drar nytta av **accelererat nätverk**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera accelererat nätverk på befintliga virtuella datorer
Om du har skapat en virtuell dator utan accelererat nätverk är det möjligt att aktivera den här funktionen på en befintlig virtuell dator.  Den virtuella datorn måste ha stöd för accelererat nätverk genom att uppfylla följande krav som också beskrivs ovan:

* Den virtuella datorn måste ha en storlek som stöds för accelererat nätverk
* Den virtuella datorn måste vara en Azure Gallery-avbildning som stöds (och kernel-version för Linux)
* Alla virtuella datorer i en tillgänglighets uppsättning eller VMSS måste stoppas/frigöras innan du aktiverar accelererat nätverk på något nätverkskort

### <a name="individual-vms--vms-in-an-availability-set"></a>Enskilda virtuella datorer & virtuella datorer i en tillgänglighets uppsättning
Först stoppa/frigör den virtuella datorn eller, om det finns en tillgänglighets uppsättning, alla virtuella datorer i uppsättningen:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Viktigt, Observera att om din virtuella dator har skapats individuellt, utan en tillgänglighets uppsättning, behöver du bara stoppa/frigöra den enskilda virtuella datorn för att aktivera accelererat nätverk.  Om den virtuella datorn skapades med en tillgänglighets uppsättning måste alla virtuella datorer som finns i tillgänglighets uppsättningen stoppas/frigörs innan du aktiverar accelererat nätverk på något av nätverkskorten. 

När du har stoppat aktiverar du accelererat nätverk på den virtuella datorns nätverkskort:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Starta om den virtuella datorn eller, om den finns i en tillgänglighets uppsättning, alla virtuella datorer i uppsättningen och bekräfta att accelererat nätverk är aktiverat: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS skiljer sig något åt, men följer samma arbets flöde.  Stoppa först de virtuella datorerna:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

När de virtuella datorerna har stoppats uppdaterar du egenskapen för accelererat nätverk under nätverks gränssnittet:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Obs! en VMSS har uppgraderingar av virtuella datorer som tillämpar uppdateringar med tre olika inställningar, automatiska, rullande och manuella.  I dessa instruktioner är principen inställd på automatisk så att VMSS kommer att hämta ändringarna direkt efter omstarten.  Så här ställer du in det på automatiskt så att ändringarna omedelbart hämtas: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Starta slutligen om VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

När du har startat om väntar du tills uppgraderingarna har slutförts, men då visas VF i den virtuella datorn.  (Kontrol lera att du använder ett OS-och VM-storlek som stöds.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med accelererat nätverk

Virtuella datorer med accelererat nätverk aktiverat kan bara ändras till virtuella datorer som har stöd för accelererat nätverk.  

Det går inte att ändra storlek på en virtuell dator med accelererat nätverk till en VM-instans som inte stöder accelererat nätverk med åtgärden ändra storlek.  I stället kan du ändra storlek på en av de virtuella datorerna: 

* Stoppa/frigör den virtuella datorn eller om den finns i en tillgänglighets uppsättning/VMSS, stoppa/frigör alla virtuella datorer i uppsättningen/VMSS.
* Accelererat nätverk måste vara inaktiverat på NÄTVERKSKORTet för den virtuella datorn eller, om det finns en tillgänglighets uppsättning/VMSS, alla virtuella datorer i uppsättningen/VMSS.
* När det accelererade nätverket har inaktiverats kan VM/tillgänglighets uppsättningen/VMSS flyttas till en ny storlek som inte har stöd för accelererat nätverk och startas om.