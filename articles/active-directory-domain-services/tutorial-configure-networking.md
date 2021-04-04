---
title: Självstudie – Konfigurera virtuella nätverk för Azure AD Domain Services | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar och konfigurerar ett virtuellt undernät i Azure eller nätverks-peering för en Azure Active Directory Domain Services hanterad domän med hjälp av Azure Portal.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618424"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Självstudie: Konfigurera virtuella nätverk för en Azure Active Directory Domain Services hanterad domän

För att tillhandahålla anslutning till användare och program distribueras en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän till ett virtuellt nätverk i Azure. Det här virtuella nätverkets undernät bör endast användas för de hanterade domän resurser som tillhandahålls av Azure-plattformen.

När du skapar dina egna virtuella datorer och program, bör de inte distribueras till samma undernät för virtuellt nätverk. I stället bör du skapa och distribuera dina program till ett separat virtuellt nätverks under nät eller i ett separat virtuellt nätverk som peer-kopplas till det virtuella Azure AD DS-nätverket.

I den här självstudien får du lära dig hur du skapar och konfigurerar ett dedikerat virtuellt nätverks under nät eller hur du peer-koppla ett annat nätverk till Azure AD DS-hanterad domäns virtuella nätverk.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förstå de virtuella nätverks anslutnings alternativen för domänanslutna resurser till Azure AD DS
> * Skapa ett IP-adressintervall och ytterligare ett undernät i det virtuella Azure AD DS-nätverket
> * Konfigurera peering för virtuellt nätverk till ett nätverk som är skilt från Azure AD DS

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att konfigurera Azure AD DS.
* Du behöver *deltagar* behörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Vid behov skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du och konfigurerar den hanterade domänen med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="application-workload-connectivity-options"></a>Anslutnings alternativ för program arbets belastning

I föregående självstudie skapade du en hanterad domän som använde vissa standard konfigurations alternativ för det virtuella nätverket. Dessa standard alternativ skapade ett virtuellt Azure-nätverk och ett virtuellt nätverks under nät. De Azure AD DS-domänkontrollanter som tillhandahåller hanterade domän tjänster är anslutna till det här virtuella nätverkets undernät.

När du skapar och kör virtuella datorer som behöver använda den hanterade domänen måste nätverks anslutningen tillhandahållas. Den här nätverks anslutningen kan tillhandahållas på något av följande sätt:

* Skapa ytterligare ett undernät för virtuellt nätverk i den hanterade domänens virtuella nätverk. Det här ytterligare under nätet är den plats där du skapar och ansluter dina virtuella datorer.
    * Eftersom de virtuella datorerna ingår i samma virtuella nätverk kan de automatiskt utföra namn matchning och kommunicera med Azure AD DS-domän kontrol Lanterna.
* Konfigurera peering för virtuella Azure-nätverk från den hanterade domänens virtuella nätverk till ett eller flera separata virtuella nätverk. Dessa separata virtuella nätverk är den plats där du skapar och ansluter till dina virtuella datorer.
    * När du konfigurerar peering för virtuella nätverk måste du också konfigurera DNS-inställningar för att använda namn matchning tillbaka till Azure AD DS-domän kontrol Lanterna.

Normalt använder du bara ett av dessa alternativ för nätverks anslutning. Valet är ofta att välja hur du vill hantera separata Azure-resurser.

* Om du vill hantera Azure AD DS och anslutna virtuella datorer som en resurs grupp kan du skapa ytterligare ett virtuellt nätverks under nät för virtuella datorer.
* Om du vill separera hanteringen av Azure AD DS och alla anslutna virtuella datorer kan du använda peering för virtuella nätverk.
    * Du kan också välja att använda peering för virtuella nätverk för att tillhandahålla anslutning till befintliga virtuella datorer i Azure-miljön som är anslutna till ett befintligt virtuellt nätverk.

I den här självstudien behöver du bara konfigurera ett sådant alternativ för anslutning till virtuellt nätverk.

