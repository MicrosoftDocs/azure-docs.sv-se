---
title: Så här aktiverar du kapslad virtualisering i Azure Virtual Machines
description: Så här aktiverar du kapslad virtualisering i Azure Virtual Machines
author: cynthn
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: how-to
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure
ms.openlocfilehash: 3988d0ed01e5aa75f498f17f9bbd23e567cb9c07
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556306"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Så här aktiverar du kapslad virtualisering i en virtuell Azure-dator

Kapslad virtualisering stöds i flera virtuella Azure-datorer. Den här funktionen ger bra flexibilitet i stöd scenarier som utveckling, testning, utbildning och demonstrations miljöer.   

Den här artikeln beskriver hur du aktiverar Hyper-V på en virtuell Azure-dator och konfigurerar Internet anslutning till den virtuella gäst datorn.

## <a name="create-a-nesting-capable-azure-vm"></a>Skapa en kapslad Azure VM-kompatibel dator

Skapa en ny Windows Server 2016 eller Windows Server 2019 Azure VM för värden. Se till att du väljer en storlek för den virtuella datorn som stöder kapsling och är tillräckligt stor för att uppfylla kraven för de virtuella gäst datorerna. En lista över storlekar för virtuella datorer som stöder kapsling finns i artikeln om [Azure Compute Unit](../acu.md) .

Du kan visa den regionala tillgängligheten för VM-storlekar på sidan [produkter som är tillgängliga per region](https://azure.microsoft.com/regions/services/) .

>[!NOTE]
>
>Detaljerade anvisningar om hur du skapar en ny virtuell dator finns i [skapa och hantera virtuella Windows-datorer med modulen Azure PowerShell](./tutorial-manage-vm.md)
    
## <a name="connect-to-your-azure-vm"></a>Anslut till din virtuella Azure-dator

Skapa en fjärrskrivbordsanslutning till den virtuella datorn.

1. Klicka på knappen **Anslut** på den virtuella datorns egenskaper. En protokollfil för fjärrskrivbord (.rdp-fil) skapas och hämtas.

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) från Mac App Store.

3. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Aktivera Hyper-V-funktionen på den virtuella Azure-datorn
Du kan konfigurera dessa inställningar manuellt eller så har vi angett ett PowerShell-skript för att automatisera konfigurationen.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Alternativ 1: Använd ett PowerShell-skript för att konfigurera kapslad virtualisering
Ett PowerShell-skript för att aktivera kapslad virtualisering på en Windows Server 2016-värd finns på [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skriptet kontrollerar krav och konfigurerar sedan kapslad virtualisering på den virtuella Azure-datorn. Det krävs en omstart av den virtuella Azure-datorn för att slutföra konfigurationen. Det här skriptet kan fungera i andra miljöer men är inte garanterat. Kolla in Azures blogg inlägg med en video demonstration om kapslad virtualisering som körs på Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Alternativ 2: Konfigurera kapslad virtualisering manuellt

1. Öppna PowerShell som administratör på den virtuella Azure-datorn. 

2. Aktivera Hyper-V-funktionen och hanterings verktyg.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Det här kommandot startar om den virtuella Azure-datorn. Du kommer att förlora RDP-anslutningen under omstarten.
    
3. När den virtuella Azure-datorn har startats om ansluter du till den virtuella datorn med RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Konfigurera Internet anslutning för den virtuella gäst datorn
Skapa ett nytt virtuellt nätverkskort för den virtuella gäst datorn och konfigurera en NAT-gateway för att aktivera Internet anslutning.

### <a name="create-a-nat-virtual-network-switch"></a>Skapa en virtuell nätverks växel för NAT

1. Öppna PowerShell som administratör på den virtuella Azure-datorn.
   
2. Skapa en intern växel.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Visa egenskaperna för växeln och notera ifIndex för det nya kortet.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Anteckna "ifIndex" för den virtuella växel som du nyss skapade.
    
4. Skapa en IP-adress för NAT-gatewayen.
    
Du behöver viss information om ditt nätverk för att konfigurera gatewayen:    
  * IP-adress – NAT-gatewayens IP-adress anger IPv4-eller IPv6-adressen som ska användas som standard-gateway-adress för det virtuella nätverkets undernät. Det generiska formuläret är a. b. c. 1 (till exempel "192.168.0.1"). Den sista positionen behöver inte vara 1. det är vanligt vis (baserat på prefixlängd). Normalt använder du ett RFC 1918 privat nätverks adress utrymme. 
  * PrefixLength – Undernätsprefixets längd definierar den lokala nät maskens storlek (nätmask). Undernätsprefixets längd är ett heltals värde mellan 0 och 32. 0 mappar hela Internet. 32 bara tillåta en mappad IP. Vanliga värden är mellan 24 och 12, beroende på hur många IP-adresser som måste kopplas till NAT. En vanlig PrefixLength är 24 – det här är en under näts mask på 255.255.255.0.
  * InterfaceIndex – **ifIndex** är gränssnitts indexet för den virtuella växel som skapades i föregående steg. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Skapa NAT-nätverket

För att du ska kunna konfigurera gatewayen måste du ange information om nätverket och NAT-gatewayen:
  * Namn – det här är namnet på NAT-nätverket. 
  * InternalIPInterfaceAddressPrefix – prefixet NAT-undernät beskriver både NAT-gatewayens IP-prefix från ovan, samt längden på NAT-undernätet. Det generiska formuläret är en. b. c. c. 0/NAT-Undernätsprefixets längd. 

Skapa ett nytt NAT-nätverk i PowerShell.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Skapa den virtuella gäst datorn

>[!IMPORTANT] 
>
>Azures gästa Gent stöds inte på kapslade virtuella datorer och kan orsaka problem på både värden och de kapslade virtuella datorerna. Installera inte Azure-agenten på kapslade virtuella datorer och Använd inte en avbildning för att skapa de kapslade virtuella datorerna där Azures gästa Gent redan är installerad.

1. Öppna Hyper-V Manager och skapa en ny virtuell dator. Konfigurera den virtuella datorn så att den använder det nya interna nätverket som du skapade.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installera ett operativ system på den virtuella gäst datorn.
    
    >[!NOTE] 
    >
    >Du behöver installationsmedia för att operativ systemet ska installeras på den virtuella datorn. I det här fallet använder vi Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Tilldela en IP-adress till den virtuella gäst datorn

Du kan tilldela en IP-adress till den virtuella gäst datorn antingen genom att manuellt ange en statisk IP-adress på den virtuella gäst datorn eller konfigurera DHCP på den virtuella Azure-datorn för att tilldela IP-adressen dynamiskt.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Alternativ 1: Konfigurera DHCP för att dynamiskt tilldela en IP-adress till den virtuella gäst datorn
Följ stegen nedan för att konfigurera DHCP på den virtuella värd datorn för dynamisk adress tilldelning.

#### <a name="install-dhcp-server-on-the-azure-vm"></a>Installera DHCP-server på den virtuella Azure-datorn

1. Öppna Serverhanteraren. Klicka på **Lägg till roller och funktioner** på instrument panelen. Guiden Lägg till roller och funktioner visas.
  
2. Klicka på **Nästa** på sidan Server roller i guiden.
  
3. Klicka för att markera kryss rutan **DHCP-server** , klicka på **Lägg till funktioner** och klicka sedan på **Nästa** tills du har slutfört guiden.
  
4. Klicka på **Installera**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurera ett nytt DHCP-omfång

1. Öppna DHCP-hanteraren.
  
2. I navigerings fönstret expanderar du Server namnet, högerklickar på **IPv4** och klickar på **nytt omfång**. Guiden Nytt scope visas, klicka på **Nästa**.
  
3. Ange ett namn och en beskrivning för omfattningen och klicka på **Nästa**.
  
4. Definiera ett IP-intervall för DHCP-servern (till exempel 192.168.0.100 till 192.168.0.200).
  
5. Klicka på **Nästa** tills sidan standardgateway visas. Ange den IP-adress som du skapade tidigare (till exempel 192.168.0.1) som standard-gateway och klicka sedan på **Lägg till**.
  
6. Klicka på **Nästa** tills guiden har slutförts, lämna alla standardvärden och klicka sedan på **Slutför**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Alternativ 2: Ange en statisk IP-adress manuellt på den virtuella gäst datorn
Om du inte konfigurerade DHCP för att dynamiskt tilldela en IP-adress till den virtuella gäst datorn följer du dessa steg för att ange en statisk IP-adress.

1. Öppna PowerShell som administratör på den virtuella Azure-datorn.

2. Högerklicka på den virtuella gäst datorn och klicka på Anslut.

3. Logga in på den virtuella gäst datorn.

4. Öppna Nätverks-och delnings Center på den virtuella gäst datorn.

5. Konfigurera nätverkskortet för en adress inom intervallet för det NAT-nätverk som du skapade i föregående avsnitt.

I det här exemplet ska du använda en adress i intervallet 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testa anslutningen på den virtuella gäst datorn

Öppna webbläsaren i den virtuella gäst datorn och navigera till en webb sida.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

Instruktioner för hur du aktiverar transparent anslutning mellan virtuella gäst datorer och virtuella Azure-datorer finns i [det här dokumentet](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).
