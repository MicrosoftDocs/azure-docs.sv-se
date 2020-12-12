---
title: 'Självstudie: krav för en tillgänglighets grupp'
description: Den här självstudien visar hur du konfigurerar förutsättningarna för att skapa en SQL Server Always on-tillgänglighetsgrupper på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a9a2f9d999a51f2b4600e8379d4a8913675b338
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360024"
---
# <a name="tutorial-prerequisites-for-creating-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Självstudie: förutsättningar för att skapa tillgänglighets grupper på SQL Server på Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här självstudien visas hur du slutför kraven för att skapa en [SQL Server Always on-tillgänglighetsgrupper på Azure Virtual Machines (VM)](availability-group-manually-configure-tutorial.md). När du har slutfört kraven har du en domänkontrollant, två SQL Server virtuella datorer och en vittnes server i en enda resurs grupp.

Även om den här artikeln konfigurerar tillgänglighets grupps miljön manuellt, är det också möjligt att göra detta med hjälp av [Azure Portal](availability-group-azure-portal-configure.md), [POWERSHELL, Azure CLI](availability-group-az-commandline-configure.md)eller [Azures snabb starts mallar](availability-group-quickstart-template-configure.md) . 

**Tids uppskattning**: det kan ta några timmar att slutföra kraven. Mycket av den här gången har du ägnat åt att skapa virtuella datorer.

Följande diagram illustrerar vad du skapar i självstudien.

