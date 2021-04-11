---
title: Använda Azure Firewall för att kontrollera trafik till en privat slutpunkt
titleSuffix: Azure Private Link
description: Lär dig hur du kan inspektera trafik till en privat slut punkt med hjälp av Azure-brandväggen.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: c3218d8781377e76f05d10a8da2c954ac0b685a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641990"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Använda Azure Firewall för att kontrollera trafik till en privat slutpunkt

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för Azures privata länk. Privata slut punkter gör det möjligt för Azure-resurser som distribuerats i ett virtuellt nätverk att kommunicera privat med privata länk resurser.

Privata slut punkter ger resurser åtkomst till den privata länk tjänsten som distribueras i ett virtuellt nätverk. Åtkomst till den privata slut punkten via virtuell nätverks-peering och lokala nätverks anslutningar utökar anslutningen.

Du kan behöva kontrol lera eller blockera trafik från klienter till de tjänster som exponeras via privata slut punkter. Slutför den här kontrollen genom att använda [Azure-brandväggen](../firewall/overview.md) eller en virtuell nätverks installation från tredje part.

Följande begränsningar gäller:

* Nätverks säkerhets grupper (NSG) kringgås av trafik som kommer från privata slut punkter
* Användardefinierade vägar (UDR) kringgås av trafik som kommer från privata slut punkter
* En enda routningstabell kan kopplas till ett undernät
* En routningstabell har stöd för upp till 400 vägar

Azure-brandväggen filtrerar trafik med hjälp av något av följande:

