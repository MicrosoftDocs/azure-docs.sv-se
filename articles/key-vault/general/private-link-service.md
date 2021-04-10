---
title: Integrera med Azure Private Link-tjänsten
description: Lär dig hur du integrerar Azure Key Vault med Azure Private Link service
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 11/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 78a57cb896f09b686d35bedd79442025ebcb7c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604601"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrera Key Vault med Azure Private Link

Azure Private Link service ger dig åtkomst till Azure-tjänster (till exempel Azure Key Vault, Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket.

En privat Azure-slutpunkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Mer information finns i [Vad är en privat Azure-länk?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill integrera ett nyckel valv med en privat Azure-länk behöver du följande:

- Ett nyckel valv.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket.
- Ägar-eller deltagar behörighet för både nyckel valvet och det virtuella nätverket.

Din privata slut punkt och det virtuella nätverket måste finnas i samma region. När du väljer en region för den privata slut punkten med hjälp av portalen filtreras automatiskt endast virtuella nätverk i den regionen. Ditt nyckel valv kan finnas i en annan region.

Din privata slut punkt använder en privat IP-adress i det virtuella nätverket.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Upprätta en anslutning för privat anslutning till Key Vault med hjälp av Azure Portal 

Skapa först ett virtuellt nätverk genom att följa stegen i [skapa ett virtuellt nätverk med hjälp av Azure Portal](../../virtual-network/quick-create-portal.md)

Du kan sedan antingen skapa ett nytt nyckel valv eller upprätta en privat länk anslutning till ett befintligt nyckel valv.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Skapa ett nytt nyckel valv och upprätta en privat länk anslutning

Du kan skapa ett nytt nyckel valv med [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md).

När du har konfigurerat grunderna i Key Vault väljer du fliken nätverk och följer de här stegen:

1. Välj alternativ knappen privat slut punkt på fliken nätverk.
1. Klicka på knappen + Lägg till för att lägga till en privat slut punkt.

    ![Skärm bild som visar fliken nätverk på sidan Skapa nyckel valv.](../media/private-link-service-1.png)
 
1. I fältet "plats" på bladet skapa privat slut punkt väljer du den region där det virtuella nätverket finns. 
1. I fältet namn skapar du ett beskrivande namn som gör att du kan identifiera den här privata slut punkten. 
1. Välj det virtuella nätverk och undernät som du vill att den här privata slut punkten ska skapas i från List menyn. 
1. Lämna alternativet "integrera med den privata zonens DNS" oförändrat.  
1. Välj OK.

    ![Skärm bild som visar sidan Skapa privat slut punkt med valda inställningar.](../media/private-link-service-8.png)
 
Nu kommer du att kunna se den konfigurerade privata slut punkten. Nu har du möjlighet att ta bort och redigera den här privata slut punkten. Välj knappen "granska + skapa" och skapa nyckel valvet. Det tar 5-10 minuter för distributionen att slutföras. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Upprätta en privat länk anslutning till ett befintligt nyckel valv

Om du redan har ett nyckel valv kan du skapa en privat länk anslutning genom att följa dessa steg:

1. Logga in på Azure-portalen. 
1. I Sök fältet skriver du in "nyckel valv"
1. Välj nyckel valvet i listan som du vill lägga till en privat slut punkt för.
1. Välj fliken nätverk under Inställningar
1. Välj fliken anslutningar för privata slut punkter överst på sidan
1. Välj knappen "+ privat slut punkt" överst på sidan.

    ![Skärm bild som visar knappen "+ privat slut punkt" på sidan "nätverk".](../media/private-link-service-3.png)
    ![Skärm bild som visar fliken "grundläggande" på sidan "skapa en privat slut punkt (för hands version).](../media/private-link-service-4.png)

Du kan välja att skapa en privat slut punkt för alla Azure-resurser med hjälp av det här bladet. Du kan antingen använda List menyerna för att välja en resurs typ och välja en resurs i din katalog, eller så kan du ansluta till en Azure-resurs med hjälp av ett resurs-ID. Lämna alternativet "integrera med den privata zonens DNS" oförändrat.  

![Skärm bild som visar hur du lägger till en privat slut punkt med det aktuella bladet. ](../media/private-link-service-3.png)
 ![ Skärm bild som visar ett exempel på sidan "skapa en privat slut punkt (för hands version).](../media/private-link-service-4.png)

När du skapar en privat slut punkt måste anslutningen godkännas. Om den resurs som du skapar en privat slut punkt för finns i din katalog kommer du att kunna godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta tills ägaren av resursen har godkänt din anslutningsbegäran.

Det finns fyra etablerings tillstånd:

| Åtgärd för att tillhandahålla tjänst | Status för privat slut punkt för tjänst förbrukare | Beskrivning |
|--|--|--|
| Ingen | Väntar | Anslutningen skapas manuellt och väntar på godkännande från ägaren till den privata länk resursen. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är redo att användas. |
| Avvisa | Avslagen | Anslutningen avvisades av ägaren till den privata länk resursen. |
| Ta bort | Frånkopplad | Anslutningen togs bort av ägaren till den privata länk resursen, den privata slut punkten blir informativ och bör tas bort för rensning. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Hantera en privat slut punkts anslutning till Key Vault med hjälp av Azure Portal 

1. Logga in på Azure Portal.
1. I Sök fältet skriver du in "nyckel valv"
1. Välj det nyckel valv som du vill hantera.
1. Välj fliken nätverk.
1. Om det finns några anslutningar som väntar, visas en anslutning som anges med "väntar" i etablerings status. 
1. Välj den privata slut punkt som du vill godkänna
1. Välj knappen Godkänn.
1. Om det finns anslutningar för privata slut punkter som du vill avvisa, oavsett om det är en väntande begäran eller en befintlig anslutning, väljer du anslutningen och klickar på knappen "avvisa".

    ![Bild](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>Upprätta en anslutning till en privat länk till Key Vault med CLI (inledande installation)

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

### <a name="create-a-private-endpoint-automatically-approve"></a>Skapa en privat slut punkt (Godkänn automatiskt) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Skapa en privat slut punkt (begär godkännande manuellt) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Hantera anslutningar för privata länkar

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

### <a name="add-private-dns-records"></a>Lägg till Privat DNS poster
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

## <a name="validate-that-the-private-link-connection-works"></a>Kontrol lera att anslutningen till den privata länken fungerar

Du bör kontrol lera att resurserna i samma undernät i den privata slut punkts resursen ansluter till ditt nyckel valv via en privat IP-adress och att de har rätt integrering av privata DNS-zoner.

Börja med att skapa en virtuell dator genom att följa stegen i [skapa en virtuell Windows-dator i Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

På fliken "nätverk":

1. Ange virtuellt nätverk och undernät. Du kan skapa ett nytt virtuellt nätverk eller välja ett befintligt. Om du väljer en befintlig, se till att regionen stämmer.
1. Ange en offentlig IP-resurs.
1. I nätverks säkerhets gruppen NIC väljer du "ingen".
1. I "belastnings utjämning" väljer du "nej".

Öppna kommando raden och kör följande kommando:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett nyckel valv över en offentlig slut punkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett nyckel valv över en privat slut punkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Felsökningsguide

* Kontrol lera att den privata slut punkten är i stadiet godkänt. 
    1. Du kan kontrollera och åtgärda detta i Azure Portal. Öppna Key Vault-resursen och klicka på alternativet Nätverk. 
    2. Välj sedan fliken anslutningar för privat slut punkt. 
    3. Kontrol lera att anslutnings status har godkänts och att etablerings statusen har slutförts. 
    4. Du kan också navigera till den privata slut punkts resursen och granska samma egenskaper där, och kontrol lera att det virtuella nätverket motsvarar det som du använder.

* Kontrol lera att du har en Privat DNS zon resurs. 
    1. Du måste ha en Privat DNS zon resurs med det exakta namnet: privatelink.vaultcore.azure.net. 
    2. Information om hur du konfigurerar detta finns i följande länk. [Privat DNS zoner](../../dns/private-dns-privatednszone.md)
    
* Kontrollera att den privata DNS-zonen inte är länkad till det virtuella nätverket. Detta kan vara ett problem om du fortfarande får den offentliga IP-adressen som returnerades. 
    1. Om DNS för den privata zonen inte är länkat till det virtuella nätverket, returnerar DNS-frågan från det virtuella nätverket den offentliga IP-adressen för nyckel valvet. 
    2. Navigera till resursen Privat DNS zon i Azure Portal och klicka på alternativet virtuella nätverks länkar. 
    4. Det virtuella nätverk som ska utföra anrop till nyckel valvet måste anges. 
    5. Om det inte finns där lägger du till det. 
    6. Detaljerade anvisningar finns i följande dokument [länk Virtual Network till privat DNS zon](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Kontrol lera att zonen Privat DNS inte saknar en A-post för nyckel valvet. 
    1. Gå till sidan Privat DNS zon. 
    2. Klicka på Översikt och kontrollera om det finns en post med det enkla namnet på ditt nyckelvalv (t.ex. fabrikam). Ange inget suffix.
    3. Kontrollera stavningen och skapa eller åtgärda A-posten. Du kan använda ett TTL-värde på 3 600 (1 timme). 
    4. Se till att du anger rätt privat IP-adress. 
    
* Kontrol lera att en post har rätt IP-adress. 
    1. Du kan bekräfta IP-adressen genom att öppna den privata slut punkts resursen i Azure Portal.
    2. Gå till resursen Microsoft.Network/privateEndpoints i Azure Portal (inte Key Vault-resursen)
    3. På sidan Översikt letar du efter nätverks gränssnitt och klickar på länken. 
    4. Länken visar en översikt över NIC-resursen, som innehåller egenskapen privat IP-adress. 
    5. Kontrol lera att det här är rätt IP-adress som anges i A-posten.

## <a name="limitations-and-design-considerations"></a>Begränsningar och design överväganden

> [!NOTE]
> Antalet nyckel valv med privata slut punkter som har Aktiver ATS per prenumeration är en justerbar gräns. Den gräns som visas nedan är standard gränsen. Skicka ett e-postmeddelande till om du vill begära en större gräns för tjänsten akv-privatelink@microsoft.com . Vi kommer att godkänna dessa förfrågningar från fall till fall.

**Priser**: information om priser finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link/).

**Begränsningar**: den privata slut punkten för Azure Key Vault är bara tillgänglig i offentliga Azure-regioner.

**Maximalt antal privata slut punkter per Key Vault**: 64.

**Standard antal nyckel valv med privata slut punkter per prenumeration**: 400.

Mer information finns i [Azure Private Link service: begränsningar](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azures privata länk](../../private-link/private-link-service-overview.md)
- Läs mer om [Azure Key Vault](overview.md)
