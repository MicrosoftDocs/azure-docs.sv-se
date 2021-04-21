---
title: Integrera med Azure Private Link-tjänsten
description: Lär dig hur du integrerar Azure Key Vault med Azure Private Link Service
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: e5ddffb17c8f5acf16cf89dd58c634b6e404bf7b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749554"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrera Key Vault med Azure Private Link

med Azure Private Link Service kan du komma åt Azure-tjänster (till exempel Azure Key Vault, Azure Storage och Azure Cosmos DB) och Azure-värdtjänster för kunder/partner via en privat slutpunkt i ditt virtuella nätverk.

En privat Azure-slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gatewayer, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontroll.

Mer information finns i [Vad är Azure Private Link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill integrera ett nyckelvalv med Azure Private Link behöver du följande:

- Ett nyckelvalv.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket.
- Ägar- eller deltagarbehörigheter för både nyckelvalvet och det virtuella nätverket.

Din privata slutpunkt och ditt virtuella nätverk måste finnas i samma region. När du väljer en region för den privata slutpunkten med hjälp av portalen filtreras endast virtuella nätverk i den regionen automatiskt. Ditt nyckelvalv kan finnas i en annan region.

Din privata slutpunkt använder en privat IP-adress i ditt virtuella nätverk.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Upprätta en privat länkanslutning till Key Vault med Azure Portal 

Skapa först ett virtuellt nätverk genom att följa stegen i [Skapa ett virtuellt nätverk med hjälp av Azure Portal](../../virtual-network/quick-create-portal.md)

Du kan sedan antingen skapa ett nytt nyckelvalv eller upprätta en privat länkanslutning till ett befintligt nyckelvalv.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Skapa ett nytt nyckelvalv och upprätta en privat länkanslutning

Du kan skapa ett nytt nyckelvalv [med Azure Portal,](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md).

När du har konfigurerat grunderna för nyckelvalvet väljer du fliken Nätverk och följer dessa steg:

1. Välj alternativknappen Privat slutpunkt på fliken Nätverk.
1. Klicka på knappen "+ Lägg till" för att lägga till en privat slutpunkt.

    ![Skärmbild som visar fliken Nätverk på sidan Skapa nyckelvalv.](../media/private-link-service-1.png)
 
1. I fältet "Plats" på bladet Skapa privat slutpunkt väljer du den region där det virtuella nätverket finns. 
1. I fältet "Namn" skapar du ett beskrivande namn som gör att du kan identifiera den här privata slutpunkten. 
1. Välj det virtuella nätverk och undernät som du vill att den här privata slutpunkten ska skapas i från den nedrullningsbara menyn. 
1. Låt alternativet "integrera med DNS för privat zon" vara oförändrat.  
1. Välj "OK".

    ![Skärmbild som visar sidan Skapa privat slutpunkt med valda inställningar.](../media/private-link-service-8.png)
 
Nu kan du se den konfigurerade privata slutpunkten. Nu har du möjlighet att ta bort och redigera den här privata slutpunkten. Välj knappen Granska + skapa och skapa nyckelvalvet. Det tar 5–10 minuter för distributionen att slutföras. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Upprätta en privat länkanslutning till ett befintligt nyckelvalv

Om du redan har ett nyckelvalv kan du skapa en privat länkanslutning genom att följa dessa steg:

1. Logga in på Azure-portalen. 
1. I sökfältet skriver du "key vaults" (nyckelvalv)
1. Välj nyckelvalvet i listan som du vill lägga till en privat slutpunkt i.
1. Välj fliken Nätverk under Inställningar
1. Välj fliken Privata slutpunktsanslutningar överst på sidan
1. Välj knappen "+ Privat slutpunkt" längst upp på sidan.

    ![Skärmbild som visar knappen + Privat slutpunkt på sidan Nätverk.](../media/private-link-service-3.png)
    ![Skärmbild som visar fliken Grundläggande inställningar på sidan Skapa en privat slutpunkt (förhandsversion).](../media/private-link-service-4.png)

Du kan välja att skapa en privat slutpunkt för alla Azure-resurser i med hjälp av det här bladet. Du kan antingen använda listmenyerna för att välja en resurstyp och välja en resurs i katalogen, eller så kan du ansluta till valfri Azure-resurs med hjälp av ett resurs-ID. Låt alternativet "integrera med DNS för privat zon" vara oförändrat.  

![Skärmbild som visar hur du lägger till en privat slutpunkt med det aktuella bladet. ](../media/private-link-service-3.png)
 ![ Skärmbild som visar ett exempel på sidan Skapa en privat slutpunkt (förhandsversion).](../media/private-link-service-4.png)

När du skapar en privat slutpunkt måste anslutningen godkännas. Om resursen som du skapar en privat slutpunkt för finns i din katalog kan du godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta tills ägaren av resursen godkänner din anslutningsbegäran.

Det finns fyra etablerings tillstånd:

| Åtgärden Ange tjänst | Tillstånd för privat slutpunkt för tjänstkonsument | Beskrivning |
|--|--|--|
| Ingen | Väntar | Anslutningen skapas manuellt och väntar på godkännande från Private Link resursägaren. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är redo att användas. |
| Avvisa | Avslagen | Anslutningen avvisades av den privata länkens resursägare. |
| Ta bort | Frånkopplad | Anslutningen har tagits bort av ägaren till den privata länkresursen. Den privata slutpunkten blir informativ och bör tas bort för rensning. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Hantera en privat slutpunktsanslutning till en Key Vault med hjälp av Azure Portal 

1. Logga in på Azure Portal.
1. I sökfältet skriver du "key vaults" (nyckelvalv)
1. Välj det nyckelvalv som du vill hantera.
1. Välj fliken Nätverk.
1. Om det finns några väntande anslutningar visas en anslutning listad med "Väntar" i etableringstillståndet. 
1. Välj den privata slutpunkt som du vill godkänna
1. Välj knappen Godkänn.
1. Om det finns några privata slutpunktsanslutningar som du vill avvisa, oavsett om det är en väntande begäran eller en befintlig anslutning, väljer du anslutningen och klickar på knappen "Avvisa".

    ![Bild](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>Upprätta en privat länkanslutning till Key Vault cli (inledande installation)

```azurecli
az login                                                         # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                  # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                  # Create a new Resource Group
az provider register -n Microsoft.KeyVault                       # Register KeyVault as a provider
az keyvault create -n {VAULT NAME} -g {RG} -l {REGION}           # Create a Key Vault
az keyvault update -n {VAULT NAME} -g {RG} --default-action deny # Turn on Key Vault Firewall
az network vnet create -g {RG} -n {vNet NAME} -location {REGION} # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="create-a-private-endpoint-automatically-approve"></a>Skapa en privat slutpunkt (godkänn automatiskt) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Skapa en privat slutpunkt (begär godkännande manuellt) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Hantera Private Link anslutningar

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Lägga Privat DNS poster
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.vaultcore.azure.net" -n {KEY VAULT NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {KEY VAULT NAME}.vault.azure.net
nslookup {KEY VAULT NAME}.privatelink.vaultcore.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Kontrollera att den privata länkanslutningen fungerar

Du bör kontrollera att resurserna i samma undernät för den privata slutpunktsresursen ansluter till ditt nyckelvalv via en privat IP-adress och att de har rätt integrering av privata DNS-zoner.

Skapa först en virtuell dator genom att följa stegen i [Skapa en virtuell Windows-dator i Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

På fliken Nätverk:

1. Ange Virtuellt nätverk och Undernät. Du kan skapa ett nytt virtuellt nätverk eller välja ett befintligt. Om du väljer en befintlig kontrollerar du att regionen matchar.
1. Ange en offentlig IP-resurs.
1. I nätverkssäkerhetsgruppen "Nätverkskort" väljer du "Ingen".
1. I "Belastningsutjämning" väljer du "Nej".

Öppna kommandoraden och kör följande kommando:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett nyckelvalv över en offentlig slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett nyckelvalv över en privat slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Felsökningsguide

* Kontrollera att den privata slutpunkten är i godkänt tillstånd. 
    1. Du kan kontrollera och åtgärda detta i Azure Portal. Öppna Key Vault-resursen och klicka på alternativet Nätverk. 
    2. Välj sedan fliken Privata slutpunktsanslutningar. 
    3. Kontrollera att anslutningstillståndet är Godkänd och att etableringstillståndet är Lyckades. 
    4. Du kan också navigera till resursen för den privata slutpunkten och granska samma egenskaper där och dubbelkolla att det virtuella nätverket matchar det som du använder.

* Kontrollera att du har en Privat DNS Zonresurs. 
    1. Du måste ha Privat DNS zonresurs med det exakta namnet: privatelink.vaultcore.azure.net. 
    2. Information om hur du ställer in detta finns på följande länk. [Privat DNS zoner](../../dns/private-dns-privatednszone.md)
    
* Kontrollera att den privata DNS-zonen inte är länkad till det virtuella nätverket. Det här kan vara problemet om du fortfarande får den offentliga IP-adressen returnerad. 
    1. Om DNS för den privata zonen inte är länkad till det virtuella nätverket returnerar DNS-frågan från det virtuella nätverket nyckelvalvets offentliga IP-adress. 
    2. Gå till Privat DNS zonresursen i Azure Portal och klicka på alternativet för virtuella nätverkslänkar. 
    4. Det virtuella nätverk som ska utföra anrop till nyckelvalvet måste anges. 
    5. Om den inte finns där lägger du till den. 
    6. Detaljerade anvisningar finns i följande dokument Länk [till Virtual Network till Privat DNS zon](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Kontrollera att den Privat DNS Zone inte saknar en A-post för nyckelvalvet. 
    1. Gå till Privat DNS zon. 
    2. Klicka på Översikt och kontrollera om det finns en post med det enkla namnet på ditt nyckelvalv (t.ex. fabrikam). Ange inget suffix.
    3. Kontrollera stavningen och skapa eller åtgärda A-posten. Du kan använda ett TTL-värde på 3 600 (1 timme). 
    4. Se till att du anger rätt privat IP-adress. 
    
* Kontrollera att A-posten har rätt IP-adress. 
    1. Du kan bekräfta IP-adressen genom att öppna resursen Privat slutpunkt i Azure Portal.
    2. Gå till resursen Microsoft.Network/privateEndpoints i Azure Portal (inte Key Vault-resursen)
    3. På översiktssidan letar du upp Nätverksgränssnitt och klickar på länken. 
    4. Länken visar översikten över nätverkskortresursen, som innehåller egenskapen Privat IP-adress. 
    5. Kontrollera att det här är rätt IP-adress som anges i A-posten.

## <a name="limitations-and-design-considerations"></a>Begränsningar och designöverväganden

> [!NOTE]
> Antalet nyckelvalv med privata slutpunkter aktiverat per prenumeration är en justerbar gräns. Den gräns som visas nedan är standardgränsen. Om du vill begära en ökning av gränsen för din tjänst skickar du ett e-postmeddelande till akv-privatelink@microsoft.com . Vi godkänner dessa begäranden från fall till fall.

**Prissättning:** Prisinformation finns i [Azure Private Link priser.](https://azure.microsoft.com/pricing/details/private-link/)

**Begränsningar:** Privat slutpunkt för Azure Key Vault är endast tillgänglig i offentliga Azure-regioner.

**Maximalt antal privata slutpunkter per Key Vault:** 64.

**Standardantal nyckelvalv med privata slutpunkter per prenumeration:** 400.

Mer information finns i [Azure Private Link tjänst: Begränsningar](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Private Link](../../private-link/private-link-service-overview.md)
- Läs mer om [Azure Key Vault](overview.md)