* [FQDN i nätverks regler](../firewall/fqdn-filtering-network-rules.md) för TCP-och UDP-protokoll
* [FQDN i program regler](../firewall/features.md#application-fqdn-filtering-rules) för http, https och MSSQL. 

> [!IMPORTANT] 
> Användning av program regler över nätverks regler rekommenderas när du inspekterar trafik som är avsedd för privata slut punkter för att underhålla flödes symmetrin. Om nätverks regler används eller om en NVA används i stället för Azure-brandväggen måste SNAT konfigureras för trafik som är avsedd för privata slut punkter.

> [!NOTE]
> SQL-FQDN-filtrering stöds endast i [proxy-läge](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). **Proxyläge** kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, vilket är standardinställningen för klienter som ansluter i Azure, kan du filtrera åtkomst med FQDN i brand Väggs nätverks regler.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Scenario 1: hubb-och eker-arkitektur – dedikerat virtuellt nätverk för privata slut punkter

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Dedikerade Virtual Network för privata slut punkter" border="true":::

Det här scenariot är den mest expanderbara arkitekturen för att ansluta privat till flera Azure-tjänster med hjälp av privata slut punkter. En väg som pekar på nätverks adress utrymmet där de privata slut punkterna distribueras skapas. Den här konfigurationen minskar den administrativa belastningen och förhindrar att den körs i gränsen på 400 vägar.

Anslutningar från ett virtuellt klient nätverk till Azure-brandväggen i ett virtuellt nav nätverk debiteras om de virtuella nätverken är peer-datorer. Anslutningar från Azure-brandväggen i ett virtuellt hubb nätverk till privata slut punkter i ett peer-kopplat virtuellt nätverk debiteras inte.

Mer information om avgifter som rör anslutningar med peer-kopplade virtuella nätverk finns i avsnittet Vanliga frågor och svar på sidan med [priser](https://azure.microsoft.com/pricing/details/private-link/) .

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Scenario 2: hubb-och eker-arkitektur – delat virtuellt nätverk för privata slut punkter och virtuella datorer

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Privata slut punkter och Virtual Machines i samma Virtual Network" border="true":::

Det här scenariot implementeras när:

* Det går inte att ha ett dedikerat virtuellt nätverk för privata slut punkter

* När endast några tjänster exponeras i det virtuella nätverket med privata slut punkter

De virtuella datorerna kommer att ha/32 system vägar som pekar på varje privat slut punkt. En väg per privat slut punkt har kon figurer ATS för att dirigera trafik via Azure-brandväggen. 

Det administrativa arbetet med att underhålla routningstabellen ökar när tjänster exponeras i det virtuella nätverket. Möjligheten att trycka på väg gränsen ökar också.

Beroende på din övergripande arkitektur är det möjligt att köra en gräns för 400-vägar. Vi rekommenderar att du använder scenario 1 när det är möjligt.

Anslutningar från ett virtuellt klient nätverk till Azure-brandväggen i ett virtuellt nav nätverk debiteras om de virtuella nätverken är peer-datorer. Anslutningar från Azure-brandväggen i ett virtuellt hubb nätverk till privata slut punkter i ett peer-kopplat virtuellt nätverk debiteras inte.

Mer information om avgifter som rör anslutningar med peer-kopplade virtuella nätverk finns i avsnittet Vanliga frågor och svar på sidan med [priser](https://azure.microsoft.com/pricing/details/private-link/) .

## <a name="scenario-3-single-virtual-network"></a>Scenario 3: enskilt virtuellt nätverk

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Enskilt virtuellt nätverk" border="true":::

Använd det här mönstret när det inte går att migrera till en hubb och eker-arkitektur. Samma saker som i scenario 2 gäller. I det här scenariot gäller inte peering-kostnader för virtuella nätverk.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Scenario 4: lokal trafik till privata slut punkter

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Lokal trafik till privata slut punkter" border="true":::

Den här arkitekturen kan implementeras om du har konfigurerat anslutningen till ditt lokala nätverk med hjälp av något av följande: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [VPN för plats till plats](../vpn-gateway/tutorial-site-to-site-portal.md) 

Om dina säkerhets krav kräver klient trafik till tjänster som exponeras via privata slut punkter som ska dirigeras via en säkerhetsinstallation, distribuera det här scenariot.

Samma överväganden som i scenario 2 gäller. I det här scenariot finns det inga kostnader för peering av virtuella nätverk. Mer information om hur du konfigurerar DNS-servrar så att lokala arbets belastningar kan komma åt privata slut punkter finns i [lokala arbets belastningar med en DNS-vidarebefordrare](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration.
* En Log Analytics-arbetsyta.  

Se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md) för att skapa en arbets yta om du inte har en i din prenumeration.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät som är värd för den virtuella datorn som används för att få åtkomst till din privata länk resurs. En Azure SQL-databas används som exempel tjänst.

## <a name="virtual-networks-and-parameters"></a>Virtuella nätverk och parametrar

Skapa tre virtuella nätverk och deras motsvarande undernät för att:

* Innehåller Azure-brandväggen som används för att begränsa kommunikationen mellan den virtuella datorn och den privata slut punkten.
* Var värd för den virtuella datorn som används för att få åtkomst till din privata länk resurs.
* Värd för den privata slut punkten.

Ersätt följande parametrar i stegen med informationen nedan:

### <a name="azure-firewall-network"></a>Azure Firewall-nätverk

| Parameter                   | Värde                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | USA, södra centrala      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Nätverk för virtuell dator

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | USA, södra centrala      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Privat slut punkt nätverk

| Parameter                   | Värde                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | USA, södra centrala      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Upprepa steg 1 till 9 för att skapa de virtuella nätverken som är värdar för den virtuella datorn och privata slut punkts resurser.

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **beräknings**  >  **virtuell dator**.

2. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du har skapat den här resurs gruppen i föregående avsnitt.  |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM**. |
    | Region | Välj **(US) södra centrala USA**. |
    | Alternativ för tillgänglighet | Lämna standard **ingen redundans för infrastruktur krävs**. |
    | Bild | Välj **Ubuntu Server 18,04 LTS-gen1**. |
    | Storlek | Välj **Standard_B2s**. |
    | **Administratörs konto** |  |
    | Autentiseringstyp | Välj **lösen ord**. |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | Lösenord | Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **Regler för inkommande portar** |  |
    | Offentliga inkommande portar | Välj **Ingen**. |
    |||

3. Välj **Nästa: diskar**.

4. I **Skapa en virtuell dator – diskar** lämnar du standardinställningarna och väljer **Nästa: Nätverk**.

5. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Välj **myVMVNet**.  |
    | Undernät | Välj **VMSubnet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardvärdet **(New) myVm-IP**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **SSH**.|
    ||

6. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

7. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

## <a name="deploy-the-firewall"></a>Distribuera brand väggen

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.

2. Skriv **brand väggen** i sökrutan och tryck på **RETUR**.

3. Välj **brand vägg** och välj sedan **skapa**.

4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**.  |
    | **Instansinformation** |  |
    | Name | Ange **myAzureFirewall**. |
    | Region | Välj **södra centrala USA**. |
    | Tillgänglighetszon | Lämna standardvärdet **none**. |
    | Välj ett virtuellt nätverk    |    Välj **Använd befintlig**.    |
    | Virtuellt nätverk    |    Välj **myAzFwVNet**.    |
    | Offentlig IP-adress    |    Välj **Lägg till nytt** och i namn Ange **brand väggen-IP**.    |
    | Tvingad tunneltrafik    | Låt standardvärdet vara **inaktiverat**.    |
    |||
5. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

6. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

## <a name="enable-firewall-logs"></a>Aktivera brand Väggs loggar

I det här avsnittet aktiverar du loggarna i brand väggen.

1. I Azure Portal väljer du **alla resurser** i den vänstra menyn.

2. Välj brand Väggs **myAzureFirewall** i listan över resurser.

3. Under **övervakning** i brand Väggs inställningarna väljer du **diagnostikinställningar**

4. Välj **+ Lägg till diagnostisk inställning** i diagnostikinställningar.

5. I **inställningen diagnostik** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn på diagnostisk inställning | Ange **myDiagSetting**. |
    | Kategori information | |
    | kvorumloggen | Välj **AzureFirewallApplicationRule** och **AzureFirewallNetworkRule**. |
    | Information om mål | Välj **Skicka till Log Analytics**. |
    | Prenumeration | Välj din prenumeration. |
    | Log Analytics-arbetsyta | Välj din Log Analytics-arbetsyta. |

6. Välj **Spara**.

## <a name="create-azure-sql-database"></a>Skapa en Azure SQL-databas

I det här avsnittet skapar du en privat SQL Database.

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **databas**  >  **SQL Database**.

2. I **skapa SQL Database – grunderna** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du har skapat den här resurs gruppen i föregående avsnitt.|
    | **Databasinformation** |  |
    | Databasnamn  | Ange min **databas**.  |
    | Server | Välj **Skapa ny** och ange informationen nedan.    |
    | Servernamn | Ange **MyDBServer**. Ange ett unikt namn om det här namnet tas.   |
    | Inloggning för serveradministratör | Ange ett namn som du väljer. |
    | Lösenord    |    Ange ett valfritt lösenord.    |
    | Bekräfta lösenord | Ange lösenordet igen    |
    | Location    | Välj **(US) södra centrala USA**.    |
    | Vill använda elastisk SQL-pool    | Lämna standardvärdet **Nej**. |
    | Beräkning och lagring | Lämna standard **generell användning Gen5, 2 virtuella kärnor, 32 GB lagring**. |
    |||

3. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

4. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet skapar du en privat slut punkt för Azure SQL-databasen i föregående avsnitt.

1. I Azure Portal väljer du **alla resurser** i den vänstra menyn.

2. Välj Azure SQL Server- **MyDBServer** i listan över tjänster.  Om du har använt ett annat Server namn väljer du det namnet.

3. I Server inställningarna väljer du **privata slut punkts anslutningar** under **säkerhet**.

4. Välj **+ privat slut punkt**.

5. I **skapa en privat slut punkt** anger eller väljer du den här informationen på fliken **grundläggande** :

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. |
    | **Instansinformation** | |
    | Name | Ange **SQLPrivateEndpoint**. |
    | Region | Välj **(US) södra centrala USA.** |

6. Välj fliken **resurs** eller Välj **Nästa: resurs** längst ned på sidan.

7. På fliken **resurs** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Anslutningsmetod | Välj **Anslut till en Azure-resurs i min katalog**. |
    | Prenumeration | Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. SQL/Servers**. |
    | Resurs | Välj **MyDBServer** eller namnet på den server som du skapade i föregående steg.
    | Målunderresurs | Välj **sqlServer**. |

8. Välj fliken **konfiguration** eller Välj **Nästa: konfiguration** längst ned på sidan.

9. På fliken **konfiguration** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Nätverk** | |
    | Virtuellt nätverk | Välj **myPEVnet**. |
    | Undernät | Välj **PrivateEndpointSubnet**. |
    | **Privat DNS-integrering** | |
    | Integrera med privat DNS-zon | Välj **Ja**. |
    | Prenumeration | Välj din prenumeration. |
    | Privata DNS-zoner | Lämna standard **privatelink.Database.Windows.net**. |

10. Välj fliken **Granska + skapa** eller Välj **Granska + skapa** längst ned på sidan.

11. Välj **Skapa**.

12. När slut punkten har skapats väljer du **brand väggar och virtuella nätverk** under **säkerhet**.

13. I **brand väggar och virtuella nätverk** väljer du **Ja** bredvid **Tillåt Azure-tjänster och-resurser för att få åtkomst till den här servern**.

14. Välj **Spara**.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Anslut de virtuella nätverken med peering för virtuella nätverk

I det här avsnittet ska vi ansluta virtuella nätverk **myVMVNet** och **myPEVNet** till **myAzFwVNet** med hjälp av peering. Det finns ingen direkt anslutning mellan **myVMVNet** och **myPEVNet**.

1. Skriv **myAzFwVNet** i portalens Sök fält.

2. Välj **peer** -kopplingar under menyn **Inställningar** och välj **+ Lägg till**.

3. I **Lägg till peering** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Peer-kopplingens namn från myAzFwVNet till ett virtuellt dator nätverk | Ange **myAzFwVNet-till-myVMVNet**. |
    | **Peer-information** |  |
    | Distributions modell för virtuellt nätverk  | Låt standard **resurs hanteraren** vara kvar.  |
    | Jag känner till mitt resurs-ID | Lämna alternativet omarkerat.    |
    | Prenumeration | Välj din prenumeration.    |
    | Virtuellt nätverk | Välj **myVMVNet**. |
    | Peer-kopplingens namn från det virtuella fjärrnätverket till myAzFwVNet    |    Ange **myVMVNet-till-myAzFwVNet**.    |
    | **Konfiguration** | |
    | **Konfigurera åtkomst inställningar för virtuellt nätverk** | |
    | Tillåt åtkomst till virtuellt nätverk från myAzFwVNet till fjärran sluten virtuellt nätverk | Låt standardvärdet **Aktiverad** vara kvar.    |
    | Tillåt åtkomst till virtuella nätverk från virtuella myAzFwVNet-nätverk till    | Låt standardvärdet **Aktiverad** vara kvar.    |
    | **Konfigurera inställningar för vidarebefordrad trafik** | |
    | Tillåt vidarebefordrad trafik från det virtuella fjärrnätverket till myAzFwVNet    | Välj **Aktiverad**. |
    | Tillåt vidarebefordrad trafik från myAzFwVNet till fjärran sluten virtuellt nätverk | Välj **Aktiverad**. |
    | **Konfigurera inställningar för gateway-överföring** | |
    | Tillåt Gateway-överföring | Lämna alternativet omarkerat |
    |||

4. Välj **OK**.

5. Välj **+ Lägg till**.

6. I **Lägg till peering** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Peer-kopplingens namn från myAzFwVNet till ett virtuellt dator nätverk | Ange **myAzFwVNet-till-myPEVNet**. |
    | **Peer-information** |  |
    | Distributions modell för virtuellt nätverk  | Låt standard **resurs hanteraren** vara kvar.  |
    | Jag känner till mitt resurs-ID | Lämna alternativet omarkerat.    |
    | Prenumeration | Välj din prenumeration.    |
    | Virtuellt nätverk | Välj **myPEVNet**. |
    | Peer-kopplingens namn från det virtuella fjärrnätverket till myAzFwVNet    |    Ange **myPEVNet-till-myAzFwVNet**.    |
    | **Konfiguration** | |
    | **Konfigurera åtkomst inställningar för virtuellt nätverk** | |
    | Tillåt åtkomst till virtuellt nätverk från myAzFwVNet till fjärran sluten virtuellt nätverk | Låt standardvärdet **Aktiverad** vara kvar.    |
    | Tillåt åtkomst till virtuella nätverk från virtuella myAzFwVNet-nätverk till    | Låt standardvärdet **Aktiverad** vara kvar.    |
    | **Konfigurera inställningar för vidarebefordrad trafik** | |
    | Tillåt vidarebefordrad trafik från det virtuella fjärrnätverket till myAzFwVNet    | Välj **Aktiverad**. |
    | Tillåt vidarebefordrad trafik från myAzFwVNet till fjärran sluten virtuellt nätverk | Välj **Aktiverad**. |
    | **Konfigurera inställningar för gateway-överföring** | |
    | Tillåt Gateway-överföring | Lämna alternativet omarkerat |

7. Välj **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Länka de virtuella nätverken till den privata DNS-zonen

I det här avsnittet länkar vi virtuella nätverk **myVMVNet** och **myAzFwVNet** till **privatelink.Database.Windows.net** privata DNS-zon. Den här zonen skapades när vi skapade den privata slut punkten. 

Länken krävs för att den virtuella datorn och brand väggen ska kunna matcha FQDN för databasen till dess privata slut punkts adress. **MyPEVNet** för virtuella nätverk länkades automatiskt när den privata slut punkten skapades.

>[!NOTE]
>Om du inte länkar virtuella nätverk för virtuella datorer och brand väggar till den privata DNS-zonen kommer både den virtuella datorn och brand väggen fortfarande att kunna matcha SQL Server FQDN. De kommer att matcha sin offentliga IP-adress.

1. I portalens Sök fält anger du **privatelink. Database**.

2. Välj **privatelink.Database.Windows.net** i Sök resultaten.

3. Välj **virtuella nätverks länkar** under **Inställningar**.

4. Välj **+ Lägg till**

5. I **Lägg till virtuellt nätverks länk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Länk namn | Ange **länk-till-myVMVNet**. |
    | **Information om virtuellt nätverk** |  |
    | Jag känner till resurs-ID för virtuellt nätverk  | Lämna alternativet omarkerat.  |
    | Prenumeration | Välj din prenumeration.    |
    | Virtuellt nätverk | Välj **myVMVNet**. |
    | **INSTÄLLNINGARNA** | |
    | Aktivera automatisk registrering | Lämna alternativet omarkerat.    |


6. Välj **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Konfigurera en program regel med SQL FQDN i Azure Firewall

I det här avsnittet konfigurerar du en program regel för att tillåta kommunikation mellan **myVM** och den privata slut punkten för SQL Server **MyDBServer.Database.Windows.net**. 

Den här regeln tillåter kommunikation via brand väggen som vi skapade i föregående steg.

1. Skriv **myAzureFirewall** i portalens Sök fält.

2. Välj **myAzureFirewall** i Sök resultaten.

3. Välj **regler** under **Inställningar** i Översikt över **myAzureFirewall** .

4. Välj fliken **samling av program regel** .

5. Välj **+ Lägg till program regel samling**.

6. I **Lägg till program regel samling** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **SQLPrivateEndpoint**. |
    | Prioritet | Ange **100**. |
    | Action | Ange **Tillåt**. |
    | **Regler** |  |
    | **FQDN-taggar** | |
    | Name  | Lämna tomt.  |
    | Källtyp | Lämna standard **-IP-adressen**.    |
    | Källa | Lämna tomt. |
    | FQDN-taggar | Låt standardvärdet **0 vara markerat**. |
    | **Mål-FQDN** | |
    | Name | Ange **SQLPrivateEndpoint**.    |
    | Källtyp | Lämna standard **-IP-adressen**. |
    | Källa | Ange **10.1.0.0/16**. |
    | Protokoll: port | Ange **MSSQL: 1433**. |
    | Mål-FQDN | Ange **MyDBServer.Database.Windows.net**. |
    |||

7. Välj **Lägg till**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Dirigera trafik mellan den virtuella datorn och den privata slut punkten via Azure-brandväggen

Vi har inte skapat någon peering för virtuellt nätverk direkt mellan virtuella nätverk **myVMVNet** och **myPEVNet**. Den virtuella datorns **myVM** har ingen väg till den privata slut punkt som vi skapade. 

I det här avsnittet ska vi skapa en routningstabell med en anpassad väg. 

Vägen skickar trafik från **myVM** -undernätet till adress utrymmet för det virtuella nätverket **myPEVNet** via Azure-brandväggen.

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.

2. Skriv **routningstabellen** i rutan Sök och tryck på **RETUR**.

3. Välj **routningstabell** och välj sedan **skapa**.

4. På sidan **skapa** routningstabell använder du följande tabell för att konfigurera routningstabellen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**.  |
    | **Instansinformation** |  |
    | Region | Välj **södra centrala USA**. |
    | Name | Ange **VMsubnet-till-AzureFirewall**. |
    | Sprida Gateway-vägar | Välj **Nej**. |

5. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

6. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

7. När distributionen är klar väljer **du gå till resurs**.

8. Välj **vägar** under **Inställningar**.

9. Välj **+ Lägg till**.

10. På sidan **Lägg till väg** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Vägnamn | Ange **myVMsubnet-till-privateendpoint**. |
    | Adressprefix | Ange **10.2.0.0/16**.  |
    | Nästa hopptyp | Välj **Virtuell installation**. |
    | Nexthop-adress | Ange **10.0.0.4**. |

11. Välj **OK**.

12. Välj **undernät** under **Inställningar**.

13. Välj **+ associera**.

14. På sidan **associera undernät** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Välj **myVMVNet**. |
    | Undernät | Välj **VMSubnet**.  |

15. Välj **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Ansluta till den virtuella datorn från klient datorn

Anslut till VM- **myVm** från Internet på följande sätt:

1. I portalens Sök fält anger du **myVm-IP**.

2. Välj **myVM-IP** i Sök resultaten.

3. Kopiera eller Skriv ned värdet under **IP-adress**.

4. Om du använder Windows 10 kör du följande kommando med hjälp av PowerShell. För andra versioner av Windows-klienter använder du en SSH-klient som [SparaTillFil](https://www.putty.org/):

* Ersätt **username** med det admin-användarnamn som du angav när du skapade den virtuella datorn.

* Ersätt **IP-adressen med** IP-adressen från föregående steg.

    ```bash
    ssh username@IPaddress
    ```

5. Ange lösen ordet som du definierade när du skapade **myVm**

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Åtkomst SQL Server privat från den virtuella datorn

I det här avsnittet ska du ansluta privat till SQL Database med hjälp av den privata slut punkten.

1. Ange `nslookup mydbserver.database.windows.net`
    
    Du får ett meddelande som liknar följande:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Installera [SQL Server kommando rads verktyg](/sql/linux/quickstart-install-connect-ubuntu#tools).

3. Kör följande kommando för att ansluta till SQL Server. Använd Server administratören och lösen ordet som du definierade när du skapade SQL Server i föregående steg.

* Ersätt **\<ServerAdmin>** med det administratörs användar namn som du angav när du skapade SQL-servern.

* Ersätt **\<YourPassword>** med det administratörs lösen ord du angav när du skapade SQL Server.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. En SQL-kommandotolk visas vid lyckad inloggning. Ange **exit** för att avsluta **SQLCMD** -verktyget.

5. Stäng anslutningen till **myVM** genom att ange **exit**.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Verifiera trafiken i Azure Firewall-loggar

1. I Azure Portal väljer du **alla resurser** och sedan Log Analytics arbets ytan.

2. Välj **loggar** under **allmänt** på sidan Log Analytics arbets yta.

3. Välj knappen blå **Kom igång** .

4. I fönstret **exempel frågor** väljer du **brand väggar** under **alla frågor**.

5. Välj knappen **Kör** under **logg data för program regel**.

6. I logg frågans utdata kontrollerar du att **MyDBServer.Database.Windows.net** är listat under **FQDN** och **SQLPrivateEndpoint** visas under **RuleCollection**.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med resurserna tar du bort resurs gruppen och alla resurser den innehåller:

1. Skriv **myResourceGroup** i **sökrutan längst** upp i portalen och välj **myResourceGroup** från Sök resultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange **myResourceGroup** för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du utforskat olika scenarier som du kan använda för att begränsa trafiken mellan en virtuell dator och en privat slut punkt med hjälp av Azure-brandväggen. 

Du har anslutit till den virtuella datorn och kommunicerat på ett säkert sätt till databasen via Azure Firewall med hjälp av en privat länk.

Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](private-endpoint-overview.md).
