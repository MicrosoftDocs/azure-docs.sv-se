---
title: Konfigurera DHCPv6 för virtuella Linux-datorer
titleSuffix: Azure Load Balancer
description: I den här artikeln lär du dig hur du konfigurerar DHCPv6 för virtuella Linux-datorer.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, dubbel stack, offentlig IP, inbyggd IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006754"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurera DHCPv6 för virtuella Linux-datorer


Några av Linux-avbildningarna för virtuella datorer på Azure Marketplace har inte Dynamic Host Configuration Protocol version 6 (DHCPv6) som standard. DHCPv6 måste konfigureras i den Linux OS-distribution som du använder för att stödja IPv6. De olika Linux-distributionerna konfigurerar DHCPv6 på flera olika sätt eftersom de använder olika paket.

> [!NOTE]
> De senaste SUSE Linux-och Core-avbildningarna i Azure Marketplace har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder de här avbildningarna.

I det här dokumentet beskrivs hur du aktiverar DHCPv6 så att din virtuella Linux-dator erhåller en IPv6-adress.

> [!WARNING]
> Genom att felaktigt redigera filer för nätverks konfiguration kan du förlora nätverks åtkomsten till den virtuella datorn. Vi rekommenderar att du testar dina konfigurations ändringar på andra datorer än produktions system. Anvisningarna i den här artikeln har testats i de senaste versionerna av Linux-avbildningarna på Azure Marketplace. Mer detaljerad information finns i dokumentationen för din egen version av Linux.

## <a name="ubuntu"></a>Ubuntu

1. Redigera */etc/DHCP/dhclient6.conf* -filen och Lägg till följande rad:

    ```config
    timeout 10;
    ```

2. Redigera nätverks konfigurationen för eth0-gränssnittet med följande konfiguration:

   * Redigera */etc/network/interfaces.d/eth0.cfg* -filen på **Ubuntu 12,04 och 14,04**. 
   * Redigera */etc/network/interfaces.d/50-Cloud-init.cfg* -filen på **Ubuntu 16,04**.

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

Från och med Ubuntu 17,10 är standard mekanismen för nätverks konfiguration [netplan]( https://netplan.io).  Vid installation/instansiering kan netplan läsa nätverks konfiguration från YAML konfigurationsfiler på den här platsen:/{lib, etc, kör}/netplan/*. yaml.

Inkludera en *dhcp6: true* -instruktion för varje Ethernet-gränssnitt i konfigurationen.  Exempel:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

Under tidig start skriver netplan "Network renderare"-konfigurationen till/Run för att kunna lämna kontroll över enheter till den angivna Network daemonen för att få referensinformation om netplan, se https://netplan.io/reference .
 
## <a name="debian"></a>Debian

1. Redigera */etc/DHCP/dhclient6.conf* -filen och Lägg till följande rad:

    ```config
    timeout 10;
    ```

2. Redigera */etc/network/interfaces* -filen och Lägg till följande konfiguration:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS och Oracle Linux

1. Redigera filen */etc/sysconfig/Network* och Lägg till följande parameter:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Redigera */etc/sysconfig/Network-scripts/ifcfg-eth0* -filen och Lägg till följande två parametrar:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 och openSUSE 13

De senaste SUSE Linux Enterprise Server (SLES) och openSUSE-avbildningarna i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder de här avbildningarna. Om du har en virtuell dator som baseras på en äldre eller anpassad SUSE-avbildning gör du följande:

1. Installera `dhcp-client` paketet om det behövs:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Redigera filen */etc/sysconfig/Network/ifcfg-eth0* och Lägg till följande parameter:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12-och openSUSE-hopp

De senaste SLES-och openSUSE-avbildningarna i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder de här avbildningarna. Om du har en virtuell dator som baseras på en äldre eller anpassad SUSE-avbildning gör du följande:

1. Redigera */etc/sysconfig/Network/ifcfg-eth0* -filen och Ersätt `#BOOTPROTO='dhcp4'` parametern med följande värde:

    ```config
    BOOTPROTO='dhcp'
    ```

2. Lägg till följande parameter i */etc/sysconfig/Network/ifcfg-eth0* -filen:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

De senaste Core-avbildningarna i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder de här avbildningarna. Om du har en virtuell dator som baseras på en äldre eller anpassad Core-avbildning gör du följande:

1. Redigera */etc/systemd/network/10_dhcp. Network* -fil:

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Förnya IPv6-adressen:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