![Tillgänglighetsgrupp](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Granska dokumentation om tillgänglighets grupp

Den här självstudien förutsätter att du har en grundläggande förståelse för SQL Server Always on-tillgänglighetsgrupper. Om du inte är bekant med den här tekniken kan du läsa [Översikt över Always on Availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).


## <a name="create-an-azure-account"></a>Skapa ett Azure-konto

Du behöver ett Azure-konto. Du kan [öppna ett kostnads fritt Azure-konto](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) eller [Aktivera förmåner för Visual Studio-prenumeranter](/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **+** om du vill skapa ett nytt objekt i portalen.

   ![Nytt objekt](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. Skriv **resurs grupp** i fönstret **Marketplace** -sökning.

   ![Resursgrupp](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. Välj **resurs grupp**.
5. Välj **Skapa**.
6. Under **resurs grupp namn**, anger du ett namn för resurs gruppen. Skriv till exempel **SQL-ha-RG**.
7. Om du har flera Azure-prenumerationer kontrollerar du att prenumerationen är den Azure-prenumeration som du vill skapa tillgänglighets gruppen i.
8. Välj en plats. Platsen är den Azure-region där du vill skapa tillgänglighets gruppen. I den här artikeln skapas alla resurser på en Azure-plats.
9. Kontrol lera att **Fäst på instrument panelen** är markerat. Med den här valfria inställningen placeras en genväg för resurs gruppen på Azure Portal instrument panelen.

   ![Genväg till resurs grupp för Azure Portal](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Välj **skapa** för att skapa resurs gruppen.

Azure skapar resurs gruppen och fäster en genväg till resurs gruppen i portalen.

## <a name="create-the-network-and-subnets"></a>Skapa nätverket och undernät

Nästa steg är att skapa nätverk och undernät i Azure-resurs gruppen.

Lösningen använder ett virtuellt nätverk med två undernät. [Översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md) innehåller mer information om nätverk i Azure.

Skapa det virtuella nätverket i Azure Portal:

1. I resurs gruppen väljer du **+ Lägg till**. 

   ![Nytt objekt](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Sök efter **virtuellt nätverk**.

     ![Sök i virtuellt nätverk](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Välj **virtuellt nätverk**.
4. I det **virtuella nätverket** väljer du distributions modellen för **Resource Manager** och väljer sedan **skapa**.

    I följande tabell visas inställningarna för det virtuella nätverket:

   | **Fält** | Värde |
   | --- | --- |
   | **Namn** |autoHAVNET |
   | **Adressutrymme** |10.33.0.0/24 |
   | **Namn på undernät** |Admin |
   | **Adressintervall för undernätet** |10.33.0.0/29 |
   | **Prenumeration** |Ange den prenumeration som du vill använda. **Prenumerationen** är tom om du bara har en prenumeration. |
   | **Resursgrupp** |Välj **Använd befintlig** och välj namnet på resurs gruppen. |
   | **Plats** |Ange Azure-platsen. |

   Adress utrymmet och under nätets adress intervall kan skilja sig från tabellen. Beroende på din prenumeration föreslår portalen ett tillgängligt adress utrymme och motsvarande adress intervall för under nätet. Om det inte finns tillräckligt med adress utrymme använder du en annan prenumeration.

   Exemplet använder under näts namnets **administratör**. Det här under nätet är för domän kontrol Lanterna.

5. Välj **Skapa**.

   ![Konfigurera det virtuella nätverket](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

Azure återgår till portalens instrument panel och meddelar dig när det nya nätverket skapas.

### <a name="create-a-second-subnet"></a>Skapa ett andra undernät

Det nya virtuella nätverket har ett undernät, med namnet **admin**. Domän kontrol Lanterna använder det här under nätet. De virtuella datorerna i SQL Server använder ett andra undernät med namnet **SQL**. Konfigurera det här under nätet:

1. På instrument panelen väljer du den resurs grupp som du skapade, **SQL-ha-RG**. Leta upp nätverket i resurs gruppen under **resurser**.

    Om **SQL-ha-RG** inte visas söker du efter det genom att välja **resurs grupper** och filtrera efter resurs gruppens namn.

2. Välj **autoHAVNET** i listan över resurser. 
3. I **autoHAVNET** virtuella nätverk under **Inställningar** väljer du **undernät**.

    Anteckna det undernät som du redan har skapat.

   ![Observera det undernät som du redan har skapat](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. Om du vill skapa ett andra undernät väljer du **+ undernät**.
6. I **Lägg till undernät** konfigurerar du under nätet genom att skriva **sqlsubnet** under **namn**. Azure anger automatiskt ett giltigt **adress intervall**. Kontrol lera att det här adress intervallet innehåller minst 10 adresser. I en produktions miljö kan du behöva fler adresser.
7. Välj **OK**.

    ![Konfigurera undernät](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

Följande tabell sammanfattar inställningarna för nätverks konfiguration:

| **Fält** | Värde |
| --- | --- |
| **Namn** |**autoHAVNET** |
| **Adressutrymme** |Det här värdet beror på tillgängliga adress utrymmen i din prenumeration. Ett typiskt värde är 10.0.0.0/16. |
| **Namn på undernät** |**administratör** |
| **Adressintervall för undernätet** |Det här värdet beror på tillgängliga adress intervall i din prenumeration. Ett typiskt värde är 10.0.0.0/24. |
| **Namn på undernät** |**sqlsubnet** |
| **Adressintervall för undernätet** |Det här värdet beror på tillgängliga adress intervall i din prenumeration. Ett typiskt värde är 10.0.1.0/24. |
| **Prenumeration** |Ange den prenumeration som du vill använda. |
| **Resursgrupp** |**SQL-HA-RG** |
| **Plats** |Ange samma plats som du valde för resurs gruppen. |

## <a name="create-availability-sets"></a>Skapa tillgänglighetsuppsättningar

Innan du skapar virtuella datorer måste du skapa tillgänglighets uppsättningar. Tillgänglighets uppsättningar minskar stillestånds tiden för planerade eller oplanerade underhålls händelser. En Azure-tillgänglighets uppsättning är en logisk grupp resurser som Azure placerar på fysiska fel domäner och uppdaterings domäner. En feldomän ser till att medlemmarna i tillgänglighets uppsättningen har separata strömförsörjnings-och nätverks resurser. En uppdaterings domän garanterar att medlemmarna i tillgänglighets uppsättningen inte har upprättats för underhåll på samma gång. Mer information finns i [Hantera tillgängligheten för virtuella datorer](../../../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Du behöver två tillgänglighets uppsättningar. En är för domän kontrol Lanterna. Den andra är för SQL Server virtuella datorer.

Om du vill skapa en tillgänglighets uppsättning går du till resurs gruppen och väljer **Lägg till**. Filtrera resultaten genom att **Ange tillgänglighets uppsättning**. Välj **tillgänglighets uppsättning** i resultaten och välj sedan **skapa**.

Konfigurera två tillgänglighets uppsättningar enligt parametrarna i följande tabell:

| **Fält** | Tillgänglighets uppsättning för domänkontrollant | SQL Server tillgänglighets uppsättning |
| --- | --- | --- |
| **Namn** |adavailabilityset |sqlavailabilityset |
| **Resursgrupp** |SQL-HA-RG |SQL-HA-RG |
| **Feldomäner** |3 |3 |
| **Uppdateringsdomäner** |5 |3 |

När du har skapat tillgänglighets uppsättningarna återgår du till resurs gruppen i Azure Portal.

## <a name="create-domain-controllers"></a>Skapa domänkontrollanter

När du har skapat nätverket, undernät och tillgänglighets uppsättningar är du redo att skapa de virtuella datorerna för domän kontrol Lanterna.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Skapa virtuella datorer för domän kontrol Lanterna

Om du vill skapa och konfigurera domän kontrol Lanterna går du tillbaka till resurs gruppen **SQL-ha-RG** .

1. Välj **Lägg till**. 
2. Skriv **Windows Server 2016 Data Center**.
3. Välj **Windows Server 2016 Data Center**. I **Windows Server 2016 Data Center** kontrollerar du att distributions modellen är **Resource Manager** och väljer sedan **skapa**. 

Upprepa föregående steg för att skapa två virtuella datorer. Namnge de två virtuella datorerna:

* AD – primär domänkontrollant
* AD-sekundär domänkontrollant

  > [!NOTE]
  > Den virtuella datorn med den **sekundära domänkontrollanten** är valfri, för att ge hög tillgänglighet för Active Directory Domain Services.
  >

I följande tabell visas inställningarna för dessa två datorer:

| **Fält** | Värde |
| --- | --- |
| **Namn** |Första domänkontrollant: *AD-Primary-DC*.</br>Andra domänkontrollanten *AD-Secondary-DC*. |
| **Typ av virtuell dator disk** |SSD |
| **Användarnamn** |DomainAdmin |
| **Lösenord** |Contoso! 0000 |
| **Prenumeration** |*Din prenumeration* |
| **Resursgrupp** |SQL-HA-RG |
| **Plats** |*Din plats* |
| **Storlek** |DS1_V2 |
| **Storage** | **Använd Managed disks**  -  **Ja** |
| **Virtuellt nätverk** |autoHAVNET |
| **Undernät** |administratör |
| **Offentlig IP-adress** |*Samma namn som den virtuella datorn* |
| **Nätverkssäkerhetsgrupp** |*Samma namn som den virtuella datorn* |
| **Tillgänglighetsuppsättning** |adavailabilityset </br>**Fel domäner**: 2 </br>**Uppdaterings domäner**: 2|
| **Diagnostik** |Enabled |
| **Lagringskonto för diagnostik** |*Skapas automatiskt* |

   >[!IMPORTANT]
   >Du kan bara placera en virtuell dator i en tillgänglighets uppsättning när du skapar den. Du kan inte ändra tillgänglighets uppsättningen när en virtuell dator har skapats. Se [Hantera tillgängligheten för virtuella datorer](../../../virtual-machines/manage-availability.md).

Azure skapar de virtuella datorerna.

När de virtuella datorerna har skapats konfigurerar du domänkontrollanten.

### <a name="configure-the-domain-controller"></a>Konfigurera domänkontrollanten

I följande steg konfigurerar du den **AD-primära DC-** datorn som en domänkontrollant för Corp.contoso.com.

1. Öppna resurs gruppen **SQL-ha-RG** i portalen och välj datorn **AD-Primary-DC** . På **AD-Primary-DC** väljer du **Anslut** för att öppna en RDP-fil för fjärr skrivbords åtkomst.

    ![Ansluta till en virtuell dator](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. Logga in med ditt konfigurerade administratörs konto (**\DomainAdmin**) och lösen ord (**contoso! 0000**).
3. Som standard visas **Serverhanteraren** instrument panelen.
4. Välj länken **Lägg till roller och funktioner** på instrument panelen.

    ![Serverhanteraren – Lägg till roller](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. Välj **Nästa** tills du kommer till avsnittet **Server roller** .
6. Välj roller för **Active Directory Domain Services** och **DNS-Server** . När du uppmanas till det lägger du till ytterligare funktioner som krävs av dessa roller.

   > [!NOTE]
   > Windows varnar dig om att det inte finns någon statisk IP-adress. Om du testar konfigurationen väljer du **Fortsätt**. För produktions scenarier anger du IP-adressen till statisk i Azure Portal eller [använder PowerShell för att ange den statiska IP-adressen för](/previous-versions/azure/virtual-network/virtual-networks-reserved-private-ip)domänkontrollantens dator.
   >

    ![Dialog rutan Lägg till roller](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. Välj **Nästa** tills du kommer till **bekräftelse** avsnittet. Markera kryss rutan **starta om mål servern automatiskt vid behov** .
8. Välj **Installera**.
9. När funktionerna har installerats går du tillbaka till instrument panelen för **Serverhanteraren** .
10. Välj alternativet ny **AD DS** i rutan till vänster.
11. Välj länken **mer** i det gula varnings fältet.

    ![AD DS-dialogruta på den virtuella DNS-servern](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. I kolumnen **åtgärd** i dialog rutan **all information om Server aktivitet** väljer **du befordra den här servern till en** domänkontrollant.
13. I **konfigurations guiden för Active Directory Domain Services** använder du följande värden:

    | **Sida** | Inställning |
    | --- | --- |
    | **Distributionskonfiguration** |**Lägg till en ny skog**<br/> **Rot domän namn** = Corp.contoso.com |
    | **Alternativ för domänkontrollant** |**DSRM-lösenord** = contoso! 0000<br/>**Bekräfta lösen ord** = contoso! 0000 |

14. Välj **Nästa** för att gå igenom de andra sidorna i guiden. På sidan **krav kontroll** kontrollerar du att följande meddelande visas: **alla nödvändiga kontroller lyckades**. Du kan granska tillämpliga varnings meddelanden, men det är möjligt att fortsätta med installationen.
15. Välj **Installera**. Den virtuella datorn för **AD-primär domänkontrollant** startas om automatiskt.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Anteckna IP-adressen för den primära domänkontrollanten

Använd den primära domänkontrollanten för DNS. Anteckna den primära domänkontrollantens IP-adress.

Ett sätt att hämta den primära domänkontrollantens IP-adress är via Azure Portal.

1. Öppna resurs gruppen på Azure Portal.

2. Välj den primära domänkontrollanten.

3. Välj **nätverks gränssnitt** på den primära domänkontrollanten.

![Nätverksgränssnitt](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Anteckna den här serverns privata IP-adress.

### <a name="configure-the-virtual-network-dns"></a>Konfigurera det virtuella nätverkets DNS

När du har skapat den första domänkontrollanten och aktiverat DNS på den första servern konfigurerar du det virtuella nätverket så att det använder den här servern för DNS.

1. I Azure Portal väljer du på det virtuella nätverket.

2. Under **Inställningar** väljer du **DNS-Server**.

3. Välj **anpassad** och ange den privata IP-adressen för den primära domänkontrollanten.

4. Välj **Spara**.

### <a name="configure-the-second-domain-controller"></a>Konfigurera den andra domänkontrollanten

När den primära domänkontrollanten har startats om kan du konfigurera den andra domänkontrollanten. Det här valfria steget är för hög tillgänglighet. Följ dessa steg om du vill konfigurera den andra domänkontrollanten:

1. I portalen öppnar du resurs gruppen **SQL-ha-RG** och väljer den **AD-sekundära DC-** datorn. På **AD-Secondary-DC** väljer du **Anslut** för att öppna en RDP-fil för fjärr skrivbords åtkomst.
2. Logga in på den virtuella datorn med ditt konfigurerade administratörs konto (**BUILTIN\DomainAdmin**) och lösen ord (**contoso! 0000**).
3. Ändra önskad DNS-serveradress till adressen för domänkontrollanten.
4. I **nätverks-och delnings Center** väljer du nätverks gränssnittet.

   ![Nätverksgränssnitt](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Välj **Egenskaper**.
6. Välj **Internet Protocol version 4 (TCP/IPv4)** och välj sedan **Egenskaper**.
7. Välj **Använd följande DNS-serveradresser** och ange adressen för den primära domänkontrollanten i **önskad DNS-Server**.
8. Välj **OK** och sedan **Stäng** för att genomföra ändringarna. Nu kan du ansluta den virtuella datorn till **Corp.contoso.com**.

   >[!IMPORTANT]
   >Om du förlorar anslutningen till fjärr skrivbordet när du har ändrat DNS-inställningen går du till Azure Portal och startar om den virtuella datorn.

9. Öppna **Serverhanteraren instrument panelen** från fjärr skrivbordet till den sekundära domänkontrollanten.
10. Välj länken **Lägg till roller och funktioner** på instrument panelen.

    ![Serverhanteraren – Lägg till roller](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Välj **Nästa** tills du kommer till avsnittet **Server roller** .
12. Välj roller för **Active Directory Domain Services** och **DNS-Server** . När du uppmanas till det lägger du till ytterligare funktioner som krävs av dessa roller.
13. När funktionerna har installerats går du tillbaka till instrument panelen för **Serverhanteraren** .
14. Välj alternativet ny **AD DS** i rutan till vänster.
15. Välj länken **mer** i det gula varnings fältet.
16. I kolumnen **åtgärd** i dialog rutan **all information om Server aktivitet** väljer **du befordra den här servern till en** domänkontrollant.
17. Under **distributions konfiguration** väljer **du Lägg till en domänkontrollant i en befintlig domän**.

    ![Distributions konfiguration](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. Klicka på **Välj**.
19. Anslut med hjälp av administratörs kontot (**Corp. CONTOSO. COM\domainadmin**) och lösen ord (**contoso! 0000**).
20. I **Välj en domän från skogen** väljer du din domän och väljer sedan **OK**.
21. Använd standardvärdena i **alternativ** för domänkontrollant och ange ett DSRM-lösenord.

    >[!NOTE]
    >Sidan **DNS-alternativ** kan varna dig om att det inte går att skapa en delegering för den här DNS-servern. Du kan ignorera den här varningen i icke-produktions miljöer.
    >

22. Välj **Nästa** tills dialog rutan når **krav** kontrollen. Välj sedan **Installera**.

När servern har slutfört konfigurations ändringarna startar du om servern.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Lägg till den privata IP-adressen till den andra domänkontrollanten i VPN-DNS-servern

I Azure Portal, under virtuellt nätverk, ändrar du DNS-servern så att den inkluderar IP-adressen för den sekundära domänkontrollanten. Med den här inställningen kan DNS-tjänsten redundans.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a> Konfigurera domän kontona

I nästa steg konfigurerar du Active Directory-konton. I följande tabell visas kontona:

| |Installations konto<br/> |SQLServer-0 <br/>SQL Server-och SQL-agentens tjänst konto |SQLServer-1<br/>SQL Server-och SQL-agentens tjänst konto
| --- | --- | --- | ---
|**Förnamn** |Installera |SQLSvc1 | SQLSvc2
|**Användar-SamAccountName** |Installera |SQLSvc1 | SQLSvc2

Använd följande steg för att skapa varje konto.

1. Logga in på datorn **AD-Primary-DC** .
2. I **Serverhanteraren** väljer du **verktyg** och väljer sedan **Active Directory Administrationscenter**.   
3. Välj **Corp (lokal)** i den vänstra rutan.
4. Välj **ny** i rutan till höger **uppgifter** och välj sedan **användare**.

   ![Active Directory Administrationscenter](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Ange ett komplext lösen ord för varje konto.<br/> För icke-produktions miljöer anger du att användar kontot aldrig upphör att gälla.
   >

5. Välj **OK** för att skapa användaren.
6. Upprepa föregående steg för var och en av de tre kontona.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Bevilja de behörigheter som krävs för installations kontot

1. I **Active Directory Administrationscenter** väljer du **Corp (lokal)** i den vänstra rutan. Välj sedan **Egenskaper** i fönstret till höger **uppgifter** .

    ![Egenskaper för CORP-användare](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. Välj **tillägg** och välj sedan knappen **Avancerat** på fliken **säkerhet** .
3. I dialog rutan **avancerade säkerhets inställningar för Corp** väljer du **Lägg till**.
4. Klicka på **Välj ett huvud konto**, Sök efter **CORP\Install** och välj sedan **OK**.
5. Markera kryss rutan **Läs alla egenskaper** .

6. Markera kryss rutan **skapa dator objekt** .

     ![Corp-användar behörigheter](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. Välj **OK** och sedan **OK** igen. Stäng fönstret **Corp** -egenskaper.

Nu när du har konfigurerat Active Directory och användar objekt, skapar du två SQL Server virtuella datorer och en virtuell vittnes Server. Anslut sedan alla tre till domänen.

## <a name="create-sql-server-vms"></a>Skapa SQL Server virtuella datorer

Skapa tre ytterligare virtuella datorer. Lösningen kräver två virtuella datorer med SQL Server instanser. En tredje virtuell dator fungerar som ett vittne. Windows Server 2016 kan använda ett [moln vittne](/windows-server/failover-clustering/deploy-cloud-witness). I den här artikeln används dock en virtuell dator för ett vittne för konsekvens med tidigare operativ system.  

Innan du fortsätter bör du tänka på följande design beslut.

* **Lagring – Azure-Managed Disks**

   Använd Azure Managed Disks för lagring av virtuella datorer. Microsoft rekommenderar Managed Disks för SQL Server virtuella datorer. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Översikt över Azure Managed Disks](../../../virtual-machines/managed-disks-overview.md). Information om hanterade diskar i en tillgänglighets uppsättning finns i [använda Managed disks för virtuella datorer i en tillgänglighets uppsättning](../../../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Nätverks-privata IP-adresser i produktion**

   I den här självstudien används offentliga IP-adresser för de virtuella datorerna. En offentlig IP-adress möjliggör fjärr anslutning direkt till den virtuella datorn via Internet och gör konfigurations stegen enklare. I produktions miljöer rekommenderar Microsoft endast privata IP-adresser för att minska sårbarheten för den SQL Server instansens VM-resurs.

* **Nätverk – rekommendera ett enda nätverkskort per server** 

Använd ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans, vilket gör att ytterligare nätverkskort och undernät inte behövs på ett gäst kluster i en virtuell Azure-dator. I kluster verifierings rapporten visas en varning om att noderna bara kan kommas åt i ett enda nätverk. Du kan ignorera den här varningen på Azure Virtual Machine Guest-kluster.

### <a name="create-and-configure-the-sql-server-vms"></a>Skapa och konfigurera de virtuella datorerna för SQL Server

Skapa sedan tre virtuella datorer – två SQL Server virtuella datorer och en virtuell dator för ytterligare en klusternod. Om du vill skapa var och en av de virtuella datorerna går du tillbaka till resurs gruppen **SQL-ha-RG** och väljer sedan **Lägg till**. Sök efter lämpligt Galleri objekt, Välj **virtuell dator** och välj sedan **från Galleri**. Använd informationen i följande tabell som hjälp för att skapa de virtuella datorerna:


| Sida | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Välj lämpligt Galleri objekt |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |
| **Grundläggande** konfiguration av virtuella datorer |**Namn** = kluster-FSW<br/>**Användar namn** = DomainAdmin<br/>**Lösen ord** = contoso! 0000<br/>**Prenumeration** = din prenumeration<br/>**Resurs grupp** = SQL-ha-rg<br/>**Plats** = din Azure-plats |**Namn** = SQLServer-0<br/>**Användar namn** = DomainAdmin<br/>**Lösen ord** = contoso! 0000<br/>**Prenumeration** = din prenumeration<br/>**Resurs grupp** = SQL-ha-rg<br/>**Plats** = din Azure-plats |**Namn** = SQLServer-1<br/>**Användar namn** = DomainAdmin<br/>**Lösen ord** = contoso! 0000<br/>**Prenumeration** = din prenumeration<br/>**Resurs grupp** = SQL-ha-rg<br/>**Plats** = din Azure-plats |
| Konfigurations **storlek** för virtuell dator |**Size** = ds1 \_ v2 (1 VCPU, 3,5 GB) |**Size** = DS2 \_ v2 (2 virtuella processorer, 7 GB)</br>Storleken måste ha stöd för SSD-lagring (stöd för Premium-diskar). )) |**Size** = DS2 \_ v2 (2 virtuella processorer, 7 GB) |
| Konfigurations **Inställningar** för virtuell dator |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet (10.1.1.0/24)<br/>**Offentlig IP-adress** genereras automatiskt.<br/>**Nätverks säkerhets grupp** = ingen<br/>**Övervaka diagnostik** = aktive rad<br/>**Lagrings konto för diagnostik** = Använd ett automatiskt genererat lagrings konto<br/>**Tillgänglighets uppsättning** = sqlAvailabilitySet<br/> |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet (10.1.1.0/24)<br/>**Offentlig IP-adress** genereras automatiskt.<br/>**Nätverks säkerhets grupp** = ingen<br/>**Övervaka diagnostik** = aktive rad<br/>**Lagrings konto för diagnostik** = Använd ett automatiskt genererat lagrings konto<br/>**Tillgänglighets uppsättning** = sqlAvailabilitySet<br/> |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet (10.1.1.0/24)<br/>**Offentlig IP-adress** genereras automatiskt.<br/>**Nätverks säkerhets grupp** = ingen<br/>**Övervaka diagnostik** = aktive rad<br/>**Lagrings konto för diagnostik** = Använd ett automatiskt genererat lagrings konto<br/>**Tillgänglighets uppsättning** = sqlAvailabilitySet<br/> |
| Konfigurations **SQL Server inställningar** för virtuell dator |Inte tillämpligt |**SQL-anslutning** = privat (inom Virtual Network)<br/>**Port** = 1433<br/>**SQL-autentisering** = inaktivera<br/>**Lagrings konfiguration** = allmänt<br/>**Automatiserad uppdatering** = söndag vid 2:00<br/>**Automatisk säkerhets kopiering** = inaktive rad</br>**Azure Key Vault integration** = inaktive rad |**SQL-anslutning** = privat (inom Virtual Network)<br/>**Port** = 1433<br/>**SQL-autentisering** = inaktivera<br/>**Lagrings konfiguration** = allmänt<br/>**Automatiserad uppdatering** = söndag vid 2:00<br/>**Automatisk säkerhets kopiering** = inaktive rad</br>**Azure Key Vault integration** = inaktive rad |

<br/>

> [!NOTE]
> Dator storlekarna som föreslås här är avsedda för att testa tillgänglighets grupper i Azure Virtual Machines. För bästa prestanda för produktions arbets belastningar, se rekommendationer för SQL Server dator storlek och konfiguration i [metod tips för prestanda för SQL Server i Azure Virtual Machines](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

När de tre virtuella datorerna är helt etablerade måste du koppla dem till **Corp.contoso.com** -domänen och bevilja CORP\Install administrativa rättigheter till datorerna.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Anslut servrarna till domänen

Nu kan du ansluta de virtuella datorerna till **Corp.contoso.com**. Utför följande steg för både SQL Server virtuella datorer och fil resurs vittnes servern:

1. Fjärrans luta till den virtuella datorn med **BUILTIN\DomainAdmin**.
2. I **Serverhanteraren** väljer du **Lokal server**.
3. Välj **arbets grupps** länken.
4. I avsnittet **dator namn** väljer du **ändra**.
5. Markera kryss rutan **domän** och skriv **Corp.contoso.com** i text rutan. Välj **OK**.
6. I dialog rutan popup för **Windows-säkerhet** anger du autentiseringsuppgifterna för standard domän administratörs kontot (**CORP\DomainAdmin**) och lösen ordet (**contoso! 0000**).
7. När du ser meddelandet "Välkommen till corp.contoso.com-domänen" väljer du **OK**.
8. Välj **Stäng** och välj sedan **starta om nu** i popup-dialogrutan.

## <a name="add-accounts"></a>Lägga till konton

Lägg till installations kontot som administratör på varje virtuell dator, bevilja behörighet till installations kontot och lokala konton inom SQL Server och uppdatera SQL Servers tjänst kontot. 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Lägg till Corp\Install-användaren som administratör på varje virtuell kluster dator

När varje virtuell dator har startats om som medlem i domänen lägger du till **CORP\Install** som medlem i den lokala administratörs gruppen.

1. Vänta tills den virtuella datorn har startats om och starta sedan RDP-filen igen från den primära domänkontrollanten för att logga in på **SQLServer-0** med hjälp av **CORP\DomainAdmin** -kontot.

   >[!TIP]
   >Kontrol lera att du loggar in med domän administratörs kontot. I föregående steg använde du det inbyggda administratörs kontot. Nu när servern finns i domänen använder du domän kontot. I RDP-sessionen anger du *domän* \\ *användar namn*.
   >

2. I **Serverhanteraren** väljer du **verktyg** och sedan **dator hantering**.
3. I fönstret **dator hantering** expanderar du **lokala användare och grupper** och väljer sedan **grupper**.
4. Dubbelklicka på gruppen **Administratörer** .
5. I dialog rutan **Administratörer egenskaper** väljer du knappen **Lägg till** .
6. Ange användar- **CORP\Install** och välj sedan **OK**.
7. Klicka på **OK** för att stänga dialog rutan **Administratörs egenskaper** .
8. Upprepa föregående steg för **SQLServer-1** och **cluster-FSW**.


### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Skapa en inloggning på varje SQL Server VM för installations kontot

Använd installations kontot (CORP\install) för att konfigurera tillgänglighets gruppen. Det här kontot måste vara medlem i den fasta Server rollen **sysadmin** på varje SQL Server VM. Följande steg skapar en inloggning för installations kontot:

1. Anslut till servern via Remote Desktop Protocol (RDP) med hjälp av *\<MachineName\> \DomainAdmin* -kontot.

1. Öppna SQL Server Management Studio och Anslut till den lokala SQL Server-instansen.

1. I **Object Explorer** väljer du **säkerhet**.

1. Högerklicka på **inloggningar**. Välj **ny inloggning**.

1. I **Login - New** (Inloggning – ny) väljer du **Sök**.

1. Välj **platser**.

1. Ange domän administratörens autentiseringsuppgifter för nätverket.

1. Använd installations kontot (CORP\install).

1. Ange att inloggningen ska vara medlem i den fasta Server rollen **sysadmin** .

1. Välj **OK**.

Upprepa föregående steg på den andra SQL Server VM.

### <a name="configure-system-account-permissions"></a>Konfigurera system konto behörigheter

Om du vill skapa ett konto för system kontot och bevilja rätt behörighet, utför du följande steg på varje SQL Server instans:

1. Skapa ett konto för `[NT AUTHORITY\SYSTEM]` på varje SQL Server instans. Följande skript skapar det här kontot:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Ge följande behörigheter för `[NT AUTHORITY\SYSTEM]` varje SQL Server-instans:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Följande skript ger följande behörigheter:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Ange SQL Server tjänst konton

Ange SQL Server-tjänstkontot på varje SQL Server VM. Använd de konton som du skapade när du konfigurerade domän kontona.

1. Öppna **SQL Server Configuration Manager**.
2. Högerklicka på tjänsten SQL Server och välj sedan **Egenskaper**.
3. Ange kontot och lösen ordet.
4. Upprepa de här stegen på andra SQL Server VM.  

För SQL Server tillgänglighets grupper måste varje SQL Server VM köras som ett domän konto.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Lägg till funktioner för redundanskluster till båda SQL Server virtuella datorer

Om du vill lägga till funktioner för redundanskluster utför du följande steg på båda SQL Server virtuella datorer:

1. Anslut till den SQL Server virtuella datorn via Remote Desktop Protocol (RDP) med hjälp av *CORP\install* -kontot. Öppna **Serverhanteraren-instrumentpanelen**.
2. Välj länken **Lägg till roller och funktioner** på instrument panelen.

    ![Serverhanteraren – Lägg till roller](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. Välj **Nästa** tills du kommer till avsnittet **Server funktioner** .
4. I **funktioner** väljer du **redundanskluster**.
5. Lägg till eventuella ytterligare nödvändiga funktioner.
6. Välj **Installera** för att lägga till funktionerna.

Upprepa stegen på den andra SQL Server VM.

  >[!NOTE]
  > Det här steget, tillsammans med att ansluta SQL Server de virtuella datorerna till redundansklustret, kan nu automatiseras med [Azure SQL CLI för virtuella datorer](./availability-group-az-commandline-configure.md) och [Azure snabb starts mallar](availability-group-quickstart-template-configure.md).
  >

### <a name="tuning-failover-cluster-network-thresholds"></a>Justera nätverks trösklar för redundanskluster

När du kör Windows-redundanskluster i virtuella Azure-datorer med SQL Server AlwaysOn, rekommenderas att ändra kluster inställningen till ett mer avslappnad övervaknings tillstånd.  Detta gör klustret mycket mer stabilt och tillförlitligt.  Mer information om detta finns i [IaaS med SQL AlwaysOn-finjustera tröskelvärden för redundanskluster](/windows-server/troubleshoot/iaas-sql-failover-cluster).


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a> Konfigurera brand väggen på varje SQL Server VM

Lösningen kräver att följande TCP-portar är öppna i brand väggen:

- **SQL Server VM**: port 1433 för en standard instans av SQL Server.
- **Avsökning av Azure Load Balancer:** Alla tillgängliga portar. I exemplen används ofta 59999.
- **Slut punkt för databas spegling:** Alla tillgängliga portar. I exemplen används ofta 5022.

Brand Väggs portarna måste vara öppna på båda SQL Server virtuella datorer.

Metoden för att öppna portarna beror på vilken brand Väggs lösning du använder. I nästa avsnitt beskrivs hur du öppnar portarna i Windows-brandväggen. Öppna de portar som krävs på alla SQL Server virtuella datorer.

### <a name="open-a-tcp-port-in-the-firewall"></a>Öppna en TCP-port i brand väggen

1. På Start skärmen för första SQL Server **startar** du **Windows-brandväggen med avancerad säkerhet**.
2. I den vänstra rutan väljer du **regler för inkommande trafik**. Välj **ny regel** i den högra rutan.
3. Välj **port** för **regel typ**.
4. För porten anger du **TCP** och anger lämpliga port nummer. Se följande exempel:

   ![SQL-brandvägg](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Välj **Nästa**.
6. På sidan **åtgärd** behåller **du Tillåt anslutningen** vald och väljer sedan **Nästa**.
7. Godkänn standardinställningarna på sidan **profil** och välj sedan **Nästa**.
8. På sidan **namn** anger du ett regel namn (till exempel **Azure lb PROBE**) i text rutan **namn** och väljer sedan **Slutför**.

Upprepa de här stegen på den andra SQL Server VM.


## <a name="next-steps"></a>Nästa steg

* [Skapa en SQL Server Always on-tillgänglighetsgrupper på Azure Virtual Machines](availability-group-manually-configure-tutorial.md)
