---
title: ta med fil
description: ta med fil
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 93caf39216ef0479ec2799267a9ba8181f37f802
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84194197"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Lägg till IP-adresser till ett VM-operativsystem

Anslut och logga in på en virtuell dator som du har skapat med flera privata IP-adresser. Du måste manuellt lägga till alla de privata IP-adresser (inklusive det primära) som du lade till den virtuella datorn. Slutför stegen som följer för ditt virtuella dator operativ system.

### <a name="windows-server"></a>Windows Server
<details>
  <summary>Visa</summary>

1. Från en kommandotolk skriver du *ipconfig /all*.  Du ser bara den *primära* privata IP-adressen (via DHCP).
2. Skriv *ncpa.cpl* i kommandotolken för att öppna fönstret **Nätverksanslutningar**.
3. Öppna egenskaperna för lämpligt kort: **Ethernet**.
4. Dubbelklicka på Internet Protocol version 4 (IPv4).
5. Välj **Använd följande IP-adress** och ange följande värden:

    * **IP-adress**: Ange den *primära* privata IP-adressen
    * **Nätmask**: Ställ in beroende på ditt undernät. Om undernätet till exempel är ett /24-undernät så är nätmasken 255.255.255.0.
    * **Standard-gateway**: Den första IP-adressen i undernätet. Om undernätet är 10.0.0.0/24 så är gateway-IP-adressen 10.0.0.1.
    * Välj **Använd följande DNS-serveradresser** och ange följande värden:
        * **Önskad DNS-server**: Ange 168.63.129.16 om du inte använder en egen DNS-server.  Om du använder en egen DNS-server måste du ange dess IP-adress.  (För alternativ DNS-server kan du välja vilken offentlig DNS-serveradress som helst.)
    * Välj knappen **Avancerat** och Lägg till ytterligare IP-adresser. Lägg till var och en av de sekundära privata IP-adresserna, som du lade till i Azure Network Interface i ett föregående steg, till nätverks gränssnittet i Windows som har tilldelats den primära IP-adress som tilldelats Azure-nätverks gränssnittet.

        Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats till en virtuell Azure-dator i den virtuella datorns operativ system. När du anger IP-adressen i operativ systemet manuellt måste du kontrol lera att den är samma adress som den privata IP-adress som tilldelats Azure- [nätverks gränssnittet](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings), eller så kan du förlora anslutningen till den virtuella datorn. Läs mer om inställningar för [privata IP-adresser](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) . Du bör aldrig tilldela en offentlig Azure-IP-adress i operativ systemet.

    * Klicka på **OK** för att stänga TCP/IP-inställningarna och sedan på **OK** igen för att stänga inställningarna för nätverkskortet. RDP-anslutningen återupprättats.

6. Från en kommandotolk skriver du *ipconfig /all*. Kontrol lera att alla IP-adresser som du har lagt till visas och att DHCP är inaktiverat.
7. Konfigurera Windows att använda den privata IP-adressen för den primära IP-konfigurationen i Azure som den primära IP-adressen för Windows. Se [Ingen Internet åtkomst från virtuell Azure Windows-dator som har flera IP-adresser](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) för mer information. 

### <a name="validation-windows-server"></a>Verifiering (Windows Server)

För att se till att du kan ansluta till Internet från den sekundära IP-konfigurationen via den offentliga IP-adressen som är kopplad till den, när du har lagt till den korrekt med stegen ovan, använder du följande kommando (ersätter 10.0.0.7 med den sekundära, privata IP-adressen):

```bash
ping -S 10.0.0.7 outlook.com
```
>[!NOTE]
>För sekundära IP-konfigurationer kan du bara pinga till Internet om konfigurationen har en offentlig IP-adress som är kopplad till den. För primära IP-konfigurationer krävs ingen offentlig IP-adress för att pinga till Internet.
</details>

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
<details>
  <summary>Visa</summary>
Vi rekommenderar att du tittar på den senaste dokumentationen för din Linux-distribution. 

1. Öppna ett terminalfönster.
2. Kontrollera att du är rotanvändaren. Om inte anger du följande kommando:

   ```bash
   sudo -i
   ```

3. Uppdatera konfigurationsfilen för nätverksgränssnittet (förutsätter 'eth0').

   * Behåll det befintliga radobjektet för dhcp. Den primära IP-adressen är fortfarande konfigurerad som den var tidigare.
   * Lägg till en konfiguration för ytterligare en statisk IP-adress med följande kommandon:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Du bör se en .cfg-fil.
4. Öppna filen. Du bör se följande rader i slutet av filen:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Lägg till följande rader efter de rader som redan finns i den här filen:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Spara filen med följande kommando:

   ```bash
   :wq
   ```

7. Återställ nätverksgränssnittet med följande kommando:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Kör både ifdown och ifup i samma rad om du använder en fjärranslutning.
   >

8. Kontrollera att IP-adressen läggs till i nätverksgränssnittet med följande kommando:

   ```bash
   ip addr list eth0
   ```

   Du bör se IP-adressen som du lade till i listan.

### <a name="validation-ubuntu-1416"></a>Verifiering (Ubuntu 14/16)

Om du vill kontrollera att du kan ansluta till Internet från en sekundär IP-konfiguration via den offentliga IP-adress som är kopplad till den använder du följande kommando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>För sekundära IP-konfigurationer kan du bara pinga till Internet om konfigurationen har en offentlig IP-adress som är kopplad till den. För primära IP-konfigurationer krävs ingen offentlig IP-adress för att pinga till Internet.