Mer information om hur du planerar och konfigurerar det virtuella nätverket finns i [nätverks överväganden för Azure Active Directory Domain Services][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Skapa ett undernät för virtuellt nätverk

Som standard innehåller det virtuella Azure-nätverket som skapats med den hanterade domänen ett enda virtuellt nätverks under nät. Det här virtuella nätverkets undernät bör endast användas av Azure-plattformen för att tillhandahålla hanterade domän tjänster. Skapa ett ytterligare undernät för att skapa och använda dina egna virtuella datorer i det här virtuella Azure-nätverket.

Utför följande steg för att skapa ett virtuellt nätverks under nät för virtuella datorer och program arbets belastningar:

1. I Azure Portal väljer du resurs gruppen för din hanterade domän, till exempel *myResourceGroup*. I listan över resurser väljer du det virtuella standard nätverket, till exempel *aadds-VNet*.
1. I den vänstra menyn i fönstret virtuellt nätverk väljer du **adress utrymme**. Det virtuella nätverket skapas med ett enda adress utrymme på *10.0.2.0/24*, som används av standard under nätet.

    Lägg till ytterligare ett IP-adressintervall i det virtuella nätverket. Storleken på det här adress intervallet och det faktiska IP-adressintervall som ska användas beror på andra nätverks resurser som redan har distribuerats. IP-adressintervallet får inte överlappa några befintliga adress intervall i din Azure eller lokala miljö. Se till att du ändrar storlek på IP-adressintervallet tillräckligt stort för antalet virtuella datorer som du förväntar dig att distribuera till under nätet.

    I följande exempel läggs ytterligare ett IP-adressintervall för *10.0.3.0/24* till. När du är klar väljer du **Spara**.

    ![Lägg till ytterligare ett IP-adressintervall för virtuellt nätverk i Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Sedan väljer du **undernät** på menyn till vänster i fönstret virtuellt nätverk och väljer sedan **+ undernät** för att lägga till ett undernät.
1. Ange ett namn för under nätet, till exempel *arbets belastningar*. Om det behövs uppdaterar du **adress intervallet** om du vill använda en delmängd av det IP-adressintervall som kon figurer ATS för det virtuella nätverket i föregående steg. Lämna standardvärdena för alternativ som nätverks säkerhets grupp, routningstabell, tjänst slut punkter.

    I följande exempel skapas ett undernät med namnet *arbets belastningar* som använder IP-adressintervallet *10.0.3.0/24* :

    ![Lägg till ytterligare ett virtuellt nätverks under nät i Azure Portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. När du är klar väljer du **OK**. Det tar en stund att skapa det virtuella nätverkets undernät.

När du skapar en virtuell dator som behöver använda den hanterade domänen, se till att du väljer det här virtuella nätverkets undernät. Skapa inte virtuella datorer i standard *-under nätet för aadds*. Om du väljer ett annat virtuellt nätverk finns det ingen nätverks anslutning och DNS-matchning för att komma åt den hanterade domänen om du inte konfigurerar peering för virtuella nätverk.

## <a name="configure-virtual-network-peering"></a>Konfigurera peering för virtuellt nätverk

Du kan ha ett befintligt virtuellt Azure-nätverk för virtuella datorer eller vill hålla ditt virtuella nätverk för hanterad domän separat. För att kunna använda den hanterade domänen behöver virtuella datorer i andra virtuella nätverk ett sätt att kommunicera med Azure AD DS-domän kontrol Lanterna. Den här anslutningen kan tillhandahållas med hjälp av Azure Virtual Network-peering.

Med peering av virtuella Azure-nätverk är två virtuella nätverk kopplade samman, utan att det krävs någon VPN-enhet (virtuellt privat nätverk). Med hjälp av nätverks-peering kan du snabbt ansluta virtuella nätverk och definiera trafikflöden i Azure-miljön.

Mer information om peering finns i [Översikt över Azure Virtual Network-peering][peering-overview].

Slutför följande steg för att peer-koppla ett virtuellt nätverk till den hanterade domänens virtuella nätverk:

1. Välj det virtuella standard nätverket som skapats för din hanterade domän med namnet *aadds-VNet*.
1. På den vänstra menyn i fönstret virtuellt nätverk väljer du **peering**.
1. Om du vill skapa en peering väljer du **+ Lägg till**. I följande exempel är standardvärdet för *aadds-VNet* peer till ett virtuellt nätverk med namnet *myVnet*. Konfigurera följande inställningar med dina egna värden:

    * **Namn på peer koppling från aadds-VNet till fjärrnätverk**: en beskrivande identifierare för de två nätverken, till exempel *aadds-VNet-till-myvnet*
    * **Distributions typ för virtuellt nätverk**: *Resource Manager*
    * **Prenumeration**: prenumerationen på det virtuella nätverk som du vill peer-koppla till, till exempel *Azure*
    * **Virtuellt nätverk**: det virtuella nätverk som du vill peer-koppla till, till exempel *myVnet*
    * **Namn på peering från myVnet till aadds-VNet**: en beskrivande identifierare för de två nätverken, till exempel *myVnet-till-aadds-VNet*

    ![Konfigurera peering för virtuellt nätverk i Azure Portal](./media/tutorial-configure-networking/create-peering.png)

    Lämna eventuella andra standardinställningar för åtkomst till virtuella nätverk eller vidarebefordrad trafik om du inte har särskilda krav för din miljö och välj sedan **OK**.

1. Det tar en stund att skapa peering i både det virtuella Azure AD DS-nätverket och det virtuella nätverk som du har valt. När du är klar rapporterar **peering-statusen** *ansluten*, som visas i följande exempel:

    ![Anslutna peer-anslutna nätverk i Azure Portal](./media/tutorial-configure-networking/connected-peering.png)

Innan virtuella datorer i ett peer-kopplat virtuellt nätverk kan använda den hanterade domänen konfigurerar du DNS-servrarna så att de tillåter korrekt namn matchning.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Konfigurera DNS-servrar i ett peer-kopplat virtuellt nätverk

För att virtuella datorer och program i det peer-distribuerade virtuella nätverket ska kunna kommunicera med den hanterade domänen måste DNS-inställningarna uppdateras. IP-adresserna för Azure AD DS-domän kontrol Lanterna måste konfigureras som DNS-servrar i det peer-virtuella nätverket. Det finns två sätt att konfigurera domän kontrol Lanterna som DNS-servrar för det peer-virtuella nätverket:

* Konfigurera virtuella Azure-nätverk DNS-servrar för att använda Azure AD DS-domänkontrollanter.
* Konfigurera den befintliga DNS-servern som används på det peer-virtuella nätverket för att använda villkorlig DNS-vidarebefordran för att dirigera frågor till den hanterade domänen. De här stegen varierar beroende på den befintliga DNS-servern som används.

I den här självstudien ska vi konfigurera Azure Virtual Network DNS-servrar för att dirigera alla frågor till Azure AD DS-domänkontrollanter.

1. I Azure Portal väljer du resurs gruppen för det peer-virtuella nätverket, till exempel *myResourceGroup*. I listan över resurser väljer du det peer-virtuella nätverket, till exempel *myVnet*.
1. Välj **DNS-servrar** i den vänstra menyn i fönstret virtuellt nätverk.
1. Som standard använder ett virtuellt nätverk de inbyggda Azure-tillhandahållna DNS-servrarna. Välj att använda **anpassade** DNS-servrar. Ange IP-adresserna för Azure AD DS-domän kontrol Lanterna, som vanligt vis är *10.0.2.4* och *10.0.2.5*. Bekräfta de här IP-adresserna i **översikts** fönstret för din hanterade domän i portalen.

    ![Konfigurera de virtuella nätverkets DNS-servrar så att de använder Azure AD DS-domänkontrollanter](./media/tutorial-configure-networking/custom-dns.png)

1. När du är klar väljer du **Spara**. Det tar en stund att uppdatera DNS-servrarna för det virtuella nätverket.
1. Om du vill använda de uppdaterade DNS-inställningarna på de virtuella datorerna startar du om virtuella datorer som är anslutna till det peer-kopplade virtuella nätverket

När du skapar en virtuell dator som behöver använda den hanterade domänen ser du till att du väljer det här peer-kopplat virtuella nätverket. Om du väljer ett annat virtuellt nätverk finns det ingen nätverks anslutning och DNS-matchning för att komma åt den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Förstå de virtuella nätverks anslutnings alternativen för domänanslutna resurser till Azure AD DS
> * Skapa ett IP-adressintervall och ytterligare ett undernät i det virtuella Azure AD DS-nätverket
> * Konfigurera peering för virtuellt nätverk till ett nätverk som är skilt från Azure AD DS

Om du vill se den här hanterade domänen i praktiken skapar du och ansluter en virtuell dator till domänen.

> [!div class="nextstepaction"]
> [Anslut en virtuell Windows Server-dator till din hanterade domän](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
