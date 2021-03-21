---
title: Ansluta en Azure SSIS-integreringskörning till ett virtuellt nätverk
description: Lär dig hur du ansluter en Azure-SSIS integration runtime till ett virtuellt Azure-nätverk.
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 9a82b305adec1385bf659987ea39df6bb953cd70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370979"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta en Azure SSIS-integreringskörning till ett virtuellt nätverk

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du använder SQL Server Integration Services (SSIS) i Azure Data Factory bör du ansluta Azure-SSIS integration Runtime (IR) till ett virtuellt Azure-nätverk i följande scenarier:

- Du vill ansluta till lokala data lager från SSIS-paket som körs på din Azure-SSIS IR utan att konfigurera eller hantera en egen värd för IR som proxy. 

- Du vill vara värd för SSIS katalog databas (SSISDB) i Azure SQL Database med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller i SQL-hanterad instans med privat slut punkt. 

- Du vill ansluta till Azure-resurser som kon figurer ATS med tjänst slut punkter för virtuella nätverk från SSIS-paket som körs på din Azure-SSIS IR.

- Du vill ansluta till data lager/resurser som kon figurer ATS med IP-brandvägg från SSIS-paket som körs på din Azure-SSIS IR.

Med Data Factory kan du ansluta dina Azure-SSIS IR till ett virtuellt nätverk som skapats via den klassiska distributions modellen eller Azure Resource Manager distributions modellen.

> [!IMPORTANT]
> Det klassiska virtuella nätverket är inaktuellt, så Använd Azure Resource Manager virtuella nätverket i stället.  Om du redan använder det klassiska virtuella nätverket växlar du till Azure Resource Manager virtuella nätverket så snart som möjligt.

Genom att [Konfigurera en Azure-SQL Server Integration Services (SSIS) integrerings körning (IR) för att ansluta till ett virtuellt nätverk](tutorial-deploy-ssis-virtual-network.md) visar vi de minsta stegen via Azure Portal. Den här artikeln expanderar i självstudien och beskriver alla valfria uppgifter:

- Om du använder ett virtuellt nätverk (klassisk).
- Om du hämtar dina egna offentliga IP-adresser för Azure-SSIS IR.
- Om du använder en egen Domain Name System-Server (DNS).
- Om du använder en nätverks säkerhets grupp (NSG) i under nätet.
- Om du använder Azure-ExpressRoute eller en användardefinierad väg (UDR).
- Om du använder anpassade Azure-SSIS IR.
- Om du använder Azure PowerShell-etablering.

## <a name="access-to-on-premises-data-stores"></a>Åtkomst till lokala data lager

