---
title: Konfigurera en IP-brandvägg för ditt Azure Cosmos DB-konto
description: Lär dig hur du konfigurerar principer för IP-åtkomstkontroll för brand Väggs stöd på Azure Cosmos-konton.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: b4e01375388f12b828d9adcb1e2ed8851061a0bf
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560737"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurera IP-brandvägg i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

För att skydda data som lagras i ditt konto har Azure Cosmos DB stöd för en hemlig, baserad auktoriserings modell som använder sig av en stark hash-baserad Message Authentication Code (HMAC). Dessutom stöder Azure Cosmos DB IP-baserade åtkomst kontroller för inkommande brand Väggs stöd. Den här modellen liknar brand Väggs reglerna i ett traditionellt databas system och ger ytterligare en säkerhets nivå för ditt konto. Med brand väggar kan du konfigurera ditt Azure Cosmos-konto så att det bara kan nås från en godkänd uppsättning datorer och/eller moln tjänster. Åtkomst till data som lagras i din Azure Cosmos-databas från dessa godkända uppsättningar av datorer och tjänster kräver fortfarande att anroparen presenterar en giltig autentiseringstoken.

## <a name="ip-access-control"></a><a id="ip-access-control-overview"></a>Kontroll av IP-åtkomst

Som standard är ditt Azure Cosmos-konto tillgängligt från Internet, förutsatt att begäran åtföljs av en giltig autentiseringstoken. Om du vill konfigurera IP-principbaserad åtkomst kontroll måste användaren ange en uppsättning IP-adresser eller IP-adressintervall i CIDR-form (Classless Inter-Domain routing) som ska ingå i listan över tillåtna klient-IP-adresser för att få åtkomst till ett angivet Azure Cosmos-konto. När den här konfigurationen används får alla begär Anden som kommer från datorer utanför den här tillåtna listan mottagning 403 (förbjuden) svar. När du använder IP-brandvägg rekommenderar vi att du tillåter Azure Portal att komma åt ditt konto. Åtkomst krävs för att tillåta användning av data Utforskaren samt för att hämta mått för ditt konto som visas på Azure Portal. När du använder data Utforskaren måste du, förutom att tillåta Azure Portal komma åt ditt konto, uppdatera brand Väggs inställningarna för att lägga till din aktuella IP-adress i brand Väggs reglerna. Observera att det kan ta upp till 15 minuter att sprida brand Väggs ändringar och brand väggen kan visa ett inkonsekvent beteende under denna period.

Du kan kombinera IP-baserad brand vägg med åtkomst kontroll för undernät och VNET. Genom att kombinera dem kan du begränsa åtkomsten till alla källor som har en offentlig IP-adress och/eller från ett särskilt undernät i VNET. Om du vill veta mer om hur du använder undernät och VNET-baserad åtkomst kontroll, se [åtkomst Azure Cosmos DB resurser från virtuella nätverk](./how-to-configure-vnet-service-endpoint.md).

För att sammanfatta krävs alltid autentiseringstoken för att få åtkomst till ett Azure Cosmos-konto. Om IP-brandväggen och VNET Access Control List (ACL) inte har kon figurer ATS kan Azure Cosmos-kontot nås med autentiseringstoken. När IP-brandväggen eller VNET-ACL: er eller båda har kon figurer ATS på Azure Cosmos-kontot får du bara begär Anden som härstammar från de källor du har angett (och med autentiseringstoken) get giltiga svar. 

Du kan skydda data som lagras i Azure Cosmos DB-kontot med hjälp av IP-brandväggar. Azure Cosmos DB stöder IP-baserade åtkomst kontroller för inkommande brand Väggs stöd. Du kan ange en IP-brandvägg på Azure Cosmos DB kontot på något av följande sätt:

* Från Azure-portalen
* Deklarativt med hjälp av en Azure Resource Manager-mall
* Program mässigt via Azure CLI eller Azure PowerShell genom att uppdatera egenskapen **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Konfigurera en IP-brandvägg med hjälp av Azure Portal

Om du vill ange princip för IP-åtkomstkontroll i Azure Portal går du till sidan Azure Cosmos DB konto och väljer **brand vägg och virtuella nätverk** på navigerings menyn. Ändra värdet **Tillåt åtkomst från** värde till **valda nätverk** och välj sedan **Spara**.

:::image type="content" source="./media/how-to-configure-firewall/azure-portal-firewall.png" alt-text="Skärm bild som visar hur du öppnar brand Väggs sidan i Azure Portal":::

När kontroll av IP-åtkomst har Aktiver ATS ger Azure Portal möjlighet att ange IP-adresser, IP-adressintervall och växlar. Växlar ger åtkomst till andra Azure-tjänster och Azure Portal. I följande avsnitt finns information om dessa växlar.

> [!NOTE]
> När du har aktiverat en princip för IP-åtkomstkontroll för ditt Azure Cosmos DB-konto avvisas alla förfrågningar till ditt Azure Cosmos DB-konto från datorer utanför listan över tillåtna IP-adressintervall. Att bläddra bland Azure Cosmos DB resurser från portalen blockeras också för att säkerställa integriteten för åtkomst kontroll.