För virtuella Linux-datorer kan du behöva lägga till lämpliga vägar när du försöker verifiera utgående anslutningar från ett sekundärt nätverkskort. Det finns flera sätt att göra detta på. Se motsvarande dokumentation för din distribution av Linux. Detta går exempelvis att åstadkomma med hjälp av följande metod:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Se till att ersätta:
    - **10.0.0.5** med den privata IP-adress som har en offentlig IP-adress som är kopplad till den
    - **10.0.0.1** till standard-gatewayen
    - **ETH2** till namnet på ditt sekundära nätverkskort </details>

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04 +)
<details>
  <summary>Visa</summary>
Ubuntu 18,04 och senare har ändrats till `netplan` för nätverks hantering i operativ systemet. Vi rekommenderar att du tittar på den senaste dokumentationen för din Linux-distribution. 

1. Öppna ett terminalfönster.
2. Kontrollera att du är rotanvändaren. Om inte anger du följande kommando:

    ```bash
    sudo -i
    ```

3. Skapa en fil för det andra gränssnittet och öppna den i en text redigerare:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Lägg till följande rader i filen och Ersätt `10.0.0.6/24` med IP/nätmask:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Spara filen med följande kommando:

    ```bash
    :wq
    ```

6. Testa ändringarna med [netplan försök](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) att bekräfta syntax:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try` ändringarna kommer att verkställas tillfälligt och ändringarna återställs efter 120 sekunder. Om anslutningen bryts kan du vänta 120 sekunder och sedan ansluta igen. Vid detta tillfälle kommer ändringarna att återställas.

7. Om du inte antar några problem med `netplan try` tillämpar du konfigurations ändringarna:

    ```bash
    netplan apply
    ```

8. Kontrollera att IP-adressen läggs till i nätverksgränssnittet med följande kommando:

    ```bash
    ip addr list eth0
    ```

    Du bör se IP-adressen som du lade till i listan. Exempel:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
### <a name="validation-ubuntu-1804"></a>Verifiering (Ubuntu 18.04 +)

Om du vill kontrollera att du kan ansluta till Internet från en sekundär IP-konfiguration via den offentliga IP-adress som är kopplad till den använder du följande kommando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>För sekundära IP-konfigurationer kan du bara pinga till Internet om konfigurationen har en offentlig IP-adress som är kopplad till den. För primära IP-konfigurationer krävs ingen offentlig IP-adress för att pinga till Internet.

För virtuella Linux-datorer kan du behöva lägga till lämpliga vägar när du försöker verifiera utgående anslutningar från ett sekundärt nätverkskort. Det finns flera sätt att göra detta på. Se motsvarande dokumentation för din distribution av Linux. Detta går exempelvis att åstadkomma med hjälp av följande metod:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Se till att ersätta:
    - **10.0.0.5** med den privata IP-adress som har en offentlig IP-adress som är kopplad till den
    - **10.0.0.1** till standard-gatewayen
    - **ETH2** till namnet på ditt sekundära nätverkskort </details>

### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS och andra)
<details>
  <summary>Visa</summary>

1. Öppna ett terminalfönster.
2. Kontrollera att du är rotanvändaren. Om inte anger du följande kommando:

    ```bash
    sudo -i
    ```

3. Ange ditt lösenord och följ instruktionerna som efterfrågas. När du är rotanvändaren navigerar du till mappen för nätverksskript med följande kommando:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Skapa en lista över de relaterade ifcfg-filerna med följande kommando:

    ```bash
    ls ifcfg-*
    ```

    Du bör se *ifcfg-eth0* som en av filerna.

5. För att lägga till en IP-adress skapar du en konfigurationsfil för den enligt anvisningarna nedan. Observera att en fil måste skapas för varje IP-konfiguration.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Öppna filen *ifcfg-eth0:0* med följande kommando:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Lägg till innehåll till filen *eth0:0*, i det här fallet med följande kommando. Se till att uppdatera informationen baserat på din IP-adress.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Spara filen med följande kommando:

    ```bash
    :wq
    ```

9. Starta om nätverkstjänsterna och kontrollera att ändringarna körs med följande kommandon:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Du bör se IP-adressen som du lade till, *eth0:0*, i listan som returneras.

### <a name="validation-red-hat-centos-and-others"></a>Verifiering (Red Hat, CentOS och andra)

Om du vill kontrollera att du kan ansluta till Internet från en sekundär IP-konfiguration via den offentliga IP-adress som är kopplad till den använder du följande kommando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>För sekundära IP-konfigurationer kan du bara pinga till Internet om konfigurationen har en offentlig IP-adress som är kopplad till den. För primära IP-konfigurationer krävs ingen offentlig IP-adress för att pinga till Internet.

För virtuella Linux-datorer kan du behöva lägga till lämpliga vägar när du försöker verifiera utgående anslutningar från ett sekundärt nätverkskort. Det finns flera sätt att göra detta på. Se motsvarande dokumentation för din distribution av Linux. Detta går exempelvis att åstadkomma med hjälp av följande metod:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Se till att ersätta:
    - **10.0.0.5** med den privata IP-adress som har en offentlig IP-adress som är kopplad till den
    - **10.0.0.1** till standard-gatewayen
    - **ETH2** till namnet på ditt sekundära nätverkskort </details>