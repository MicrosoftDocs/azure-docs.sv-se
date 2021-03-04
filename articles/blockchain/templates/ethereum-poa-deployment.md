---
title: Distribuera Ethereum proof-of-Authority – Solution Template på Azure
description: Använd Ethereum-lösningen för att distribuera och konfigurera ett Ethereum-nätverk med flera medlemmar i Azure
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 70c9498bae9117585963e111bea4f1e127cab232
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097949"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Distribuera Ethereum proof-of-Authority – Solution Template på Azure

Du kan använda [Ethereum-mallen för förhands granskning av Azure-lösningar](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) för att distribuera, konfigurera och styra ett Ethereum-nätverk med flera medlemmar med minimala kunskaper om Azure och Ethereum.

Lösnings mal len kan användas av varje konsortiums medlem för att etablera ett blockchain nätverk med hjälp av Microsoft Azure beräknings-, nätverks-och lagrings tjänster. Varje medlem i konsortiets nätverk består av en uppsättning belastningsutjämnade validator-noder som ett program eller en användare kan interagera med för att skicka Ethereum-transaktioner.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Välj en Azure blockchain-lösning

Innan du väljer att använda Ethereum proof-of-Authority-, jämför ditt scenario med vanliga användnings fall av tillgängliga alternativ för Azure blockchain.