### <a name="allow-requests-from-the-azure-portal"></a>Tillåt begär Anden från Azure Portal

När du aktiverar en princip för IP-åtkomstkontroll program mässigt måste du lägga till IP-adressen för Azure Portal till egenskapen **ipRangeFilter** för att upprätthålla åtkomsten. Portalens IP-adresser är:

|Region|IP-adress|
|------|----------|
|Tyskland|51.4.229.218|
|Kina|139.217.8.252|
|US Gov|52.244.48.71|
|Alla andra regioner|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Du kan aktivera begär Anden om åtkomst till Azure Portal genom att välja alternativet **Tillåt åtkomst från Azure Portal** , som du ser i följande skärm bild:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-portal.png" alt-text="Skärm bild som visar hur du aktiverar Azure Portal-åtkomst":::

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Tillåt förfrågningar från globala Azure-datacenter eller andra källor i Azure

Om du har åtkomst till ditt Azure Cosmos DB-konto från tjänster som inte har en statisk IP-adress (till exempel Azure Stream Analytics och Azure Functions) kan du fortfarande använda IP-brandväggen för att begränsa åtkomsten. Du kan aktivera åtkomst från andra källor i Azure genom att markera alternativet **Godkänn anslutningar från Azure-datacenter** , som du ser i följande skärm bild:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-services.png" alt-text="Skärm bild som visar hur du accepterar anslutningar från Azure-datacenter":::

När du aktiverar det här alternativet läggs IP-adressen `0.0.0.0` till i listan över tillåtna IP-adresser. `0.0.0.0`IP-adressen begränsar begär anden till ditt Azure Cosmos DB konto från Azure datacenters IP-intervall. Den här inställningen tillåter inte åtkomst för några andra IP-intervall till ditt Azure Cosmos DB-konto.

> [!NOTE]
> Det här alternativet konfigurerar brand väggen så att den tillåter alla förfrågningar från Azure, inklusive förfrågningar från prenumerationer på andra kunder som distribueras i Azure. Listan över IP-adresser som tillåts av det här alternativet är bred, så den begränsar en brand Väggs princips effektivitet. Använd bara det här alternativet om dina begär Anden inte härstammar från statiska IP-adresser eller undernät i virtuella nätverk. Om du väljer det här alternativet tillåts åtkomst från Azure Portal automatiskt eftersom Azure Portal distribueras i Azure.

### <a name="requests-from-your-current-ip"></a>Begär Anden från din aktuella IP

För att förenkla utvecklingen hjälper Azure Portal att identifiera och lägga till IP-adressen för klient datorn i listan över tillåtna. Appar som kör datorn kan sedan komma åt ditt Azure Cosmos DB-konto.

Portalen identifierar automatiskt klientens IP-adress. Det kan vara klientens IP-adress eller IP-adressen för din nätverks-Gateway. Se till att ta bort den här IP-adressen innan du tar dina arbets belastningar till produktionen.

Om du vill lägga till din aktuella IP-adress i listan över IP-adresser väljer du **Lägg till min nuvarande IP**. Välj sedan **Spara**.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="Skärm bild som visar hur du konfigurerar brand Väggs inställningar för den aktuella IP-adressen":::

### <a name="requests-from-cloud-services"></a>Förfrågningar från moln tjänster

