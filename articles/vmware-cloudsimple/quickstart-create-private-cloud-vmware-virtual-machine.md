---
title: Snabb start – skapa en virtuell Azure VMware-dator i ett privat moln – Azure VMware-lösning av CloudSimple
description: Lär dig hur du skapar en virtuell VMware-dator på ett privat CloudSimple-moln. Få åtkomst till CloudSimple-portalen från Azure Portal.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c741f01ccc3e34ec991aa77fc850fd9f60c4d8aa
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899294"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Skapa virtuella VMware-datorer i ditt privata moln

Börja med att komma åt CloudSimple-portalen från Azure Portal om du vill skapa virtuella datorer i ditt privata moln.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänster**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. På sidan **Översikt** klickar du på **gå till CloudSimple-portalen** för att öppna en ny flik i webbläsaren för CloudSimple-portalen.  Logga in med dina inloggnings uppgifter för Azure om du uppmanas till detta.  

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Starta vCenter Web-UI

Du kan nu starta vCenter för att konfigurera virtuella datorer och principer.

Kom åt vCenter genom att starta från CloudSimple-portalen. På Start sidan, under **vanliga uppgifter**, klickar du på **Starta vSphere-klient**.  Välj det privata molnet och klicka sedan på **Starta vSphere-klient** i det privata molnet.

   ![Starta vSphere-klienten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Ladda upp en ISO-eller vSphere-mall

  > [!WARNING]
  > För ISO-överföring använder du vSphere HTML5-klienten.  Om du använder en Flash-klient kan ett fel uppstå.

1. Hämta ISO-eller vSphere-mallen som du vill överföra till vCenter för att skapa en virtuell dator och ha den tillgänglig i det lokala systemet.
2. Klicka på **disk** ikonen i vCenter och välj **vsanDatastore**. Klicka på **filer** och sedan på **ny mapp**.
    ![vCenter ISO](media/vciso00.png)

3. Skapa en mapp med titeln "ISO och templates".

4. Navigera till mappen ISO i ISO och mallar och klicka på **överför filer**. Följ anvisningarna på skärmen för att ladda upp ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Skapa en virtuell dator i vCenter

1. I vCenter klickar du på ikonen **värdar och kluster** .

2. Högerklicka på **arbets belastning** och välj **ny virtuell dator**.
    ![Skärm bild som markerar meny alternativet ny virtuell dator.](media/vcvm01.png)

3. Välj **Skapa ny virtuell dator** och klicka på **Nästa**.
    ![Skärm bild som markerar alternativet Skapa ny virtuell dator.](media/vcvm02.png)

4. Namnge datorn, Välj platsen för **arbets Belastningens VM** och klicka på **Nästa**.
    ![Skärm bild som visar alternativet virtuella arbets belastningar.](media/vcvm03.png)

5. Välj resurs för beräkning av **arbets belastning** och klicka på **Nästa**.
    ![Skärm bild som visar beräknings resursen för arbets belastningen.](media/vcvm04.png)

6. Välj **vsanDatastore** och klicka på **Nästa**.
    ![Skärm bild som visar alternativet vsanDatastore.](media/vcvm05.png)

7. Behåll standard valet av ESXi 6,5 och klicka på **Nästa**.
    ![Skärm bild som visar det valda ESXi 6,5-kompatibilitetsinställningen.](media/vcvm06.png)

8. Välj gäst operativ systemet för ISO-filen för den virtuella dator som du skapar och klicka på **Nästa**.
    ![Skärm bild som visar hur du väljer guese-operativ systemet för den virtuella datorn ISO.](media/vcvm07.png)

9. Välj alternativ för hård disk och nätverk. För ny CD/DVD-enhet väljer du **data lager ISO-fil**.  Om du vill tillåta trafik från den offentliga IP-adressen till den här virtuella datorn väljer du nätverket som **VM-1**.
    ![Skärm bild som visar var du väljer ISO-filen för data lager.](media/vcvm08.png)

10. Ett val fönster öppnas. Välj den fil som du tidigare laddade upp till mappen ISO och mallar och klicka på **OK**.
    ![Ny virtuell dator](media/vcvm10.png)

11. Granska inställningarna och klicka på **OK** för att skapa den virtuella datorn.
    ![Skärm bild som visar var du granskar inställningarna.](media/vcvm11.png)

Den virtuella datorn läggs nu till i beräknings resurserna för arbets belastningen och är redo att användas. 
![Skärm bild som visar den virtuella datorn som har lagts till i beräknings resurserna för arbets belastningen.](media/vcvm12.png)

Den grundläggande installationen slutförs nu. Du kan börja använda ditt privata moln på samma sätt som du använder din lokala infrastruktur för virtuella datorer.

Följande avsnitt innehåller information om hur du konfigurerar DNS-och DHCP-servrar för privata moln arbets belastningar och hur du ändrar standard konfigurationen för nätverk.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Lägg till användare och identitets källor till vCenter (valfritt)

CloudSimple tilldelar ett standard-vCenter-användarkonto med användar namn `cloudowner@cloudsimple.local` . Det krävs inga ytterligare konto inställningar för att komma igång.  CloudSimple tilldelar vanligt vis administratörer de privilegier de behöver för att utföra normala åtgärder.  Konfigurera din lokala Active Directory eller Azure AD som en [ytterligare identitets källa](set-vcenter-identity.md) i ditt privata moln.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Skapa en DNS-och DHCP-server (valfritt)

Program och arbets belastningar som körs i en privat moln miljö kräver namn matchning och DHCP-tjänster för sökning och tilldelning av IP-adresser. En korrekt DHCP-och DNS-infrastruktur krävs för att tillhandahålla dessa tjänster. Du kan konfigurera en virtuell dator i vCenter för att tillhandahålla dessa tjänster i din privata moln miljö.

Förutsättningar

* En distribuerad port grupp med VLAN konfigurerat

* Dirigera installationen till lokala eller Internetbaserade DNS-servrar

* Mall för virtuella datorer eller ISO för att skapa en virtuell dator

Följande länkar ger vägledning om hur du konfigurerar DHCP-och DNS-servrar i Linux och Windows.

#### <a name="linux-based-dns-server-setup"></a>Installation av Linux-baserad DNS-Server

Linux erbjuder olika paket för att konfigurera DNS-servrar.  Här är en länk till instruktioner för att skapa en BIND DNS-server med öppen källkod.

[Exempel på installation](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows-baserad installation

Dessa Microsoft-avsnitt beskriver hur du konfigurerar en Windows-Server som en DNS-server och som en DHCP-server.

[Windows Server som DNS-Server](/windows-server/networking/dns/dns-top)

[Windows Server som DHCP-server](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Anpassa nätverks konfiguration (valfritt)

På nätverks sidorna i CloudSimple-portalen kan du ange konfigurationen för brand Väggs tabeller och offentliga IP-adresser för virtuella datorer.

### <a name="allocate-public-ips"></a>Allokera offentliga IP-adresser

1. Navigera till **nätverks > offentlig IP-adress** i CloudSimple-portalen.
2. Klicka på **allokera offentlig IP**.
3. Ange ett namn för att identifiera IP-adress posten.
4. Behåll standard platsen.
5. Använd skjutreglaget för att ändra tids gränsen för inaktivitet om du vill.
6. Ange den lokala IP-adress som du vill tilldela en offentlig IP-adress för.
7. Ange ett associerat DNS-namn om du vill.
8. Klicka på **Klar**.

    ![Offentlig IP-adress](media/quick-create-pc-public-ip.png)

Uppgiften att allokera den offentliga IP-adressen börjar. Du kan kontrol lera status för aktiviteten på sidan **aktiviteter > uppgifter** . När allokeringen är klar visas den nya posten på sidan offentliga IP-adresser.

Den virtuella dator som den här IP-adressen måste mappas till måste konfigureras med den lokala adress som anges ovan. Proceduren för att konfigurera en IP-adress är speciell för operativ systemet för den virtuella datorn. Mer information finns i dokumentationen för operativ systemet för virtuella datorer.

#### <a name="example"></a>Exempel

Här är till exempel information om Ubuntu 16,04.

Lägg till den statiska metoden i inet-adress familjen-konfigurationen i filen/etc/network/interfaces. Ändra adress, nätmask och gateway-värden. I det här exemplet använder vi eth0-gränssnittet, interna IP-192.168.24.10, Gateway-192.168.24.1 och netmask 255.255.255.0. Information om tillgängliga undernät finns i Välkommen e-postmeddelandet för din miljö.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Inaktivera gränssnittet manuellt.

```
sudo ifdown eth0
```
Aktivera gränssnittet manuellt.

```
sudo ifup eth0
```

Som standard **nekas** all inkommande trafik från Internet. Skapa en [brand Väggs tabell](firewall.md)om du vill öppna någon annan port.

När du har konfigurerat en intern IP-adress som statisk IP-adress kontrollerar du att du kan ansluta till Internet från den virtuella datorn.

```
ping 8.8.8.8
```
Kontrol lera också att du kan komma åt den virtuella datorn från Internet med hjälp av den offentliga IP-adressen.

Se till att alla iptable-regler på den virtuella datorn inte blockerar port 80 inkommande.
        
```
netstat -an | grep 80
```

Starta en http-server som lyssnar på port 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

eller

```
python3 -m http.server 80
```
Starta en webbläsare på Skriv bordet och peka på port 80 för den offentliga IP-adressen för att bläddra bland filerna på den virtuella datorn.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Standard brand Väggs regler för offentlig IP-CloudSimple

* VPN-trafik: all trafik mellan (från/till) VPN-nätverket och alla arbets belastnings nätverk och hanterings nätverk tillåts.
* Intern trafik i privat moln: all sydöstra sydöstra trafik mellan (från/till) arbets belastnings nätverk och hanterings nätverket (visas ovan) tillåts.
* Internet trafik:
  * All inkommande trafik från Internet nekas till arbets belastnings nätverk och hanterings nätverket.
  * All utgående trafik till Internet från arbets belastnings nätverk eller hanterings nätverk tillåts.

Du kan också ändra hur trafiken skyddas med hjälp av funktionen brand Väggs regler. Mer information finns i [Konfigurera brand Väggs tabeller och regler](firewall.md).

## <a name="install-solutions-optional"></a>Installera lösningar (valfritt)

Du kan installera lösningar på ditt privata moln i CloudSimple för att dra full nytta av din privata moln vCenter-miljö. Du kan konfigurera säkerhets kopiering, katastrof återställning, replikering och andra funktioner för att skydda dina virtuella datorer. Exempel på detta är VMware Site Recovery Manager (VMware SRM) och säkerhets kopiering & replikering av Veeam.

Om du vill installera en lösning måste du begära ytterligare behörigheter under en begränsad period. Se [eskalera privilegier](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* [Ansluta till ett lokalt nätverk med hjälp av Azure ExpressRoute](on-premises-connection.md)
* [Konfigurera VPN-gatewayer i CloudSimple-nätverk](vpn-gateway.md)