> [!IMPORTANT]
> Överväg att använda [Azure blockchain-tjänsten](../service/overview.md) i stället för Ethereum på Azures lösnings mall. Azure blockchain-tjänsten är en hanterad Azure-tjänst som stöds. Paritets Ethereum övergick till community driven utveckling och underhåll. Mer information finns i [överföra paritets Ethereum till OPENETHEREUM Dao](https://www.parity.io/parity-ethereum-openethereum-dao/).

Alternativ | Tjänst modell | Vanligt användnings fall
-------|---------------|-----------------
Lösningsmallar | IaaS | Solution templates är Azure Resource Manager mallar som du kan använda för att etablera en helt konfigurerad blockchain-nätverkstopologi. Mallarna distribuerar och konfigurerar Microsoft Azure beräknings-, nätverks-och lagrings tjänster för en specifik nätverks typ för blockchain. Solution templates tillhandahålls utan något service nivå avtal. Använd [sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html) för support.
[Azure Blockchain Service](../service/overview.md) | PaaS | För hands versionen av Azure blockchain service fören klar investeringen, hanteringen och styrningen av konsortiet blockchain-nätverk. Använd Azure blockchain-tjänsten för lösningar som kräver PaaS, konsortiets hantering eller kontrakt och transaktions sekretess.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS och PaaS | Azure blockchain Workbench Preview är en samling Azure-tjänster och-funktioner som är utformade för att hjälpa dig att skapa och distribuera blockchain-program för att dela affärs processer och data med andra organisationer. Använd Azure blockchain Workbench för prototyp av en blockchain-lösning eller ett koncept för blockchain-program. Azure Blockchain Workbench tillhandahålls utan serviceavtal. Använd [sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html) för support.

## <a name="solution-architecture"></a>Lösningsarkitektur

Med hjälp av mallen för Ethereum-lösningar kan du distribuera en enda eller flera regioner-baserade Ethereum-nätverk med flera medlemmar.

![distributions arkitektur](./media/ethereum-poa-deployment/deployment-architecture.png)

Varje distribution av konsortiet ingår:

* Virtual Machines för att köra PoA-verifierarna
* Azure Load Balancer för distribution av RPC-, peering-och styrnings DApp-begäranden
* Azure Key Vault för att skydda verifieraren identiteter
* Azure Storage för att vara värd för beständig nätverksinformation och koordinera leasing
* Azure Monitor för agg regering av loggar och prestanda statistik
* VNet Gateway (valfritt) för att tillåta VPN-anslutningar över privata virtuella nätverk

Som standard är RPC-och peering-slutpunkterna tillgängliga över offentlig IP för att möjliggöra förenklad anslutning mellan prenumerationer och moln. För åtkomst på program nivå kan du använda [paritetens godkännande avtal](https://openethereum.github.io/Permissioning.html). Nätverk som distribueras bakom VPN, som utnyttjar VNet-gatewayer för anslutning mellan prenumerationer stöds. Eftersom VPN-och VNet-distributioner är mer komplexa kan du behöva börja med en offentlig IP-modell vid prototyp av en lösning.

Docker-behållare används för tillförlitlighet och modulärhet. Azure Container Registry används för att vara värd för och hantera versions avbildningar som en del av varje distribution. Behållar avbildningarna består av:

* Orchestrator – genererar identiteter och styrnings avtal. Lagrar identiteter i ett identitets lager.
* Paritets klient – lånar identitet från identitets lagret. Identifierar och ansluter till peer-datorer.
* EthStats-agent – samlar in lokala loggar och statistik via RPC och push-överför information till Azure Monitor.
* Styrning DApp – webb gränssnitt för att interagera med styrnings avtal.

### <a name="validator-nodes"></a>Validator-noder

I proof-of-Authority-protokollet tar verifierade noder platsen för traditionella Miner-noder. Varje verifierare har en unik Ethereum-identitet som gör det möjligt att delta i processen för att skapa block. Varje konsortiums medlem kan etablera två eller fler verifierade noder i fem regioner för GEO-redundans. Validator-noder kommunicerar med andra validator-noder för att komma till enighet om status för den underliggande distribuerade redovisningen. För att säkerställa en rättvis medverkan i nätverket, tillåts varje konsortiums medlem inte att använda fler verifierare än den första medlemmen i nätverket. Om den första medlemmen till exempel distribuerar tre verifierare får varje medlem bara ha upp till tre verifierare.

### <a name="identity-store"></a>Identitets Arkiv

Ett identitets lager distribueras i varje medlems prenumeration som har genererat Ethereum-identiteter på ett säkert sätt. För varje verifierare genererar Orchestration-behållaren en privat Ethereum-nyckel och lagrar den i Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Distribuera Ethereum Consortium-nätverk

I den här genom gången antar vi att du skapar ett Ethereum konsortium-nätverk med flera parter. Följande flöde är ett exempel på en distribution med flera parter:

1. Tre medlemmar varje generera ett Ethereum-konto med MetaMask
1. *Medlem A* distribuerar Ethereum POA, vilket ger sin Ethereum offentliga adress
1. *Medlem A* innehåller konsortiets URL till *medlem B* och *medlem C*
1. *Medlem B* och *medlem C* -distribution, Ethereum POA, som tillhandahåller sin Ethereum offentliga adress och *medlem A*: s konsortiums-URL
1. *Medlem A* röster i *medlem B* som administratör
1. *Medlem A* och *medlem B* både röst *medlem C* som administratör

I nästa avsnitt visas hur du konfigurerar den första medlemmens storlek i nätverket.

### <a name="create-resource"></a>Skapa resurs

I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs** i det övre vänstra hörnet.

Välj **blockchain**  >  **Ethereum proof-of-Authority Consortium (för hands version)**.

### <a name="basics"></a>Grundläggande inställningar

Under **grunderna** anger du värden för standard parametrar för alla distributioner.

![Grundläggande inställningar](./media/ethereum-poa-deployment/basic-blade.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Skapa ett nytt nätverk eller Anslut till ett befintligt nätverk | Du kan skapa ett nytt konsortiums nätverk eller ansluta till ett befintligt konsortiums nätverk. För att kunna ansluta till ett befintligt nätverk krävs ytterligare parametrar. | Skapa ny
E-postadress | Du får ett e-postmeddelande när distributionen är klar med information om distributionen. | En giltig e-postadress
Användar namn för virtuell dator | Administratörs användar namn för varje distribuerad virtuell dator | 1-64 alfanumeriska tecken
Autentiseringstyp | Metoden för att autentisera till den virtuella datorn. | Lösenord
Lösenord | Lösen ordet för administratörs kontot för var och en av de virtuella datorerna som distribueras. Alla virtuella datorer har till början samma lösen ord. Du kan ändra lösen ordet efter etableringen. | 12-72 tecken 
Prenumeration | Den prenumeration som används för att distribuera konsortiets nätverk |
Resursgrupp| Den resurs grupp som används för att distribuera konsortial nätverket. | myResourceGroup
Location | Azure-regionen för resurs gruppen. | USA, västra 2

Välj **OK**.

### <a name="deployment-regions"></a>Distributions regioner

Under *distributions regioner* anger du antalet regioner och platser för var och en. Du kan distribuera högst fem regioner. Den första regionen ska överensstämma med avsnittet för resurs gruppens plats från *grunderna* . För utvecklings-eller test nätverk kan du använda en enda region per medlem. För produktion kan du distribuera över två eller flera regioner för hög tillgänglighet.

![distributions regioner](./media/ethereum-poa-deployment/deployment-regions.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Antal regioner|Antal regioner för att distribuera konsortiets nätverk| 2
Första regionen | Första regionen för att distribuera konsortiet nätverk | USA, västra 2
Andra regionen | Andra regionen för att distribuera konsortiets nätverk. Ytterligare regioner visas när antalet regioner är två eller fler. | USA, östra 2

Välj **OK**.

### <a name="network-size-and-performance"></a>Nätverks storlek och prestanda

Under *nätverks storlek och prestanda* anger du indata för storleken på konsortiets nätverk. Lagrings storleken för validator-noden styr den potentiella storleken på blockchain. Storleken kan ändras efter distributionen.

![Nätverks storlek och prestanda](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Antal noder för belastningsutjämnad verifiering | Antalet verifierade noder som ska etableras som en del av nätverket. | 2
Lagrings prestanda för validator-nod | Den typ av hanterad disk som ska användas för var och en av de distribuerade validator-noderna. Mer information om priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/managed-disks/) | Standard SSD
Virtuell dator storlek för validator-nod | Den virtuella dator storleken som används för verifierade noder. Mer information om priser finns i [priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Standard D2 v3

Den virtuella datorn och lagrings nivån påverkar nätverks prestanda.  Använd följande tabell för att få hjälp att välja kostnads effektivitet:

SKU för virtuell dator|Lagrings nivå|Pris|Dataflöde|Svarstid
---|---|---|---|---
F1|Standard SSD|börjar|börjar|hög
D2_v3|Standard SSD|medel|medel|medel
F16-enheter|Premium SSD|hög|hög|börjar

Välj **OK**.

### <a name="ethereum-settings"></a>Ethereum-inställningar

Under *Inställningar för Ethereum* anger du Ethereum konfigurations inställningar.

![Ethereum-inställningar](./media/ethereum-poa-deployment/ethereum-settings.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Konsortiums medlems-ID | Det ID som är kopplat till varje medlem som ingår i konsortiets nätverk. Den används för att konfigurera IP-adressutrymme för att undvika kollision. För ett privat nätverk ska medlems-ID: t vara unikt i olika organisationer i samma nätverk.  Ett unikt medlems-ID krävs även om samma organisation distribuerar till flera regioner. Anteckna värdet för den här parametern eftersom du måste dela den med andra medlemmar för att se till att det inte finns någon kollision. Giltigt intervall är 0 till 255. | 0
Nätverks-ID | Nätverks-ID för konsortiet Ethereum-nätverk som ska distribueras. Varje Ethereum nätverk har sitt eget nätverks-ID, med 1 som ID för det offentliga nätverket. Giltigt intervall är 5 till 999 999 999 | 10101010
Admin Ethereum-adress | Ethereum-konto adress som används för att delta i PoA-styrning. Du kan använda MetaMask för att generera en Ethereum-adress. |
Avancerade alternativ | Avancerade alternativ för Ethereum-inställningar | Aktivera
Distribuera med hjälp av offentlig IP | Om du väljer privat VNet, distribueras nätverket bakom en VNet-gateway och tar bort peering-åtkomst. För privata virtuella nätverk måste alla medlemmar använda en VNet-Gateway för att anslutningen ska vara kompatibel. | Offentlig IP-adress
Block gas gräns | Nätverkets start Blocks gräns. | 50000000
Blockera omförslutning av period (SEK) | Den frekvens med vilken tomma block skapas när det inte finns några transaktioner i nätverket. En högre frekvens kommer att ha snabbare slut för and öka lagrings kostnaderna. | 15
Transaktions godkännande kontrakt | Bytekod för transaktions behörighets avtal. Begränsar distribution och körning av smarta kontrakt till en lista över tillåtna Ethereum-konton. |

Välj **OK**.

### <a name="monitoring"></a>Övervakning

Med övervakning kan du konfigurera en logg resurs för nätverket. Övervaknings agenten samlar in och samlar in användbara mått och loggar från nätverket och ger möjlighet att snabbt kontrol lera nätverks hälsan eller fel söknings problem.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Övervakning | Alternativ för att aktivera övervakning | Aktivera
Ansluta till befintliga Azure Monitor loggar | Alternativ för att skapa en ny Azure Monitor loggar instans eller ansluta till en befintlig instans | Skapa ny
Location | Den region där den nya instansen distribueras | East US
Befintligt logganalys-arbetsyte-ID (Anslut till befintliga Azure Monitor loggar = koppling befintlig)|Arbetsyte-ID för den befintliga Azure Monitor loggar instansen||NA
Befintlig Log Analytics primär nyckel (Anslut till befintliga Azure Monitor loggar = koppling befintlig)|Den primära nyckel som används för att ansluta till den befintliga Azure Monitor loggar instansen||NA

Välj **OK**.

### <a name="summary"></a>Sammanfattning

Klicka dig igenom sammanfattningen för att granska de angivna indata och köra grundläggande verifiering före distribution. Innan du distribuerar kan du hämta mallen och parametrarna.

Välj **skapa** för att distribuera.

Om distributionen inkluderar VNet-gatewayer kan distributionen ta 45 till 50 minuter.

## <a name="deployment-output"></a>Distributions utdata

När distributionen har slutförts kan du komma åt de parametrar som krävs med hjälp av Azure Portal.

### <a name="confirmation-email"></a>Bekräfta e-post

Om du anger en e-postadress ([avsnittet grundläggande](#basics)) skickas ett e-postmeddelande som innehåller distributions information och länkar till den här dokumentationen.

![e-postdistribution](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

När distributionen har slutförts och alla resurser har etablerats kan du Visa utdataparametrar i resurs gruppen.

1. Gå till resurs gruppen i portalen.
1. Välj **översikt > distributioner**.

    ![Översikt över resurs grupp](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Välj den **Microsoft-Azure-blockchain. Azure-blockchain-eter-...-** distributionen.
1. Välj avsnittet **utdata** .

    ![Distributions utdata](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Växande konsortiet

Om du vill expandera ditt konsortium måste du först ansluta det fysiska nätverket. Om du distribuerar bakom en VPN-anslutning kan du läsa avsnittet [ansluta VNet Gateway](#connecting-vnet-gateways) konfigurera nätverks anslutningen som en del av den nya medlems distributionen. När distributionen är klar använder du [styrnings DApp](#governance-dapp) för att bli nätverks administratör.

### <a name="new-member-deployment"></a>Ny medlems distribution

Dela följande information med den anslutna medlemmen. Informationen finns i e-postmeddelandet efter distributionen eller i portalen.

* Data-URL för konsortiet
* Antalet noder som du har distribuerat
* Resurs-ID för VNet-Gateway (om du använder VPN)

Den distribuerade medlemmen bör använda samma Ethereum-mall för certifikat utfärdare som används för att distribuera nätverks närvaron med hjälp av följande rikt linjer:

* Välj **Anslut befintlig**
* Välj samma antal validator-noder som resten av medlemmarna i nätverket för att säkerställa en rättvis representation
* Använd samma administratörs Ethereum-adress
* Använd den angivna *konsortiet data-URL: en* i *Ethereum-inställningarna*
* Om resten av nätverket ligger bakom en VPN väljer du **privat VNet** under avsnittet Avancerat

### <a name="connecting-vnet-gateways"></a>Ansluta VNet-gatewayer

Det här avsnittet krävs endast om du har distribuerat med ett privat VNet. Du kan hoppa över det här avsnittet om du använder offentliga IP-adresser.

För ett privat nätverk är de olika medlemmarna anslutna via VNet Gateway-anslutningar. Innan en medlem kan ansluta till nätverket och se transaktions trafik, måste en befintlig medlem utföra en slutgiltig konfiguration på sin VPN-gateway för att godkänna anslutningen. Ethereum-noderna i den anslutna medlemmen körs inte förrän en anslutning har upprättats. Du kan minska risken för en enskild felpunkt genom att skapa redundanta nätverks anslutningar i konsortiet.

När den nya medlemmen har distribuerats måste den befintliga medlemmen slutföra den dubbelriktade anslutningen genom att konfigurera en VNet Gateway-anslutning till den nya medlemmen. Befintliga medlems behov:

* VNet Gateway-ResourceID för den anslutna medlemmen. Se [distributions utdata](#deployment-output).
* Den delade anslutnings nyckeln.

Den befintliga medlemmen måste köra följande PowerShell-skript för att slutföra anslutningen. Du kan använda Azure Cloud Shell som finns i det övre högra navigerings fältet i portalen.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="governance-dapp"></a>Styrnings DApp

När det gäller certifikat beviset är decentraliserad styrning. Eftersom bevis på auktoritet förlitar sig på en tillåten lista över nätverks certifikat för att hålla nätverket felfritt, är det viktigt att tillhandahålla en rättvis mekanism för att göra ändringar i behörighets listan. Varje distribution levereras med en uppsättning smarta kontrakt och portaler för styrning av den här listan över tillåtna användare. När en föreslagen ändring når en majoritets röst av konsortiets medlemmar, är ändringen i sitt beslut. Röstning gör det möjligt att lägga till eller komprometterade deltagare som kan tas bort på ett transparent sätt som uppmuntrar ett Honest nätverk.

Styrnings DApp är en uppsättning fördistribuerade [smarta kontrakt](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) och ett webb program som används för att styra myndigheterna i nätverket. Utfärdarna är indelade i admin identiteter och validator-noder.
Administratörer har möjlighet att delegera konsensus deltagande till en uppsättning validator-noder. Administratörer kan också rösta andra administratörer i eller ut ur nätverket.

![Styrnings DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Decentraliserad styrning:** Ändringar i nätverks myndigheterna administreras genom en röst markering genom att välja administratörer.
* **Delegering av verifierare:** Myndigheter kan hantera sina verifierade noder som ställs in i varje PoA-distribution.
* **Gransknings bar ändrings historik:** Varje ändring registreras på blockchain och ger insyn och gransknings möjlighet.

### <a name="getting-started-with-governance"></a>Komma igång med styrning

Om du vill utföra någon typ av transaktioner via styrnings DApp måste du använda en Ethereum plån bok. Den enklaste metoden är att använda en plån bok i en webbläsare, till exempel [MetaMask](https://metamask.io); men eftersom dessa smarta kontrakt har distribuerats i nätverket kan du också automatisera dina interaktioner till styrnings kontraktet.

När du har installerat MetaMask navigerar du till styrnings-DApp i webbläsaren.  Du kan hitta URL: en via Azure Portal i distributionens utdata.  Om du inte har en plån bok som är installerad i webbläsaren kan du inte utföra några åtgärder. Du kan dock Visa administratörs tillstånd.  

### <a name="becoming-an-admin"></a>Bli administratör

Om du är den första medlem som distribueras i nätverket blir du automatiskt en administratör och dina paritets-noder visas som verifierare. Om du ansluter till nätverket måste du ha röstat in som administratör med en majoritet (större än 50%) av den befintliga administratörs uppsättningen. Om du väljer att inte bli administratör synkroniserar noderna fortfarande och validerar blockchain. de deltar dock inte i processen för att skapa block. Starta röstnings processen och bli administratör genom att välja **nominera** och ange din Ethereum-adress och alias.

![Utse](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Beds

Om du väljer fliken **kandidater** visas den aktuella uppsättningen av kandidat administratörer.  När en kandidat når majoritets omröstning av de aktuella administratörerna, kommer kandidat gruppen att uppgraderas till en administratör.  Om du vill rösta på en kandidat markerar du raden och väljer **rösta i**. Om du ändrar dig för en röst väljer du kandidaten och väljer **återkalla röst**.

![Beds](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administratörer

Fliken **Administratörer** visar den aktuella uppsättningen administratörer och ger dig möjlighet att rösta mot.  När en administratör har förlorat mer än 50% stöd tas de bort som administratör i nätverket. Alla validerade noder som administratören äger förlorar verifierings status och blir transaktions noder i nätverket. En administratör kan tas bort av valfritt antal orsaker. Det är dock upp till konsortiet att enas om en princip i förväg.

![Administratörer](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Verifierare

Om du väljer fliken **verifierare** visas de aktuella distribuerade paritets noderna för instansen och deras aktuella status (nodtyp). Varje konsortiums medlem har en annan uppsättning verifierare i listan, eftersom den här vyn representerar den aktuella distribuerade konsortiet medlem. Om instansen nyligen distribueras och du inte har lagt till dina verifierare får du möjlighet att **lägga till verifierare**. Genom att lägga till validerare väljs automatiskt en regionalt bal anse rad uppsättning paritets noder och tilldelar dem till din verifierings uppsättning. Om du har distribuerat fler noder än den tillåtna kapaciteten blir de återstående noderna Transaction-noder i nätverket.

Adressen för varje verifierare tilldelas automatiskt via [identitets arkivet](#identity-store) i Azure.  Om en nod kraschar, överlåter den sin identitet, vilket gör att en annan nod i distributionen kan ta sin plats. Den här processen säkerställer att ditt samförstånds bidrag är högt tillgängligt.

![Verifierare](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Namn på konsortium

Alla administratörer kan uppdatera namnet på konsortiet.  Välj kugg hjuls ikonen längst upp till vänster för att uppdatera namnet på konsortiet.

### <a name="account-menu"></a>Menyn konto

Längst upp till höger är ditt Ethereum-konto-alias och Identicon.  Om du är administratör har du möjlighet att uppdatera ditt alias.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>Support och feedback<a id="tutorials"></a>

För Azure blockchain News går du till [Azure blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/) för att hålla dig uppdaterad om blockchain service-erbjudanden och information från Azures teknik team för blockchain.

För att ge feedback på produkter eller för att begära nya funktioner, post eller rösta för en idé via [Azure feedback-forumet för blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Community-support

Engagera med Microsoft-tekniker och Azure blockchain community-experter.

* [Sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html). Teknisk support för blockchain-mallar är begränsad till distributions problem.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure blockchain-lösningar finns i [dokumentationen för Azure blockchain](../index.yml).