I Azure är moln tjänster ett vanligt sätt för att vara värd för mellanskikts tjänst logik med hjälp av Azure Cosmos DB. Om du vill ge åtkomst till ditt Azure Cosmos DB-konto från en moln tjänst måste du lägga till den offentliga IP-adressen för moln tjänsten i listan över tillåtna IP-adresser som är kopplade till ditt Azure Cosmos DB konto genom att [Konfigurera principen för IP-åtkomstkontroll](#configure-ip-policy). Detta säkerställer att alla roll instanser av moln tjänster har åtkomst till ditt Azure Cosmos DB-konto.

Du kan hämta IP-adresser för dina moln tjänster i Azure Portal, som visas på följande skärm bild:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Skärm bild som visar den offentliga IP-adressen för en moln tjänst som visas i Azure Portal":::

När du skalar ut din moln tjänst genom att lägga till roll instanser, kommer dessa nya instanser automatiskt att ha åtkomst till det Azure Cosmos DB kontot eftersom de är en del av samma moln tjänst.

### <a name="requests-from-virtual-machines"></a>Begär Anden från virtuella datorer

Du kan också använda [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/) eller [skalnings uppsättningar för virtuella](../virtual-machine-scale-sets/overview.md) datorer för att hantera tjänster på mellan nivå med hjälp av Azure Cosmos dB. Om du vill konfigurera ditt Cosmos DB-konto så att det tillåter åtkomst från virtuella datorer, måste du konfigurera den offentliga IP-adressen för den virtuella datorn och/eller skalnings uppsättningen för den virtuella datorn som en av de tillåtna IP-adresserna för ditt Azure Cosmos DB-konto genom att [Konfigurera principen för IP-åtkomstkontroll](#configure-ip-policy).

Du kan hämta IP-adresser för virtuella datorer i Azure Portal, som visas på följande skärm bild:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Skärm bild som visar en offentlig IP-adress för en virtuell dator som visas i Azure Portal":::

När du lägger till instanser av virtuella datorer i gruppen får de automatiskt åtkomst till ditt Azure Cosmos DB-konto.

### <a name="requests-from-the-internet"></a>Begär Anden från Internet

När du ansluter till ditt Azure Cosmos DB-konto från en dator på Internet måste klientens IP-adress eller IP-adressintervall läggas till i listan över tillåtna IP-adresser för ditt konto.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Konfigurera en IP-brandvägg med en Resource Manager-mall

Om du vill konfigurera åtkomst kontroll till ditt Azure Cosmos DB konto kontrollerar du att Resource Manager-mallen anger egenskapen **ipRules** med en matris med tillåtna IP-intervall. Om du konfigurerar IP-brandvägg till ett redan distribuerat Cosmos-konto ser du till att `locations` matrisen matchar vad som för närvarande har distribuerats. Du kan inte ändra `locations` matrisen och andra egenskaper samtidigt. Mer information och exempel på Azure Resource Manager mallar för Azure Cosmos DB finns i [Azure Resource Manager mallar för Azure Cosmos DB](./templates-samples-sql.md)

> [!IMPORTANT]
> **IpRules** -egenskapen har introducerats med API-version 2020-04-01. Tidigare versioner exponerade en **ipRangeFilter** -egenskap i stället, som är en lista över kommaavgränsade IP-adresser.

Exemplet nedan visar hur **ipRules** -egenskapen exponeras i API version 2020-04-01 eller senare:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

Här är samma exempel för alla API-versioner före 2020-04-01:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Konfigurera en princip för åtkomst kontroll för IP-åtkomst med hjälp av Azure CLI

Följande kommando visar hur du skapar ett Azure Cosmos DB-konto med IP-åtkomst kontroll:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Konfigurera en princip för åtkomst kontroll för IP-åtkomst med hjälp av PowerShell

Följande skript visar hur du skapar ett Azure Cosmos DB-konto med IP-åtkomst kontroll:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Felsöka problem med en princip för åtkomst kontroll för IP

Du kan felsöka problem med en kontroll princip för IP-åtkomst med hjälp av följande alternativ:

### <a name="azure-portal"></a>Azure Portal

Genom att aktivera en princip för IP-åtkomstkontroll för ditt Azure Cosmos DB-konto blockerar du alla förfrågningar till ditt konto från datorer utanför listan över tillåtna IP-adressintervall. Om du vill aktivera data Plans åtgärder på portalen som att söka efter behållare och fråga dokument måste du uttryckligen tillåta Azure Portal åtkomst med hjälp av **brand Väggs** fönstret i portalen.

### <a name="sdks"></a>SDK:er

När du kommer åt Azure Cosmos DB-resurser med hjälp av SDK: er från datorer som inte finns i listan över tillåtna, returneras ett allmänt **403 förbjudet** svar utan ytterligare information. Kontrol lera att det finns en lista över tillåtna IP-adresser för ditt konto och se till att rätt princip konfiguration tillämpas på ditt Azure Cosmos DB-konto.

### <a name="source-ips-in-blocked-requests"></a>Käll-IP: er i blockerade begär Anden

Aktivera diagnostisk loggning på ditt Azure Cosmos DB-konto. Loggarna visar varje begäran och svar. De brand Väggs meddelanden som är relaterade loggas med en 403-returkod. Genom att filtrera dessa meddelanden kan du se käll-IP: er för de blockerade förfrågningarna. Se [Azure Cosmos DB diagnostisk loggning](./monitor-cosmos-db.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Begär Anden från ett undernät med en tjänst slut punkt för Azure Cosmos DB aktive rad

Begär Anden från ett undernät i ett virtuellt nätverk som har en tjänst slut punkt för Azure Cosmos DB aktive rad skickar det virtuella nätverket och under nätets identitet till Azure Cosmos DB-konton. Dessa begär Anden har ingen offentlig IP-adress för källan, så IP-filter avvisar dem. Om du vill tillåta åtkomst från specifika undernät i virtuella nätverk lägger du till en åtkomst kontrol lista enligt beskrivningen i [så här konfigurerar du virtuellt nätverk och fjärråtkomst till ditt Azure Cosmos DB konto](how-to-configure-vnet-service-endpoint.md). Det kan ta upp till 15 minuter innan brand Väggs reglerna tillämpas och brand väggen kan få ett inkonsekvent beteende under denna period.

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>Privata IP-adresser i listan över tillåtna adresser

Det går inte att skapa eller uppdatera ett Azure Cosmos-konto med en lista över tillåtna adresser som innehåller privata IP-adresser. Kontrol lera att ingen privat IP-adress har angetts i listan.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar en tjänst slut punkt för virtuellt nätverk för ditt Azure Cosmos DB-konto finns i följande artiklar:

* [Åtkomst kontroll för virtuellt nätverk och undernät för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md)
* [Konfigurera Virtual Network och Subnet-based Access för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md)