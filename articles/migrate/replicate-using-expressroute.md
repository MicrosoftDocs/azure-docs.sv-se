---
title: Replikera data över ExpressRoute med Azure Migrate Server-migrering
description: Så här använder du Azure-ExpressRoute för replikering med Azure Migrate Server-migrering
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: b52d61c2828ddf5c04ab943d73964d236c9017c1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098850"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Replikera data över ExpressRoute med Azure Migrate: Server-migrering

I den här artikeln får du lära dig hur du konfigurerar [Azure Migrate: Server-migrering](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) för att replikera data över en ExpressRoute-krets vid migrering av servrar till Azure.

## <a name="understand-azure-expressroute-circuits"></a>Förstå Azure ExpressRoute-kretsar
En ExpressRoute-krets (ER) ansluter din lokala infrastruktur till Microsoft via en anslutnings leverantör. ExpressRoute-kretsar kan konfigureras för att använda privat peering, Microsoft-peering eller både och. Läs artikeln om [ExpressRoute-kretsar och peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) för att lära dig mer om de olika peering-alternativ som är tillgängliga med ExpressRoute.

Med verktyget Migreringsverktyg för Server kan du migrera lokala servrar och servrar från andra moln till Azure Virtual Machines. Azure Migrate Verktyget fungerar genom att konfigurera en pågående replik ström för att replikera data från de servrar som ska migreras till hanterade diskar i din Azure-prenumeration. När du är redo att migrera servrarna används replikerade data i Azure för att migrera servrarna.

Data som replikeras från dina lokala servrar kan konfigureras att skickas till din Azure-prenumeration via Internet (med en säker krypterad anslutning) eller via en ExpressRoute-anslutning. När du har ett stort antal servrar att migrera kan du med hjälp av ExpressRoute för replikering hjälpa dig att migrera servrar effektivare genom att använda den allokerade bandbredd som är tillgänglig med din ExpressRoute-krets.

I den här artikeln får du lära dig
> [!div class="checklist"]
>
> * Så här replikerar du data med hjälp av en ExpressRoute-krets med privat peering.
> * Så här replikerar du data med hjälp av en ExpressRoute-krets med Microsoft-peering.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Replikera data med hjälp av en ExpressRoute-krets med privat peering

