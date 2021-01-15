---
title: Konfigurera IP-adresser för ett Azure-nätverks gränssnitt | Microsoft Docs
description: Lär dig hur du lägger till, ändrar och tar bort privata och offentliga IP-adresser för ett nätverks gränssnitt.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 3fd0cfe644ad78059e25d5386cd1a01f56ad9fba
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216996"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Lägga till, ändra eller ta bort IP-adresser för ett Azure-nätverksgränssnitt

Lär dig hur du lägger till, ändrar och tar bort offentliga och privata IP-adresser för ett nätverks gränssnitt. Privata IP-adresser som tilldelas till ett nätverks gränssnitt gör det möjligt för en virtuell dator att kommunicera med andra resurser i ett virtuellt Azure-nätverk och anslutna nätverk. En privat IP-adress möjliggör även utgående kommunikation till Internet med en oförutsägbar IP-adress. En [offentlig IP-adress](virtual-network-public-ip-address.md) som tilldelas till ett nätverks gränssnitt möjliggör inkommande kommunikation till en virtuell dator från Internet. Adressen möjliggör även utgående kommunikation från den virtuella datorn till Internet med en förutsägbar IP-adress. Mer information finns i [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Om du behöver skapa, ändra eller ta bort ett nätverks gränssnitt läser du artikeln [hantera ett nätverks gränssnitt](virtual-network-network-interface.md) . Om du behöver lägga till nätverks gränssnitt eller ta bort nätverks gränssnitt från en virtuell dator läser du artikeln [Lägg till eller ta bort nätverks gränssnitt](virtual-network-network-interface-vm.md) .

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på, eller ansluter till Azure med, måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som är tilldelad lämpliga åtgärder som anges i [nätverks gränssnittets behörigheter](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Lägg till IP-adresser

Du kan lägga till så många [privata](#private) och [offentliga](#public) [IPv4](#ipv4) -adresser som behövs för ett nätverks gränssnitt inom de gränser som anges i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Du kan lägga till en privat IPv6-adress till en [sekundär IP-konfiguration](#secondary) (så länge det inte finns några befintliga sekundära IP-konfigurationer) för ett befintligt nätverks gränssnitt. Varje nätverks gränssnitt får ha högst en privat IPv6-adress. Du kan också lägga till en offentlig IPv6-adress till en IPv6-nätverks gränssnitts konfiguration. Se [IPv6](#ipv6) för information om hur du använder IPv6-adresser.

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt som du vill lägga till en IPv4-adress för från listan.
3. Under **Inställningar** väljer du **IP-konfigurationer**.
4. Under **IP-konfigurationer** väljer du **+ Lägg till**.
5. Ange följande och välj sedan **OK**:

   |Inställning|Obligatoriskt?|Information|
   |---|---|---|
   |Name|Yes|Måste vara unikt för nätverks gränssnittet|
   |Typ|Yes|Eftersom du lägger till en IP-konfiguration i ett befintligt nätverks gränssnitt, och varje nätverks gränssnitt måste ha en [primär](#primary) IP-konfiguration, är ditt enda alternativ **sekundärt**.|
   |Tilldelnings metod för privat IP-adress|Yes|[**Dynamisk**](#dynamic): Azure tilldelar nästa tillgängliga adress för under nätets adress intervall som nätverks gränssnittet har distribuerats i. [**Statisk**](#static): du tilldelar en oanvänd adress för under nätets adress intervall som nätverks gränssnittet har distribuerats i.|
   |Offentlig IP-adress|No|**Inaktive rad:** Ingen offentlig IP-adressresurs är för närvarande kopplad till IP-konfigurationen. **Aktive rad:** Välj en befintlig offentlig IPv4-IP-adress eller skapa en ny. Information om hur du skapar en offentlig IP-adress finns i artikeln [offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address) .|
6. Lägg till sekundära privata IP-adresser manuellt i operativ systemet för den virtuella datorn genom att följa anvisningarna i artikeln [tilldela flera IP-adresser till operativ systemen för virtuella datorer](virtual-network-multiple-ip-addresses-portal.md#os-config) . Se [privata](#private) IP-adresser för särskilda överväganden innan du manuellt lägger till IP-adresser i ett operativ system för virtuella datorer. Lägg inte till några offentliga IP-adresser i operativ systemet för den virtuella datorn.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Ändra inställningar för IP-adress

Du kan behöva ändra tilldelnings metoden för en IPv4-adress, ändra den statiska IPv4-adressen eller ändra den offentliga IP-adress som tilldelats till ett nätverks gränssnitt. Om du ändrar den privata IPv4-adressen för en sekundär IP-konfiguration som är kopplad till ett sekundärt nätverks gränssnitt på en virtuell dator (Läs mer om [primära och sekundära nätverks gränssnitt](virtual-network-network-interface-vm.md)) placerar du den virtuella datorn i läget Stoppad (Frigjord) innan du slutför följande steg:

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt som du vill visa eller ändra inställningar för IP-adress för i listan.
3. Under **Inställningar** väljer du **IP-konfigurationer**.
4. Välj den IP-konfiguration som du vill ändra i listan.
5. Ändra inställningarna efter behov med hjälp av informationen om inställningarna i steg 5 i [Lägg till en IP-konfiguration](#add-ip-addresses).
6. Välj **Spara**.

>[!NOTE]
>Om det primära nätverks gränssnittet har flera IP-konfigurationer och du ändrar den privata IP-adressen för den primära IP-konfigurationen måste du manuellt tilldela om de primära och sekundära IP-adresserna till nätverks gränssnittet i Windows (krävs inte för Linux). Information om hur du manuellt tilldelar IP-adresser till ett nätverks gränssnitt i ett operativ system finns i [tilldela flera IP-adresser till virtuella datorer](virtual-network-multiple-ip-addresses-portal.md#os-config). Särskilda överväganden innan du lägger till IP-adresser i ett operativ system för virtuella datorer manuellt finns i [privata](#private) IP-adresser. Lägg inte till några offentliga IP-adresser i operativ systemet för den virtuella datorn.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC IP-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Ta bort IP-adresser

Du kan ta bort [privata](#private) och [offentliga](#public) IP-adresser från ett nätverks gränssnitt, men ett nätverks gränssnitt måste alltid ha minst en privat IPv4-adress tilldelad till sig.

1. Skriv *nätverks gränssnitt* i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **nätverks gränssnitt** visas i Sök resultaten väljer du det.
2. Välj det nätverks gränssnitt som du vill ta bort IP-adresser från listan.
3. Under **Inställningar** väljer du **IP-konfigurationer**.
4. Högerklicka på en [sekundär](#secondary) IP-konfiguration (du kan inte ta bort den [primära](#primary) konfigurationen) som du vill ta bort, Välj **ta bort**, Välj **Ja** för att bekräfta borttagningen. Om konfigurationen hade en offentlig IP-adressresurs kopplad resursen bort från IP-konfigurationen, men resursen tas inte bort.

**Kommandon**

|Verktyg|Kommando|
|---|---|
|CLI|[AZ Network NIC IP-config Delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-konfigurationer

[Privata](#private) och (valfritt) [offentliga](#public) IP-adresser tilldelas till en eller flera IP-konfigurationer som tilldelas till ett nätverks gränssnitt. Det finns två typer av IP-konfigurationer:

### <a name="primary"></a>Primär

Varje nätverks gränssnitt tilldelas en primär IP-konfiguration. En primär IP-konfiguration:

- Har en [privat](#private) [IPv4](#ipv4) -adress tilldelad till den. Du kan inte tilldela en privat [IPv6](#ipv6) -adress till en primär IP-konfiguration.
- Kan också ha en [offentlig](#public) IPv4-adress som är tilldelad till den. Du kan inte tilldela en offentlig IPv6-adress till en primär (IPv4) IP-konfiguration. 

### <a name="secondary"></a>Sekundär

Förutom en primär IP-konfiguration kan ett nätverks gränssnitt ha noll eller flera sekundära IP-konfigurationer kopplade till den. En sekundär IP-konfiguration:

- Måste ha en privat IPv4-eller IPv6-adress tilldelad. Om adressen är IPv6 kan nätverks gränssnittet bara ha en sekundär IP-konfiguration. Om adressen är IPv4 kan nätverks gränssnittet ha flera sekundära IP-konfigurationer kopplade till den. Mer information om hur många privata och offentliga IPv4-adresser som kan tilldelas till ett nätverks gränssnitt finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .
- Kan även ha tilldelats en offentlig IPv4-eller IPv6-adress. Att tilldela flera IPv4-adresser till ett nätverks gränssnitt är användbart i scenarier som:
  - Vara värd för flera webbplatser eller tjänster med olika IP-adresser och TLS/SSL-certifikat på en enskild server.
  - En virtuell dator som fungerar som en virtuell nätverks installation, till exempel en brand vägg eller belastningsutjämnare.
  - Möjlighet att lägga till någon av de privata IPv4-adresserna för alla nätverks gränssnitt till en Azure Load Balancer backend-pool. Tidigare kunde bara den primära IPv4-adressen för det primära nätverks gränssnittet läggas till i en backend-pool. Mer information om hur du läser in flera IPv4-konfigurationer finns i artikeln [belastnings utjämning flera IP-konfigurationer](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 
  - Möjlighet att belastningsutjämna en IPv6-adress som tilldelats till ett nätverks gränssnitt. Mer information om hur du läser in saldo till en privat IPv6-adress finns i artikeln [belastnings utjämning IPv6-adresser](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

## <a name="address-types"></a>Adress typer

Du kan tilldela följande typer av IP-adresser till en [IP-konfiguration](#ip-configurations):

### <a name="private"></a>Privat

Privata [IPv4](#ipv4) -eller IPv6-adresser gör det möjligt för en virtuell dator att kommunicera med andra resurser i ett virtuellt nätverk eller i andra anslutna nätverk. 

Som standard tilldelar Azure DHCP-servrarna den privata IPv4-adressen för den [primära IP-konfigurationen](#primary) för Azure nätverks gränssnittet till nätverks gränssnittet i operativ systemet för den virtuella datorn. Om det inte är nödvändigt bör du aldrig manuellt ange IP-adressen för ett nätverks gränssnitt i den virtuella datorns operativ system.

> [!WARNING]
> Om IPv4-adressen som angetts som primär IP-adress för ett nätverks gränssnitt i en virtuell dators operativ system skiljer sig något från den privata IPv4-adress som har tilldelats den primära IP-konfigurationen för det primära nätverks gränssnittet som är kopplat till en virtuell dator i Azure förlorar du anslutningen till den virtuella datorn.

Det finns scenarier där det är nödvändigt att manuellt ange IP-adressen för ett nätverks gränssnitt i den virtuella datorns operativ system. Du måste till exempel manuellt ange de primära och sekundära IP-adresserna för ett Windows-operativsystem när du lägger till flera IP-adresser till en virtuell Azure-dator. För en virtuell Linux-dator behöver du kanske bara manuellt ange de sekundära IP-adresserna. Mer information finns i [lägga till IP-adresser till ett operativ system för virtuella datorer](virtual-network-multiple-ip-addresses-portal.md#os-config) . Om du skulle behöva ändra adressen som tilldelats till en IP-konfiguration rekommenderar vi att du:

1. Se till att den virtuella datorn tar emot en adress från Azure DHCP-servrarna. När du har gjort det ändrar du tilldelningen av IP-adressen till DHCP i operativ systemet och startar om den virtuella datorn.
2. Stoppa (frigör) den virtuella datorn.
3. Ändra IP-adressen för IP-konfigurationen i Azure.
4. Starta den virtuella datorn.
5. [Konfigurera](virtual-network-multiple-ip-addresses-portal.md#os-config) de sekundära IP-adresserna i operativ systemet manuellt (och även den primära IP-adressen inom Windows) för att matcha det du angett i Azure.

Genom att följa föregående steg, är den privata IP-adress som tilldelats nätverks gränssnittet i Azure och i en virtuell dators operativ system oförändrad. Överväg att lägga till en Azure- [tagg](../azure-resource-manager/management/tag-resources.md) till de virtuella datorerna för att hålla reda på vilka virtuella datorer i din prenumeration som du har angett IP-adresser manuellt i ett operativ system. Du kan använda "IP-adresstilldelning: statisk", till exempel. På så sätt kan du enkelt hitta de virtuella datorerna i din prenumeration som du manuellt har angett IP-adressen för i operativ systemet.

Förutom att aktivera en virtuell dator för att kommunicera med andra resurser inom samma eller anslutna virtuella nätverk, kan en privat IP-adress också göra det möjligt för en virtuell dator att kommunicera utgående till Internet. Utgående anslutningar är käll nätverks adress som översätts av Azure till en oförutsägbar offentlig IP-adress. Läs mer om Azure utgående Internet-anslutning i artikeln om [Azure utgående Internet anslutning](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Du kan inte kommunicera inkommande till den virtuella datorns privata IP-adress från Internet. Om dina utgående anslutningar kräver en förutsägbar offentlig IP-adress, associerar du en offentlig IP-adressresurs till ett nätverks gränssnitt.

### <a name="public"></a>Offentliga

Offentliga IP-adresser som tilldelats via en offentlig IP-adressresurs möjliggör inkommande anslutning till en virtuell dator från Internet. Utgående anslutningar till Internet använder en förutsägbar IP-adress. Mer information finns i [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Du kan tilldela en offentlig IP-adress till en IP-konfiguration, men krävs inte för. Om du inte tilldelar en offentlig IP-adress till en virtuell dator genom att associera en offentlig IP-adressresurs, kan den virtuella datorn fortfarande kommunicera utgående till Internet. I det här fallet är den privata IP-adressen käll nätverks adress översatt av Azure till en oförutsägbar offentlig IP-adress. Mer information om offentliga IP-adressresurser finns i [offentlig IP](virtual-network-public-ip-address.md)-adressresurs.

Det finns gränser för antalet privata och offentliga IP-adresser som du kan tilldela till ett nätverks gränssnitt. Mer information finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

> [!NOTE]
> Azure översätter en virtuell dators privata IP-adress till en offentlig IP-adress. Därför är en virtuell dators operativ system inte medvetna om någon offentlig IP-adress som tilldelats den, så det finns inget behov av att manuellt tilldela en offentlig IP-adress i operativ systemet.

## <a name="assignment-methods"></a>Tilldelnings metoder

Offentliga och privata IP-adresser tilldelas med någon av följande tilldelnings metoder:

### <a name="dynamic"></a>Dynamisk

Dynamiska privata IPv4-och IPv6-adresser (valfritt) tilldelas som standard.

- **Endast offentligt**: Azure tilldelar adressen från ett intervall som är unikt för varje Azure-region. Information om vilka intervall som tilldelas varje region finns i [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Adressen kan ändras när en virtuell dator stoppas (frigörs) och sedan startas igen. Du kan inte tilldela en offentlig IPv6-adress till en IP-konfiguration med hjälp av en tilldelnings metod.
- **Endast privat**: Azure reserverar de fyra första adresserna i varje under näts adress intervall och tilldelar inte adresserna. Azure tilldelar nästa tillgängliga adress till en resurs från adressintervallet i undernätet. Om under nätets adress intervall till exempel är 10.0.0.0/16, och adresserna 10.0.0.4-10.0.0.14 redan har tilldelats (. 0-.3 är reserverade) tilldelar Azure 10.0.0.15 till resursen. Dynamisk är standardinställningen för allokering. När de har tilldelats släpps endast dynamiska IP-adresser om ett nätverksgränssnitt har tagits bort, tilldelats ett annat undernät inom samma virtuella nätverk eller om allokeringsmetoden ändras till statisk och en annan IP-adress anges. Som standard tilldelar Azure den tidigare dynamiskt tilldelade adressen som statisk adress när du ändrar allokeringsmetod från dynamisk till statisk. 

### <a name="static"></a>Statisk

Du kan (valfritt) tilldela en offentlig eller privat statisk IPv4-eller IPv6-adress till en IP-konfiguration. Mer information om hur Azure tilldelar statiska offentliga IPv4-adresser finns i [offentliga IP-adresser](virtual-network-public-ip-address.md).

- **Endast offentligt**: Azure tilldelar adressen från ett intervall som är unikt för varje Azure-region. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064). Adressen ändras inte förrän den offentliga IP-adressresurs som den är tilldelad till tas bort, eller så har tilldelnings metoden ändrats till dynamisk. Om den offentliga IP-adressresursen är kopplad till en IP-konfiguration måste den kopplas bort från IP-konfigurationen innan dess tilldelnings metod ändras.
- **Endast privat**: du väljer och tilldelar en adress från under nätets adress intervall. Adressen som du tilldelar kan vara alla adresser i adressintervallet i undernätet som inte är någon av de fyra första adresserna i undernätets adressintervall och som inte är tilldelade till någon annan resurs i undernätet. Statiska adresser släpps endast om ett nätverksgränssnitt tas bort. Om du ändrar tilldelnings metoden till statisk tilldelar Azure dynamiskt den tidigare tilldelade dynamiska IP-adressen som den statiska adressen, även om adressen inte är nästa tillgängliga adress i under nätets adress intervall. Adressen ändras också om nätverksgränssnittet har tilldelats ett annat undernät i samma virtuella nätverk. För att tilldela nätverksgränssnittet till ett annat undernät måste du emellertid först ändra allokeringsmetoden från statisk till dynamisk. När nätverksgränssnittet har tilldelats ett annat undernät kan du ändra allokeringsmetod till statisk och tilldela en IP-adress från det nya undernätets adressintervall.

## <a name="ip-address-versions"></a>IP-adress versioner

Du kan ange följande versioner när du tilldelar adresser:

### <a name="ipv4"></a>IPv4

Varje nätverks gränssnitt måste ha en [primär](#primary) IP-konfiguration med en tilldelad [privat](#private) [IPv4](#ipv4) -adress. Du kan lägga till en eller flera [sekundära](#secondary) IP-konfigurationer som var och en har en IPv4-privat och (valfritt) en [offentlig](#public) IPv4-IP-adress.

### <a name="ipv6"></a>IPv6

Du kan tilldela noll eller en privat [IPv6](#ipv6) -adress till en sekundär IP-konfiguration för ett nätverks gränssnitt. Nätverks gränssnittet får inte ha några befintliga sekundära IP-konfigurationer. Varje nätverks gränssnitt får ha högst en privat IPv6-adress. Du kan också lägga till en offentlig IPv6-adress till en IPv6-nätverks gränssnitts konfiguration.

> [!NOTE]
> Även om du kan skapa ett nätverks gränssnitt med en IPv6-adress med hjälp av portalen kan du inte lägga till ett befintligt nätverks gränssnitt till en ny eller befintlig virtuell dator med hjälp av portalen. Använd PowerShell eller Azure CLI för att skapa ett nätverks gränssnitt med en privat IPv6-adress och Anslut sedan nätverks gränssnittet när du skapar en virtuell dator. Du kan inte bifoga ett nätverks gränssnitt med en privat IPv6-adress tilldelad till en befintlig virtuell dator. Du kan inte lägga till en privat IPv6-adress i en IP-konfiguration för ett nätverks gränssnitt som är kopplat till en virtuell dator med hjälp av valfria verktyg (portal, CLI eller PowerShell).

Du kan inte tilldela en offentlig IPv6-adress till en primär eller sekundär IP-konfiguration.

## <a name="skus"></a>SKU:er

En offentlig IP-adress skapas med Basic-eller standard-SKU: n. Mer information om skillnader i SKU finns i [hantera offentliga IP-adresser](virtual-network-public-ip-address.md).

> [!NOTE]
> När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](./network-security-groups-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.

## <a name="next-steps"></a>Nästa steg
Om du vill skapa en virtuell dator med olika IP-konfigurationer kan du läsa följande artiklar:

|Uppgift|Verktyg|
|---|---|
|Skapa en virtuell dator med flera nätverksgränssnitt|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en virtuell dator med en virtuell NÄTVERKSKORTs uppsättning med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en virtuell nätverkskort virtuell dator med en privat IPv6-adress (bakom en Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|