Om dina SSIS-paket har åtkomst till lokala data lager kan du ansluta dina Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket. Eller så kan du konfigurera och hantera en egen värd-IR som proxy för din Azure-SSIS IR. Mer information finns i [Konfigurera en egen värd-IR som en proxy för en Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Kom ihåg följande viktiga punkter när du ansluter till din Azure-SSIS IR till ett virtuellt nätverk: 

- Om inget virtuellt nätverk är anslutet till ditt lokala nätverk måste du först skapa ett [Azure Resource Manager virtuella nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) som Azure-SSIS IR ska ansluta till. Konfigurera sedan en [VPN gateway-anslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) för plats-till-plats eller [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -anslutning från det virtuella nätverket till ditt lokala nätverk. 

- Om ett Azure Resource Manager virtuella nätverk redan är anslutet till ditt lokala nätverk på samma plats som din Azure-SSIS IR kan du ansluta IR-nätverket till det virtuella nätverket. 

- Om ett klassiskt virtuellt nätverk redan är anslutet till ditt lokala nätverk på en annan plats än din Azure-SSIS IR, kan du skapa ett [Azure Resource Manager virtuellt nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) som din Azure-SSIS IR ska ansluta till. Konfigurera sedan ett [klassiskt-till-Azure Resource Manager virtuell nätverks](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning. 
 
- Om ett Azure Resource Manager virtuella nätverk redan är anslutet till ditt lokala nätverk på en annan plats än Azure-SSIS IR kan du först skapa ett [Azure Resource Manager virtuellt nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network) för din Azure-SSIS IR att ansluta till. Konfigurera sedan en virtuell nätverks anslutning Azure Resource Manager-till-Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Vara värd för SSIS-katalogen i SQL Database

Om du är värd för SSIS-katalogen i en Azure SQL Database med tjänst slut punkter för virtuella nätverk, se till att du ansluter Azure-SSIS IR till samma virtuella nätverk och undernät.

Om du är värd för SSIS-katalogen i SQL-hanterad instans med privat slut punkt, se till att du ansluter Azure-SSIS IR till samma virtuella nätverk, men i ett annat undernät än den hanterade instansen. Om du vill ansluta din Azure-SSIS IR till ett annat virtuellt nätverk än den SQL-hanterade instansen, rekommenderar vi antingen virtuell nätverks-peering (som är begränsad till samma region) eller en anslutning från det virtuella nätverket till ett virtuellt nätverk. Mer information finns i [ansluta ditt program till en Azure SQL-hanterad instans](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Åtkomst till Azure-tjänster

Om SSIS-paketen har åtkomst till Azure-resurser som har stöd för [tjänst slut punkter i virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) och du vill skydda åtkomsten till dessa resurser från Azure-SSIS IR, kan du ansluta dina Azure-SSIS IR till ett undernät för virtuella nätverk som har kon figurer ATS för tjänst slut punkter i virtuella nätverk och sedan lägga till en virtuell nätverks regel i relevanta Azure-resurser för att tillåta åtkomst från samma undernät.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Åtkomst till data källor som skyddas av regel för IP-brandvägg

Om dina SSIS-paket har åtkomst till data lager/resurser som endast tillåter vissa statiska offentliga IP-adresser och du vill skydda åtkomsten till dessa resurser från Azure-SSIS IR, kan du associera [offentliga IP-adresser](../virtual-network/virtual-network-public-ip-address.md) med Azure-SSIS IR när du ansluter till den till ett virtuellt nätverk och sedan lägga till en IP-brandväggsregel till de relevanta resurserna för att tillåta åtkomst från dessa IP-adresser. Det finns två alternativa sätt att göra detta: 

- När du skapar Azure-SSIS IR kan du ta med dina egna offentliga IP-adresser och ange dem via [Data Factory användar gränssnitt eller SDK](#join-the-azure-ssis-ir-to-a-virtual-network). Endast den utgående Internet anslutningen för Azure-SSIS IR använder dina tillhandahållna offentliga IP-adresser och andra enheter i under nätet kommer inte att använda dem.
- Du kan också konfigurera [Virtual Network NAT](../virtual-network/nat-overview.md) för det undernät som Azure-SSIS IR ansluter till och all utgående anslutning i det här under nätet kommer att använda dina angivna offentliga IP-adresser.

I samtliga fall kan det virtuella nätverket bara distribueras via Azure Resource Manager distributions modell.

I följande avsnitt finns mer information. 

## <a name="virtual-network-configuration"></a>Konfiguration av virtuellt nätverk

Konfigurera ditt virtuella nätverk så att det uppfyller följande krav: 

- Se till att `Microsoft.Batch` är en registrerad Provider under prenumerationen på det virtuella nätverkets undernät som är värd för Azure-SSIS IR. Om du använder ett klassiskt virtuellt nätverk kan du även ansluta `MicrosoftAzureBatch` till den klassiska rollen virtuell dator deltagare för det virtuella nätverket. 

- Kontrol lera att du har de behörigheter som krävs. Mer information finns i [Konfigurera behörigheter](#perms).

- Välj rätt undernät som värd för Azure-SSIS IR. Mer information finns i [Välj under nätet](#subnet). 

- Om du hämtar dina egna offentliga IP-adresser för Azure-SSIS IR, se [Välj statiska offentliga IP-adresser](#publicIP)

- Om du använder en egen Domain Name System-Server (DNS) i det virtuella nätverket, se [Konfigurera DNS-servern](#dns_server). 

- Om du använder en nätverks säkerhets grupp (NSG) i under nätet, se [Konfigurera en NSG](#nsg). 

- Om du använder Azure-ExpressRoute eller en användardefinierad väg (UDR) kan du läsa [använda Azure ExpressRoute eller en UDR](#route). 

- Se till att det virtuella nätverkets resurs grupp (eller resurs gruppen offentliga IP-adresser "om du tar med egna offentliga IP-adresser) kan skapa och ta bort vissa Azure-nätverks resurser. Mer information finns i [Konfigurera resurs gruppen](#resource-group). 

- Om du anpassar din Azure-SSIS IR enligt beskrivningen i den [anpassade installationen för Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)använder vår interna process för att hantera sina noder privata IP-adresser från ett fördefinierat område 172.16.0.0 till 172.31.255.255. Se därför till att de privata IP-adressintervall för dina virtuella eller lokala nätverk inte kolliderar med det här intervallet.

Det här diagrammet visar de anslutningar som krävs för din Azure-SSIS IR:

![Diagram som visar de anslutningar som krävs för din Azure-SSIS IR.](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> Konfigurera behörigheter

Den användare som skapar Azure-SSIS IR måste ha följande behörigheter:

- Om du ansluter din SSIS-IR till ett Azure Resource Manager virtuellt nätverk har du två alternativ:

  - Använd den inbyggda rollen nätverks deltagare. Den här rollen ingår i _Microsoft. Network/ \*_ permission, som har en mycket större omfattning än vad som behövs.

  - Skapa en anpassad roll som endast innehåller nödvändig _Microsoft. Network/virtualNetworks/ \* /Join/Action-_ behörighet. Om du även vill ta med dina egna offentliga IP-adresser för Azure-SSIS IR när du ansluter till den till ett Azure Resource Manager virtuellt nätverk, kan du även ta med _Microsoft. Network/publicIPAddresses/*/Join/Action-_ behörighet i rollen.

- Om du ansluter din SSIS-IR till ett klassiskt virtuellt nätverk, rekommenderar vi att du använder den inbyggda klassiska rollen virtuell dator deltagare. Annars måste du definiera en anpassad roll som innehåller behörighet att ansluta till det virtuella nätverket.

### <a name="select-the-subnet"></a><a name="subnet"></a> Välj under nätet

När du väljer ett undernät: 

- Välj inte GatewaySubnet för att distribuera en Azure-SSIS IR. Den är dedikerad för virtuella nätverks-gatewayer. 

- Kontrol lera att det undernät du väljer har tillräckligt med ledigt adress utrymme för att Azure-SSIS IR ska kunna använda. Lämna tillgängliga IP-adresser för minst två gånger IR-nodnummer. Azure reserverar vissa IP-adresser i varje undernät. De här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse och tre fler adresser används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Använd inte ett undernät som uteslutande används av andra Azure-tjänster (till exempel SQL Database SQL-hanterad instans, App Service och så vidare). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Välj statiska offentliga IP-adresser

Om du vill använda egna statiska offentliga IP-adresser för Azure-SSIS IR samtidigt som du ansluter till ett virtuellt nätverk, kontrollerar du att de uppfyller följande krav:

- Exakt två oanvända som inte redan är kopplade till andra Azure-resurser bör tillhandahållas. Den extra som används när vi uppgraderar Azure-SSIS IR regelbundet. Observera att en offentlig IP-adress inte kan delas mellan din aktiva Azure-SSIS IRs.

- De bör båda vara statiska av standard typ. Se [SKU: er för offentlig IP-adress](../virtual-network/public-ip-addresses.md#sku) för mer information.

- De bör båda ha ett DNS-namn. Om du inte har angett något DNS-namn när du skapar dem kan du göra det på Azure Portal.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- De och det virtuella nätverket bör vara under samma prenumeration och i samma region.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Konfigurera DNS-servern 
Om du behöver använda din egen DNS-server i ett virtuellt nätverk som är anslutet av din Azure-SSIS IR för att matcha ditt privata värdnamn, kontrollerar du att det även kan matcha globala Azure-värdnamn (till exempel en Azure Storage-BLOB med namnet `<your storage account>.blob.core.windows.net` ). 

En rekommenderad metod är nedan: 

-   Konfigurera den anpassade DNS som vidarebefordrar begär anden till Azure DNS. Du kan vidarebefordra olösta DNS-poster till IP-adressen för Azures rekursiva matchare (168.63.129.16) på din egen DNS-server. 

Mer information finns i [namn matchning som använder din egen DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Använd ett fullständigt kvalificerat domän namn (FQDN) för ditt privata värdnamn (till exempel använda `<your_private_server>.contoso.com` i stället för `<your_private_server>` ). Alternativt kan du använda en anpassad standard installation på din Azure-SSIS IR för att automatiskt lägga till ett eget DNS-suffix (till exempel `contoso.com` ) till ett icke-kvalificerat domän namn för en domän och omvandla det till ett fullständigt domän namn innan det används i DNS-frågor, se [vanliga anpassade installations exempel](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> Konfigurera en NSG
Om du behöver implementera en NSG för under nätet som används av din Azure-SSIS IR ska du tillåta inkommande och utgående trafik via följande portar: 

-   **Ingående krav för Azure-SSIS IR**

| Riktning | Transport protokoll | Källa | Källportintervall | Mål | Målportintervall | Kommentarer |
|---|---|---|---|---|---|---|
| Inkommande | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (om du ansluter IR till ett virtuellt Resource Manager-nätverk) <br/><br/>10100, 20100, 30100 (om du ansluter IR till ett klassiskt virtuellt nätverk)| Den Data Factory tjänsten använder dessa portar för att kommunicera med noderna i ditt Azure-SSIS IR i det virtuella nätverket. <br/><br/> Oavsett om du skapar en NSG på under näts nivå konfigurerar Data Factory alltid en NSG på nivån för nätverkskorten (NIC) som är anslutna till de virtuella datorer som är värdar för Azure-SSIS IR. Det är bara inkommande trafik från Data Factory IP-adresser på de angivna portarna som tillåts av NSG på NÄTVERKSKORTs nivå. Även om du öppnar de här portarna till Internet trafik på under näts nivån blockeras trafik från IP-adresser som inte Data Factory IP-adresser på NÄTVERKSKORTs nivån. |
| Inkommande | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Valfritt Den här regeln krävs endast när Microsoft Support ber kunden öppna för avancerad fel sökning och kan stängas direkt efter fel sökningen. **CorpNetSaw** service tag tillåter endast säker åtkomst till arbets stationer på Microsofts företags nätverk för att använda fjärr skrivbord. Och den här tjänst tag gen kan inte väljas från portalen och är bara tillgänglig via Azure PowerShell eller Azure CLI. <br/><br/> På NÄTVERKSKORTs nivå NSG är port 3389 öppen som standard och vi låter dig kontrol lera port 3389 på under näts nivån NSG, medan Azure-SSIS IR har otillåten port 3389 utgående som standard i Windows-brandväggens regel på varje IR-nod för skydd. |
||||||||

-   **Utgående krav för Azure-SSIS IR**

| Riktning | Transport protokoll | Källa | Källportintervall | Mål | Målportintervall | Kommentarer |
|---|---|---|---|---|---|---|
| Utgående | TCP | VirtualNetwork | * | AzureCloud | 443 | Noderna i Azure-SSIS IR i det virtuella nätverket använder den här porten för att få åtkomst till Azure-tjänster, till exempel Azure Storage och Azure-Event Hubs. |
| Utgående | TCP | VirtualNetwork | * | Internet | 80 | Valfritt Noderna i Azure-SSIS IR i det virtuella nätverket Använd den här porten för att hämta en lista över återkallade certifikat från Internet. Om du blockerar den här trafiken kan du få nedgradering av prestanda när du startar IR och förlorar möjlighet att kontrol lera listan över återkallade certifikat för certifikat användning. Om du vill begränsa destinationen till vissa FQDN ytterligare kan du läsa avsnittet **använda Azure ExpressRoute eller UDR** .|
| Utgående | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | Valfritt Den här regeln krävs bara när noderna i Azure-SSIS IR i det virtuella nätverket har åtkomst till en SSISDB som finns på servern. Om din server anslutnings princip är inställd på **proxy** i stället för **omdirigering** krävs bara port 1433. <br/><br/> Den här utgående säkerhets regeln gäller inte för en SSISDB som hanteras av din SQL-hanterade instans i det virtuella nätverket eller SQL Database som kon figurer ATS med privat slut punkt. |
| Utgående | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | Valfritt Den här regeln krävs bara när noderna i Azure-SSIS IR i det virtuella nätverket har åtkomst till en SSISDB som hanteras av din SQL-hanterade instans i det virtuella nätverket eller SQL Database som kon figurer ATS med privat slut punkt. Om din server anslutnings princip är inställd på **proxy** i stället för **omdirigering** krävs bara port 1433. |
| Utgående | TCP | VirtualNetwork | * | Storage | 445 | Valfritt Den här regeln krävs bara när du vill köra SSIS-paketet som lagras i Azure Files. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Använd Azure ExpressRoute eller UDR
Om du vill kontrol lera utgående trafik från Azure-SSIS IR kan du dirigera trafik som initierats från Azure-SSIS IR till lokal brand Väggs enhet via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) Force-tunnlar (annonsera en BGP-väg, 0.0.0.0/0, till det virtuella nätverket) eller till en virtuell nätverks installation (NVA) som brand vägg eller [Azure-brandvägg](../firewall/index.yml) via [UDR](../virtual-network/virtual-networks-udr-overview.md). 

![NVA-scenario för Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Du måste göra något av följande för att hela scenariot ska fungera
   -   Inkommande trafik mellan Azure Batch hanterings tjänster och Azure-SSIS IR kan inte dirigeras via brand Väggs enheten.
   -   Brand Väggs apparaten måste tillåta utgående trafik som krävs av Azure-SSIS IR.

Inkommande trafik mellan Azure Batch hanterings tjänster och Azure-SSIS IR kan inte dirigeras till brand Väggs enheten annars kommer trafiken att brytas på grund av problem med asymmetrisk routning. Vägar måste definieras för inkommande trafik så att trafiken kan svara på samma sätt som den kom fram. Du kan definiera en speciell UDR för att dirigera trafik mellan Azure Batch hanterings tjänster och Azure-SSIS IR med nästa hopp typ som **Internet**.

Om din Azure-SSIS IR till exempel finns på `UK South` och du vill kontrol lera utgående trafik via Azure-brandväggen, så skulle du först hämta en lista med IP-intervall med service tag `BatchNodeManagement.UKSouth` från service tagns [IP-intervall nedladdnings länk](https://www.microsoft.com/download/details.aspx?id=56519) eller [service tag Discovery-API](../virtual-network/service-tags-overview.md#service-tags-on-premises). Använd sedan följande UDR av relaterade IP-adressintervall med nästa hopp typ som **Internet** tillsammans med vägen 0.0.0.0/0 med nästa hopp typ som **virtuell** installation.

![Azure Batch inställningar för UDR](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Den här metoden innebär ytterligare en underhålls kostnad. Kontrol lera IP-intervallet regelbundet och Lägg till nya IP-intervall i din UDR för att undvika att avbryta Azure-SSIS IR. Vi rekommenderar att du kontrollerar IP-intervallet varje månad eftersom när den nya IP-adressen visas i tjänst tag gen, tar IP-adressen en gång i följd. 

För att göra installationen av UDR-regler enklare kan du köra följande PowerShell-skript för att lägga till UDR-regler för Azure Batch Management Services:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

För att brand Väggs enheten ska kunna tillåta utgående trafik måste du tillåta utgående trafik till under portar på samma sätt som kravet i NSG utgående regler.
-   Port 443 med målet som Azure Cloud Services.

    Om du använder Azure-brandväggen kan du ange nätverks regel med AzureCloud service tag. För brand väggar av andra typer kan du antingen bara tillåta mål som alla för port 443 eller tillåta nedanstående FQDN-namn baserat på typen av Azure-miljö:

    | Azure-miljö | Slutpunkter                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure, offentlig      | <ul><li><b>Azure Data Factory (hantering)</b><ul><li>\*. frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (hantering)</b><ul><li>\*.blob.core.windows.net</li><li>\*. table.core.windows.net</li></ul></li><li><b>Azure Container Registry (anpassad installation)</b><ul><li>\*. azurecr.io</li></ul></li><li><b>Event Hub (loggning)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft Logging service (intern användning)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (hantering)</b><ul><li>\*. frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (hantering)</b><ul><li>\*. blob.core.usgovcloudapi.net</li><li>\*. table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (anpassad installation)</b><ul><li>\*. azurecr.us</li></ul></li><li><b>Event Hub (loggning)</b><ul><li>\*. servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft Logging service (intern användning)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure Kina 21Vianet     | <ul><li><b>Azure Data Factory (hantering)</b><ul><li>\*. frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (hantering)</b><ul><li>\*. blob.core.chinacloudapi.cn</li><li>\*. table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (anpassad installation)</b><ul><li>\*. azurecr.cn</li></ul></li><li><b>Event Hub (loggning)</b><ul><li>\*. servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft Logging service (intern användning)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Som för FQDN för Azure Storage, Azure Container Registry och Event Hub, kan du också välja att aktivera följande tjänst slut punkter för det virtuella nätverket så att nätverks trafiken till dessa slut punkter går via Azure stamnät nätverket i stället för att dirigeras till brand Väggs enheten:
    -  Microsoft.Storage
    -  Microsoft. ContainerRegistry
    -  Microsoft. EventHub


-   Port 80 med målet som hämtnings platser för CRL.

    Du kan tillåta nedanstående FQDN som används som lista över återkallade certifikat (lista över återkallade certifikat) att hämta certifikat platser för Azure-SSIS IR hanterings syfte:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Om du använder certifikat som har olika listor över återkallade certifikat rekommenderar vi att du även tar med dem. Du kan läsa detta om du vill veta mer om [listan över återkallade certifikat](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Om du inte tillåter den här trafiken kan du uppleva prestanda nedgradering när du startar Azure-SSIS IR och förlorar möjlighet att kontrol lera listan över återkallade certifikat för certifikat användning som inte rekommenderas från säkerhets punkten i vyn.

-   Port 1433, 11000-11999 med mål som Azure SQL Database (krävs endast när noderna i Azure-SSIS IR i det virtuella nätverket har åtkomst till en SSISDB som servern är värd för).

    Om du använder Azure-brandväggen kan du ange nätverks regel med Azure SQL Service-tagg, annars kan du tillåta mål som en viss Azure SQL-URL i brand Väggs enheten.

-   Port 445 med målet som Azure Storage (krävs endast när du kör SSIS-paketet som lagras i Azure Files).

    Om du använder Azure-brandväggen kan du ange en nätverks regel med taggen Storage service, annars kan du tillåta mål som en viss URL för Azure File Storage i brand Väggs installationen.

> [!NOTE]
> Om du konfigurerar Virtual Network tjänst slut punkter i ditt undernät för Azure SQL och Storage kommer trafiken mellan Azure-SSIS IR och Azure SQL i samma region \ Azure Storage i samma region eller i den kopplade regionen att dirigeras till Microsoft Azure stamnät nätverket direkt i stället för brand Väggs enheten.

Om du inte behöver ha möjlighet att inspektera utgående trafik i Azure-SSIS IR kan du bara använda Route för att tvinga all trafik till nästa hopp typ **Internet**:

-   I ett Azure ExpressRoute-scenario kan du använda en router med värdet 0.0.0.0/0 med nästa hopp typ som **Internet** på det undernät som är värd för Azure-SSIS IR. 
-   I ett NVA-scenario kan du ändra den befintliga 0.0.0.0/0-vägen som används på det undernät som är värd för Azure-SSIS IR från nästa hopp typ som **virtuell** installation till **Internet**.

![Lägg till en väg](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Ange Route med nästa hopp typ **Internet** innebär inte att all trafik går via Internet. Så länge mål adressen är för en av Azures tjänster dirigerar Azure trafiken direkt till tjänsten via Azures stamnät nätverk, i stället för att dirigera trafiken till Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Konfigurera resurs gruppen

Azure-SSIS IR måste skapa vissa nätverks resurser i samma resurs grupp som det virtuella nätverket. Dessa resurser omfattar:
- En Azure Load Balancer med namnet *\<Guid> -azurebatch-cloudserviceloadbalancer*.
- En offentlig Azure-IP-adress med namnet *\<Guid> -azurebatch-cloudservicepublicip*.
- En nätverks säkerhets grupp med namnet *\<Guid> -azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Du kan nu ta med egna statiska offentliga IP-adresser för Azure-SSIS IR. I det här scenariot skapar vi bara Azure Load Balancer och nätverks säkerhets gruppen under samma resurs grupp som dina statiska offentliga IP-adresser i stället för det virtuella nätverket.

De här resurserna kommer att skapas när din Azure-SSIS IR startas. De kommer att tas bort när Azure-SSIS IR stoppar. Om du använder egna statiska offentliga IP-adresser för Azure-SSIS IR raderas inte dina egna statiska offentliga IP-adresser när Azure-SSIS IR stoppas. För att undvika att blockera Azure-SSIS IR från att stoppa kan du inte återanvända de här nätverks resurserna i dina andra resurser.

Kontrol lera att du inte har resurs låset för resurs gruppen/prenumerationen som det virtuella nätverket/dina statiska offentliga IP-adresser tillhör. Om du konfigurerar ett skrivskyddat/borttagnings lås kommer det inte att gå att starta och stoppa Azure-SSIS IR, eller så slutar det svara.

Se till att du inte har en Azure Policy tilldelning som förhindrar att följande resurser skapas under resurs gruppen/prenumerationen som det virtuella nätverket/dina statiska offentliga IP-adresser tillhör: 
- Microsoft. Network/belastningsutjämnare 
- Microsoft. Network/NetworkSecurityGroups 
- Microsoft. Network/PublicIPAddresses 

Se till att resurs kvoten för din prenumeration är tillräckligt för de tre nätverks resurserna ovan. För varje Azure-SSIS IR som skapas i det virtuella nätverket måste du reservera två kostnads fria kvoter för var och en av de tre nätverks resurserna ovan. Den extra en kvot kommer att användas när vi regelbundet uppgraderar din Azure-SSIS IR.

### <a name="faq"></a><a name="faq"></a> ASSURANCE

- Hur kan jag skydda den offentliga IP-adressen som exponeras på min Azure-SSIS IR för inkommande anslutning? Är det möjligt att ta bort den offentliga IP-adressen?
 
  Just nu skapas en offentlig IP-adress automatiskt när Azure-SSIS IR ansluter till ett virtuellt nätverk. Vi har en NSG på NIC-nivå för att bara tillåta Azure Batch hanterings tjänster för inkommande – Anslut till din Azure-SSIS IR. Du kan också ange en NSG på under näts nivå för inkommande skydd.

  Om du inte vill att någon offentlig IP-adress ska exponeras bör du överväga att [Konfigurera en lokal IR as-proxy för din Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) istället för att ansluta till din Azure-SSIS IR till ett virtuellt nätverk, om detta gäller för ditt scenario.
 
- Kan jag lägga till den offentliga IP-adressen för mina Azure-SSIS IR i brand väggens lista över tillåtna data källor?

  Du kan nu ta med egna statiska offentliga IP-adresser för Azure-SSIS IR. I så fall kan du lägga till IP-adresserna i brand väggens lista över tillåtna data källor. Du kan också överväga andra alternativ nedan för att skydda data åtkomst från din Azure-SSIS IR beroende på ditt scenario:

  - Om din data källa finns lokalt, efter att du har anslutit ett virtuellt nätverk till ditt lokala nätverk och anslutit din Azure-SSIS IR till det virtuella nätverkets undernät, kan du sedan lägga till det privata IP-adressintervallet för det under nätet i brand väggens lista över tillåtna användare för din data källa.
  - Om data källan är en Azure-tjänst som stöder tjänst slut punkter för virtuella nätverk kan du konfigurera en tjänst slut punkt för virtuellt nätverk i det virtuella nätverkets undernät och ansluta din Azure-SSIS IR till det under nätet. Du kan sedan lägga till en virtuell nätverks regel med det under nätet i brand väggen för din data källa.
  - Om data källan är en moln tjänst som inte kommer från Azure kan du använda en UDR för att dirigera utgående trafik från din Azure-SSIS IR till en NVA/Azure-brandvägg via en statisk offentlig IP-adress. Du kan sedan lägga till den statiska offentliga IP-adressen för din NVA/Azure-brandvägg i brand väggens lista över tillåtna för din data källa.
  - Om inget av ovanstående alternativ motsvarar dina behov kan du överväga att [Konfigurera en lokal IR as-proxy för din Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). Du kan sedan lägga till den statiska offentliga IP-adressen för den dator som är värd för IR till brand väggens lista över tillåtna webbplatser för din data källa.

- Varför måste jag ange två statiska offentliga adresser om jag vill ta med mig för Azure-SSIS IR?

  Azure-SSIS IR uppdateras automatiskt regelbundet. Nya noder skapas under uppgraderingen och de gamla tas bort. Men för att undvika drift stopp kommer de gamla noderna inte att tas bort förrän de nya är klara. Därför kan din första statiska offentliga IP-adress som används av de gamla noderna inte frisläppas omedelbart och vi behöver din andra statiska offentliga IP-adress för att skapa de nya noderna.

- Jag har fått egna statiska offentliga IP-adresser för Azure-SSIS IR, men varför kan de fortfarande inte komma åt mina data källor?

  - Bekräfta att de två statiska offentliga IP-adresserna har lagts till i brand väggens lista över tillåtna för dina data källor. Varje gången din Azure-SSIS IR uppgraderas växlas den statiska offentliga IP-adressen mellan de två som du har fått. Om du bara lägger till en av dem i listan över tillåtna data kommer data åtkomsten för din Azure-SSIS IR att bli bruten efter uppgraderingen.
  - Om data källan är en Azure-tjänst kontrollerar du om du har konfigurerat den med tjänst slut punkter för virtuella nätverk. I så fall kommer trafiken från Azure-SSIS IR till din data källa att växla till att använda de privata IP-adresser som hanteras av Azure-tjänster och att lägga till egna statiska offentliga IP-adresser i brand väggens lista över tillåtna data för din data källa börjar inte gälla.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory UI)

Det här avsnittet visar hur du ansluter en befintlig Azure-SSIS IR till ett virtuellt nätverk (klassisk eller Azure Resource Manager) med hjälp av Azure Portal och Data Factory användar gränssnitt. 

Innan du ansluter till din Azure-SSIS IR till det virtuella nätverket måste du konfigurera det virtuella nätverket på rätt sätt. Följ stegen i avsnittet som gäller din typ av virtuella nätverk (klassisk eller Azure Resource Manager). Följ sedan stegen i det tredje avsnittet för att ansluta dina Azure-SSIS IR till det virtuella nätverket. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurera ett Azure Resource Manager virtuellt nätverk

Använd portalen för att konfigurera ett Azure Resource Manager virtuellt nätverk innan du försöker ansluta till ett Azure-SSIS IR.

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare Data Factory användar gränssnitt. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Välj **fler tjänster**. Filtrera och välj **virtuella nätverk**. 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På sidan **virtuellt nätverk** väljer du **Egenskaper**. 

1. Välj kopierings knappen för **resurs-ID** för att kopiera resurs-ID för det virtuella nätverket till Urklipp. Spara ID: t från Urklipp i OneNote eller en fil. 

1. På den vänstra menyn väljer du **undernät**. Se till att antalet tillgängliga adresser är större än noderna i Azure-SSIS IR. 

1. Verifiera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory Portal registreras Azure Batch-providern automatiskt åt dig.) 

   1. I Azure Portal väljer du **prenumerationer** på den vänstra menyn. 

   1. Välj din prenumeration. 

   1. Välj **resurs leverantörer** till vänster och bekräfta att **Microsoft.BatCH** är en registrerad Provider. 

   ![Bekräftelse av status registrerad](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.BatCH** i listan kan du registrera det genom att [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurera ett klassiskt virtuellt nätverk

Använd portalen för att konfigurera ett klassiskt virtuellt nätverk innan du försöker ansluta till en Azure-SSIS IR. 

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare Data Factory användar gränssnitt. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Välj **fler tjänster**. Filtrera och välj **virtuella nätverk (klassisk)**. 

1. Filtrera efter och välj ditt virtuella nätverk i listan. 

1. På sidan **virtuellt nätverk (klassisk)** väljer du **Egenskaper**. 

   ![Klassiskt virtuellt nätverks resurs-ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Välj kopierings knappen för **resurs-ID** för att kopiera resurs-ID för det klassiska nätverket till Urklipp. Spara ID: t från Urklipp i OneNote eller en fil. 

1. På den vänstra menyn väljer du **undernät**. Se till att antalet tillgängliga adresser är större än noderna i Azure-SSIS IR. 

   ![Antal tillgängliga adresser i det virtuella nätverket](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Anslut **MicrosoftAzureBatch** till den **klassiska rollen virtuell dator deltagare** för det virtuella nätverket. 

   1. Välj **åtkomst kontroll (IAM)** på den vänstra menyn och välj fliken **roll tilldelningar** . 

       ![Knapparna "åtkomst kontroll" och "Lägg till"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Välj **Lägg till rolltilldelning**.

   1. På sidan **Lägg till roll tilldelning** för **roll** väljer du **klassisk virtuell dator deltagare**. I rutan **Välj** klistrar du in **ddbf3205-c6bd-46ae-8127-60eb93363864** och väljer sedan **Microsoft Azure Batch** i listan med Sök resultat. 

       ![Sök resultat på sidan Lägg till roll tilldelning](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Spara inställningarna och Stäng sidan genom att välja **Spara** . 

       ![Spara åtkomst inställningar](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Bekräfta att du ser **Microsoft Azure Batch** i listan över bidrags givare. 

       ![Bekräfta Azure Batch åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifiera att Azure Batch-providern är registrerad i Azure-prenumerationen som har det virtuella nätverket. Eller registrera Azure Batch-providern. Om du redan har ett Azure Batch konto i din prenumeration registreras din prenumeration för Azure Batch. (Om du skapar Azure-SSIS IR i Data Factory Portal registreras Azure Batch-providern automatiskt åt dig.) 

   1. I Azure Portal väljer du **prenumerationer** på den vänstra menyn. 

   1. Välj din prenumeration. 

   1. Välj **resurs leverantörer** till vänster och bekräfta att **Microsoft.BatCH** är en registrerad Provider. 

   ![Bekräftelse av status registrerad](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Om du inte ser **Microsoft.BatCH** i listan kan du registrera det genom att [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Ansluta Azure-SSIS IR till ett virtuellt nätverk

När du har konfigurerat ditt Azure Resource Manager virtuella nätverk eller ett klassiskt virtuellt nätverk kan du ansluta Azure-SSIS IR till det virtuella nätverket:

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöder endast dessa webbläsare Data Factory användar gränssnitt. 

1. I [Azure Portal](https://portal.azure.com)väljer du **data fabriker** på den vänstra menyn. Om du inte ser **data fabriker** på menyn väljer du **fler tjänster** och i avsnittet **information + analys** väljer du **data fabriker**. 

   ![Lista över data fabriker](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Välj din data fabrik med Azure-SSIS IR i listan. Du ser start sidan för din data fabrik. Välj panelen **författare & Monitor** . Du ser Data Factory användar gränssnitt på en separat flik. 

   ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. I Data Factory användar gränssnitt växlar du till fliken **Redigera** , väljer **anslutningar** och växlar till fliken **integrerings körningar** . 

   ![Fliken "integrerings körningar"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Om din Azure-SSIS IR körs går du till listan **åtgärder** i kolumnen för **integration runtime** och väljer **stopp** knappen för din Azure-SSIS IR. Du kan inte redigera din Azure-SSIS IR förrän du stoppar den. 

   ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. I listan med **integrerings körningar** i kolumnen **åtgärder** väljer du knappen **Redigera** för Azure-SSIS IR. 

   ![Redigera integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Gå igenom avsnitten **allmänna inställningar** och **SQL-inställningar** på sidan för integration runtime-installationen genom att välja knappen **Nästa** . 

1. I avsnittet **Avancerade inställningar** : 

   1. Markera kryss rutan **Välj ett VNet för Azure-SSIS integration runtime att ansluta, Tillåt ADF att skapa vissa nätverks resurser och om du vill ta med egna statiska offentliga IP-adresser** . 

   1. För **prenumeration** väljer du den Azure-prenumeration som har ditt virtuella nätverk.

   1. För **plats** är samma plats för integrerings körningen markerad.

   1. I **typ** väljer du typ av virtuellt nätverk: klassisk eller Azure Resource Manager. Vi rekommenderar att du väljer ett Azure Resource Manager virtuellt nätverk, eftersom klassiska virtuella nätverk snart kommer att bli föråldrade.

   1. För **VNet-namn** väljer du namnet på ditt virtuella nätverk. Det bör vara samma som används för SQL Database med tjänst slut punkter för virtuella nätverk eller SQL-hanterad instans med privat slut punkt som värd för SSISDB. Eller bör vara samma som den som är ansluten till ditt lokala nätverk. Annars kan det vara ett virtuellt nätverk för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

   1. För **under näts namn** väljer du namnet på under nätet för ditt virtuella nätverk. Det bör vara samma som används för SQL Database med tjänst slut punkter för virtuella nätverk som värd för SSISDB. Eller bör vara ett annat undernät än det som används för SQL-hanterad instans med privat slut punkt som värd för SSISDB. Annars kan det vara vilket undernät som helst för att ta med egna statiska IP-adresser för Azure-SSIS IR.

   1. Markera kryss rutan **Lägg till statiska offentliga IP-adresser för din Azure-SSIS integration runtime** för att välja om du vill ta med egna statiska IP-adresser för Azure-SSIS IR, så att du kan tillåta dem i brand väggen för dina data källor.

      Om du markerar kryss rutan utför du följande steg.

      1. För **första statiska offentliga IP-adress** väljer du den första statiska offentliga IP-adress som [uppfyller kraven](#publicIP) för din Azure-SSIS IR. Om du inte har några, klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure Portal och klickar sedan på uppdatera-knappen här, så att du kan välja dem.
      
      1. För **andra statiska offentliga IP-adresser** väljer du den andra statiska offentliga IP-adress som [uppfyller kraven](#publicIP) för din Azure-SSIS IR. Om du inte har några, klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure Portal och klickar sedan på uppdatera-knappen här, så att du kan välja dem.

   1. Välj **VNet-validering**. Om verifieringen lyckas väljer du **Fortsätt**. 

   ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. I avsnittet **Sammanfattning** granskar du alla inställningar för din Azure-SSIS IR. Välj sedan **Uppdatera**.

1. Starta din Azure-SSIS IR genom att välja **Start** -knappen i kolumnen **åtgärder** för din Azure-SSIS IR. Det tar cirka 20 till 30 minuter att starta Azure-SSIS IR som ansluter till ett virtuellt nätverk. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definiera variablerna

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du kan ansluta dina Azure-SSIS IR till ett virtuellt nätverk måste du konfigurera det virtuella nätverket. Lägg till följande skript för att automatiskt konfigurera behörigheter och inställningar för virtuella nätverk för din Azure-SSIS IR att ansluta till det virtuella nätverket:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Skapa en Azure-SSIS IR och ansluta den till ett virtuellt nätverk

Du kan skapa en Azure-SSIS IR och ansluta den till ett virtuellt nätverk på samma tid. Fullständiga skript och instruktioner finns i [skapa en Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Ansluta en befintlig Azure-SSIS IR till ett virtuellt nätverk

I artikeln [skapa en Azure-SSIS IR](create-azure-ssis-integration-runtime.md) visas hur du skapar en Azure-SSIS IR och ansluter den till ett virtuellt nätverk i samma skript. Om du redan har ett Azure-SSIS IR följer du de här stegen för att ansluta den till det virtuella nätverket: 
1. Stoppa Azure-SSIS IR. 
1. Konfigurera Azure-SSIS IR att ansluta till det virtuella nätverket. 
1. Starta Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>Stoppa Azure-SSIS IR

Du måste stoppa Azure-SSIS IR innan du kan ansluta den till ett virtuellt nätverk. Detta kommando frigör alla noder och avslutar faktureringen:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurera inställningar för virtuella nätverk för Azure-SSIS IR att ansluta till

Använd följande skript om du vill konfigurera inställningar för det virtuella nätverk som Azure-SSIS ska ansluta till: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurera Azure-SSIS IR

Kör kommandot för att ansluta dina Azure-SSIS IR till ett virtuellt nätverk `Set-AzDataFactoryV2IntegrationRuntime` : 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Starta Azure-SSIS IR

Kör följande kommando för att starta Azure-SSIS IR: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Det här kommandot tar 20 till 30 minuter att slutföra.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-SSIS IR finns i följande artiklar: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller allmän begreppsmässig information om IRs, inklusive Azure-SSIS IR. 
- [Självstudie: Distribuera SSIS-paket till Azure](./tutorial-deploy-ssis-packages-azure.md). Den här självstudien innehåller stegvisa instruktioner för att skapa din Azure-SSIS IR. Den använder Azure SQL Database som värd för SSIS-katalogen. 
- [Skapa en Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Den här artikeln expanderar i självstudien. Det ger instruktioner om hur du använder Azure SQL Database med tjänst slut punkter för virtuella nätverk eller SQL-hanterad instans i ett virtuellt nätverk som värd för SSIS-katalogen. Det visar hur du ansluter Azure-SSIS IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om din Azure-SSIS IR. Den innehåller status beskrivningar för den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort Azure-SSIS IR. Det visar också hur du kan skala ut Azure-SSIS IR genom att lägga till noder.