> [!NOTE]
> Replikering över en privat peering-krets stöds för närvarande endast för [migrering utan agent för virtuella VMware-datorer till Azure](./tutorial-migrate-vmware.md). Stöd för privata slut punkter för andra [replikeringsintervall](./migrate-services-overview.md#azure-migrate-server-migration-tool) kommer snart att vara tillgängligt.

I den metod som används för att migrera virtuella VMware-datorer till Azure laddar Azure Migrate-installationen först replikeringsdata till ett lagrings konto (cache Storage-konto) i din prenumeration. Replikerade data från cache-lagrings kontot flyttas sedan till replikbaserade diskar i din prenumeration av Azure Migrates tjänsten. Om du vill använda en privat peering-krets för replikering skapar du och ansluter en privat slut punkt till lagrings kontot för cache. Privata slut punkter använder en eller flera privata IP-adresser från ditt virtuella nätverk (VNet), vilket effektivt tar lagrings kontot i ditt Azure VNet. Den privata slut punkten gör att Azure Migrate-enheten kan ansluta till cache-lagrings kontot med ExpressRoute privat peering och överföra data direkt på den privata IP-adressen. <br/>  

![Replikeringsprocessen](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Förutom replikeringsdata kommunicerar Azure Migrate-utrustningen med Azure Migrate-tjänsten för kontroll Plans aktiviteterna, inklusive att samordna replikeringen. Kontroll Plans kommunikationen mellan Azure Migrate-installationen och tjänsten Azure Migrate fortsätter att ske via Internet på Azure Migrate tjänstens offentliga slut punkt.
> - Lagrings kontots privata slut punkt bör vara tillgänglig från det nätverk som Azure Migrate-installationen har distribuerats på.
> - DNS måste konfigureras för att matcha DNS-frågor från Azure Migrate-enheten för cache Storage-kontots BLOB service-slutpunkt till den privata IP-adressen för den privata slut punkten som är kopplad till cache-lagrings kontot.
> - Lagrings kontot för cache måste vara tillgängligt på den offentliga slut punkten. (Azure Migrate tjänsten använder cache-kontots offentliga slut punkt för att flytta data från lagrings kontot till replik Managed disks.) 


### <a name="1-pre-requisites"></a>1. krav

Azure-användaren som skapar den privata slut punkten måste ha följande behörigheter för resurs gruppen och det virtuella nätverk som den privata slut punkten kommer att skapas i.

**Användningsfall** | **Behörigheter** 
--- | --- 
 Skapa och hantera privata slut punkter. | Microsoft. Network/privateEndpoint/Write/Action<br/>Microsoft. Network/privateEndpoint/Read/Action  
|Koppla en privat slut punkt till ett VNet/undernät.<br/>Detta krävs på det virtuella nätverk där den privata slut punkten ska skapas.| Microsoft. Network/virtualNetworks/Subnet/JOIN/åtgärd Microsoft. Network/virtualNetworks/Join/Action
|Länka den privata slut punkten till ett lagrings konto. <br/>| Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnectionApproval/åtgärd <br/> Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnections/Read
|Skapa ett nätverks gränssnitt och Anslut det till en nätverks säkerhets grupp. | Microsoft. Network/networkInterfaces/Read <br/> Microsoft. Network/networkInterfaces/subnets/Write <br/> Microsoft. Network/networkInterfaces/subnets/Read<br/> Microsoft. Network/networkSecurityGroups/Join/Action (valfritt)
Skapa och hantera privata DNS-zoner.| Rollen Privat DNS Zone Contributor <br/> _Eller_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/Write Microsoft. Network/privateDnsZones/Read <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Read <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/Read <br/> Microsoft. Network/virtualNetworks/JOIN/åtgärd 

### <a name="2-identify-the-cache-storage-account"></a>2. identifiera lagrings kontot för cachen 
 
Azure Migrate skapar automatiskt ett cache Storage-konto när du konfigurerar replikering (med hjälp av Azure Portal upplevelse) för en virtuell dator för första gången i ett Azure Migrate-projekt. Lagrings kontot skapas i samma prenumeration och resurs grupp som du skapade Azure Migrate-projektet i.

Skapa och hitta lagrings kontot:

1. Använd Azure Portal-upplevelsen för Azure Migrate för att replikera en eller flera virtuella datorer i projektet.
2. Navigera till resurs gruppen för det Azure Migrate projektet.
3. Leta upp lagrings kontot för cache genom att identifiera prefixet **"LSA"** i lagrings konto namnet.

![Vyn resurs grupp](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Om du har fler än ett lagrings konto med prefixet **"LSA"** i resurs gruppen kan du kontrol lera lagrings kontot genom att gå till menyn replikeringsinställningar och mål konfiguration för någon av de replikerade virtuella datorerna i projektet. <br/> 
> ![Översikt över replikeringsinställningar](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. uppgradera cache Storage-kontot till Generell användning v2 

Du kan bara skapa privata slut punkter på ett Generell användning v2-lagrings konto (GPv2). Om lagrings kontot för cache inte är ett GPv2 lagrings konto kan du uppgradera det till GPv2 med hjälp av följande steg:

1. Navigera till ditt lagringskonto.
2. Välj **Konfiguration**.
3. Välj **uppgradering** under **typ av konto**.
4. Ange namnet på ditt konto under **Bekräfta uppgradering**.
5. Välj **Uppgradera** längst ned på sidan.

![Uppgradera lagrings konto](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. skapa en privat slut punkt för lagrings kontot

1. Gå till ditt lagrings konto, Välj **nätverk** på den vänstra menyn och välj fliken **anslutningar för privata slut punkter** .  
2. Välj **+ privat slut punkt**.

    a. I fönstret **skapa en privat slut punkt** väljer du **prenumerationen** och **resurs gruppen**. Ange ett namn för din privata slut punkt och välj lagrings kontots region.  
    ![Fönstret PE-konfiguration](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. På fliken **resurs** anger du namnet på den **prenumeration** som lagrings kontot finns i. Välj **Microsoft. Storage/storageAccounts** som **resurs typ**. Ange namnet på lagrings kontot för GPv2 typ i **resurs**. Välj **BLOB** som **mål under resurs**.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. På fliken **konfiguration** väljer du det **virtuella nätverket** och **under nätet** för lagrings kontots privata slut punkt.  

    > [!Note]
    > Det virtuella nätverket måste innehålla ExpressRoute Gateway-slutpunkten eller vara ansluten till det virtuella nätverket med ExpressRoute-gatewayen. 

    I avsnittet **privat DNS-integrering** väljer du **Ja** och integrerar med en privat DNS-zon. Att välja **Ja** länkar automatiskt DNS-zonen till det valda virtuella nätverket och lägger till de DNS-poster som krävs för DNS-matchning av nya IP-adresser och fullständiga domän namn som skapats för den privata slut punkten. Läs mer om [privata DNS-zoner.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Du kan också lägga till **taggar** för din privata slut punkt.  

    e. Fortsätt att **Granska + skapa** när du har skrivit in information. När verifieringen är klar väljer du **skapa** för att skapa den privata slut punkten.

    > [!Note]
    > Om användaren som skapar den privata slut punkten också är ägare till lagrings kontot, kommer den privata slut punkten automatiskt att godkännas. Annars måste ägaren godkänna den privata slut punkten för användning. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Skapa privata DNS-zoner och Lägg till DNS-poster manuellt (valfritt)

Om du inte valde alternativet att integrera med en privat DNS-zon när den privata slut punkten skapas, följer du stegen i det här avsnittet för att skapa en privat DNS-zon manuellt. 

> [!Note]
> Om du valde **Ja** för att integrera med en privat DNS-zon kan du hoppa över det här avsnittet. 

1. Skapa en privat DNS-zon. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  På sidan **privat DNS zoner** väljer du knappen **+ Lägg** till för att börja skapa en ny zon.  
    b.  Fyll i den information som krävs på sidan **skapa privat DNS-zon** . Ange namnet på den privata DNS-zonen som _privatelink_. blob.Core.Windows.net. c. Fortsätt till fliken **Granska + skapa** för att granska och skapa DNS-zonen.

2. Länka den privata DNS-zonen till det virtuella nätverket.  

    Den privata DNS-zon som skapades ovan måste länkas till det virtuella nätverk som den privata slut punkten är kopplad till.

    a. Gå till den privata DNS-zon som skapades i föregående steg och navigera till virtuella nätverks länkar till vänster på sidan. Klicka på knappen **+ Lägg till** .   
    b. Fyll i den information som krävs. Fälten **prenumeration** och **virtuell nätverk** måste fyllas i med motsvarande information om det virtuella nätverk där din privata slut punkt är ansluten. De andra fälten kan vara kvar som de är.

3. Nästa steg är att lägga till DNS-poster i DNS-zonen. Lägg till en post för lagrings kontots fullständigt kvalificerade domän namn i din privata DNS-zon.

    a. Gå till din privata DNS-zon och gå till **översikts** avsnittet till vänster på sidan. Välj **+ post** uppsättning för att börja lägga till poster.  

    b. På sidan **Lägg till uppsättning av poster** lägger du till en post för det fullständigt kvalificerade domän namnet och den privata IP-adressen som en typ post.

> [!Important]
> Du kan behöva ytterligare DNS-inställningar för att matcha den privata IP-adressen för lagrings kontots privata slut punkt från käll miljön. [Läs den här artikeln](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) för att förstå den DNS-konfiguration som krävs.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Replikera data med hjälp av en ExpressRoute-krets med Microsoft-peering

Du kan använda Microsoft-peering eller en befintlig offentlig peering-domän (inaktuell för nya ExpressRoute-anslutningar) för att dirigera replikeringstrafiken genom en ExpressRoute-krets som illustreras i diagrammet nedan.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Även om replikeringsdata som går över till Microsoft-peer-kretsen, behöver du fortfarande Internet anslutning från den lokala platsen för annan kommunikation (kontroll plan) med tjänsten Azure Migrate. Det finns vissa ytterligare URL: er som inte kan nås via ExpressRoute, som Replication-enheten/Hyper-V-värden behöver åtkomst till för att dirigera processen för replikering. Du kan granska URL-kraven baserat på migrerings scenariot, [VMware agent-migreringar](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) eller [agentbaserade migreringar](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance).  

Om du använder en proxyserver på din lokala plats och vill använda ExpressRoute för replikeringstrafiken måste du konfigurera ett kringgåt tillägg för relevanta URL: er på den lokala enheten. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Konfigurera regler för att kringgå proxy på Azure Migrate-installationen (för VMware-agent utan migrering)

1. Logga in (fjärr skrivbord) till Azure Migrate-enheten.   
2. Öppna filen C:/program data/MicrosoftAzure/config/appliance.jsmed anteckningar.
3. I filen ändrar du raden med texten "EnableProxyBypassList": "false" till "EnableProxyBypassList": "true". Spara ändringarna och starta om enheten.
4. När du har startat om kan du se alternativet för att kringgå proxy i webbappens användar gränssnitt när du öppnar Konfigurations hanteraren för-enheten. Lägg till URL: erna nedan i listan över Återanvänd proxy.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Konfigurera regler för att kringgå proxy på replikerings enheten (för agentbaserade migreringar)

Följ stegen nedan för att konfigurera listan över återanvändade proxyservrar på konfigurations servern och process servrarna:

1. [Hämta PsExec-verktyget](https://docs.microsoft.com/sysinternals/downloads/psexec) för att få åtkomst till systemets användar kontext.
2. Öppna Internet Explorer i system användar kontext genom att köra följande kommando rad PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
3. Lägg till proxyinställningar i IE.
4. I listan över undantag lägger du till Azure Storage URL. *. blob. Core. Windows. net.  

Ovanstående regler för kringgående säkerställer att replikeringstrafiken kan flöda genom ExpressRoute medan hanterings kommunikationen kan gå via proxyservern för Internet.  

Dessutom måste du annonsera vägar i flödes filtret för följande BGP-communities för att den Azure Migrate replikeringstrafik ska passera en ExpressRoute-krets i stället för Internet.  

- Regional BGP community för Azures käll Azure-region (Azure Migrate projekt region)
- Regional BGP-community för Azure-regionen (region för migrering)
- BGP-communityn för Azure Active Directory (12076:5060)

Lär dig mer om [flödes filter](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) och listan med [BGP-communities för ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp). 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [ExpressRoute-kretsar](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- Läs mer om [ExpressRoute-routningsdomäner](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- Läs mer om [privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).