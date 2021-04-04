---
title: Fel sökning av problem med virtuella nätverks installationer i Azure | Microsoft Docs
description: Felsök NVA-problem (Network Virtual installation) i Azure och verifiera grundläggande krav för Azure-plattformen för NVA-konfigurationer.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: fe4c17b74cd786d03bd19257dea190a21ecaa9f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095666"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problem med virtuella nätverksinstallationer i Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det kan uppstå problem med virtuella datorer eller VPN-anslutningar och fel vid användning av en virtuell nätverks installation (NVA) från tredje part i Microsoft Azure. Den här artikeln innehåller grundläggande steg som hjälper dig att verifiera grundläggande krav på Azure-plattformen för NVA-konfigurationer.

Teknisk support för NVA från tredje part och deras integrering med Azure-plattformen tillhandahålls av NVA-leverantören.

> [!NOTE]
> Om du har en anslutning eller ett problem med routning som omfattar en NVA bör du [kontakta leverantören av NVA](https://mskb.pkisolutions.com/kb/2984655) direkt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Check lista för fel sökning med NVA-leverantör

- Program uppdateringar för NVA VM-programvara
- Konfiguration och funktioner för tjänst konto
- Användardefinierade vägar (UDR) i undernät för virtuella nätverk som dirigerar trafik till NVA
- UDR i undernät för virtuella nätverk som dirigerar trafik från NVA
- Vägvals tabeller och regler inom NVA (till exempel från NIC1 till NIC2)
- Spårning på NVA nätverkskort för att verifiera att ta emot och skicka nätverks trafik
- När du använder en standard-SKU och offentliga IP-adresser måste du skapa en NSG och en explicit regel för att tillåta att trafiken dirigeras till NVA.

## <a name="basic-troubleshooting-steps"></a>Grundläggande fel söknings steg

- Kontrol lera den grundläggande konfigurationen
- Kontrol lera NVA-prestanda
- Avancerad nätverks fel sökning

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Kontrol lera minimi kraven för konfiguration för NVA på Azure

Varje NVA har grundläggande konfigurations krav för att fungera korrekt på Azure. Följande avsnitt innehåller anvisningar för att kontrol lera de grundläggande konfigurationerna. [Kontakta leverantören av NVA](https://mskb.pkisolutions.com/kb/2984655)om du vill ha mer information.

**Kontrol lera om IP-vidarebefordring är aktiverat på NVA**

Använda Azure-portalen

1. Leta upp resursen NVA i [Azure Portal](https://portal.azure.com), Välj nätverk och välj sedan nätverks gränssnittet.
2. På sidan nätverks gränssnitt väljer du IP-konfiguration.
3. Kontrol lera att IP-vidarebefordring är aktiverat.

Använd PowerShell

1. Öppna PowerShell och logga sedan in på ditt Azure-konto.
2. Kör följande kommando (ersätt värdena för hakparenteser med din information):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Kontrol lera egenskapen **EnableIPForwarding** .
4. Om IP-vidarebefordring inte är aktive rad kör du följande kommandon för att aktivera det:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Sök efter NSG när du använder standard-SKU Pubilc-IP** När du använder en standard-SKU och offentliga IP-adresser måste en NSG skapas och en explicit regel för att tillåta trafik till NVA.

**Kontrol lera om trafiken kan dirigeras till NVA**

1. Öppna **Network Watcher** på [Azure Portal](https://portal.azure.com), Välj **nästa hopp**.
2. Ange en virtuell dator som är konfigurerad för att omdirigera trafiken till NVA och en mål-IP-adress för att visa nästa hopp. 
3. Om NVA inte visas som **nästa hopp**, kontrollerar du och uppdaterar Azure Route-tabellerna.

**Kontrol lera om trafiken kan komma åt NVA**

1. I [Azure Portal](https://portal.azure.com)öppnar du **Network Watcher** och väljer sedan **kontrol lera IP-flöde**. 
2. Ange den virtuella datorn och IP-adressen för NVA och kontrol lera sedan om trafiken blockeras av några nätverks säkerhets grupper (NSG).
3. Om det finns en NSG-regel som blockerar trafiken, letar du upp NSG i **gällande säkerhets** regler och uppdaterar den sedan så att trafiken kan passera. Kör sedan **IP-flöde verifiera** igen och Använd **anslutnings fel sökning** för att testa TCP-kommunikation från virtuell dator till din interna eller externa IP-adress.

**Kontrol lera om NVA och virtuella datorer lyssnar efter förväntad trafik**

1. Anslut till NVA med hjälp av RDP eller SSH och kör sedan följande kommando:

    För Windows:

    ```console
   netstat -an
    ```

    För Linux:

    ```console
   netstat -an | grep -i listen
    ```
2. Om du inte ser TCP-porten som används av NVA-programvaran som visas i resultatet måste du konfigurera programmet på NVA och den virtuella datorn för att lyssna och reagera på trafik som når dessa portar. [Kontakta NVA-leverantören om du behöver hjälp](https://mskb.pkisolutions.com/kb/2984655).

## <a name="check-nva-performance"></a>Kontrol lera NVA-prestanda

### <a name="validate-vm-cpu"></a>Verifiera VM-CPU

Om CPU-användningen går nära 100 procent kan det uppstå problem som påverkar nätverks paketets droppar. Din virtuella dator rapporterar Genomsnittlig CPU för ett särskilt tidsintervall i Azure Portal. Under en processor ökning bör du undersöka vilken process på den virtuella gäst datorn som orsakar den höga processorn och minimera den, om möjligt. Du kan också behöva ändra storlek på den virtuella datorn till en större SKU-storlek eller, om du använder skalnings uppsättning för virtuell dator, öka antalet instanser eller ange värdet Auto Scale för CPU-användning. För något av dessa problem kan [du kontakta NVA-leverantören om du behöver hjälp](https://mskb.pkisolutions.com/kb/2984655).

### <a name="validate-vm-network-statistics"></a>Verifiera statistik för virtuella dator nätverk

Om det virtuella dator nätverket använder toppar eller visar perioder med hög användning, kan du också behöva öka SKU-storleken på den virtuella datorn för att få högre genomflöde-funktioner. Du kan också distribuera om den virtuella datorn med accelererat nätverk aktiverat. Om du vill kontrol lera om NVA stöder accelererad nätverksfunktioner [kontaktar du NVA-leverantören om](https://mskb.pkisolutions.com/kb/2984655)du behöver hjälp.

## <a name="advanced-network-administrator-troubleshooting"></a>Avancerad nätverks administratör fel sökning

### <a name="capture-network-trace"></a>Avbilda nätverks spårning
Avbilda en samtidig nätverks spårning på den virtuella käll datorn, NVA och den virtuella mål datorn när du kör **[PsPing](/sysinternals/downloads/psping)** eller **nmap**, och stoppa sedan spårningen.

1. Om du vill avbilda en samtidig nätverks spårning kör du följande kommando:

   **För Windows**

   Netsh trace start Capture = Yes tracefile = c:\ server_IP. etl scenario = NetConnection

   **För Linux**

   sudo tcpdump-S0-i eth0-X-w vmtrace. Cap

2. Använd **PsPing** eller **nmap** från den virtuella käll datorn till den virtuella mål datorn (till exempel: `PsPing 10.0.0.4:80` eller `Nmap -p 80 10.0.0.4` ).
3. Öppna Nätverks spårningen från den virtuella mål datorn med hjälp av [Network Monitor](https://download.cnet.com/s/network-monitor) eller tcpdump. Använd ett visnings filter för IP-adressen för den virtuella käll datorn som du körde **PsPing** eller **nmap** från, till exempel `IPv4.address==10.0.0.4 (Windows netmon)` eller `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analysera spår

Om du inte ser inkommande paket till spårningen på den virtuella datorn på den virtuella datorn, finns det troligen ett NSG-eller UDR som stör eller NVA vägvals tabeller är felaktiga.

Om du ser att paket, men inget svar, kommer in kan du behöva åtgärda problem med ett program på den virtuella datorn eller med brandväggen. För något av dessa problem kan [du kontakta NVA-leverantören om du behöver hjälp](https://mskb.pkisolutions.com/kb/2984655).