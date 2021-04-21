---
title: Brandväggsåtkomstregler
description: Konfigurera regler för åtkomst till ett Azure-containerregister bakom en brandvägg genom att tillåta åtkomst till REST API- och dataslutpunktsdomännamn eller tjänstspecifika IP-adressintervall.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 6aea4415468eb21e8d010b74597fc68e4ebf573f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783943"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurera regler för åtkomst till ett Azure-containerregister bakom en brandvägg

Den här artikeln beskriver hur du konfigurerar regler i brandväggen för att tillåta åtkomst till ett Azure-containerregister. Till exempel kan en Azure IoT Edge enhet bakom en brandvägg eller proxyserver behöva komma åt ett containerregister för att hämta en containeravbildning. Eller så kan en låst server i ett lokalt nätverk behöva åtkomst för att push-skicka en avbildning.

Om du i stället vill konfigurera inkommande nätverksåtkomst till ett containerregister endast inom ett virtuellt Azure-nätverk, se Konfigurera [Azure Private Link för ett Azure-containerregister.](container-registry-private-link.md)

## <a name="about-registry-endpoints"></a>Om registerslutpunkter

För att hämta eller push-pusha avbildningar eller andra artefakter till ett Azure-containerregister måste en klient, till exempel en Docker-daemon, interagera över HTTPS med två olika slutpunkter. För klienter som har åtkomst till ett register bakom en brandvägg måste du konfigurera åtkomstregler för båda slutpunkterna. Båda slutpunkterna nås via port 443.

* **Register REST API slutpunkt** – Autentiserings- och registerhanteringsåtgärder hanteras via registrets offentliga REST API slutpunkt. Den här slutpunkten är registrets inloggningsservernamn. Exempel: `myregistry.azurecr.io`

* **Lagringsslutpunkt (data)** – Azure allokerar [bloblagring](container-registry-storage.md) i Azure Storage-konton för varje registers räkning för att hantera data för containeravbildningar och andra artefakter. När en klient får åtkomst till avbildningslager i ett Azure-containerregister gör den begäranden med hjälp av en slutpunkt för lagringskontot som tillhandahålls av registret.

Om registret är [geo-replikerat](container-registry-geo-replication.md)kan en klient behöva interagera med dataslutpunkten i en viss region eller i flera replikerade regioner.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Tillåt åtkomst till REST och dataslutpunkter

* **REST-slutpunkt** – Tillåt åtkomst till det fullständigt kvalificerade inloggningsservernamnet för `<registry-name>.azurecr.io` registret, eller ett associerat IP-adressintervall
* **Lagringsslutpunkt (data)** – Tillåt åtkomst till alla Azure Blob Storage-konton med jokertecknet `*.blob.core.windows.net` eller ett associerat IP-adressintervall.
> [!NOTE]
> Azure Container Registry introducerar [dedikerade dataslutpunkter,](#enable-dedicated-data-endpoints)vilket gör att du kan begränsa klientens brandväggsregler för din registerlagring. Du kan också aktivera dataslutpunkter i alla regioner där registret finns eller replikeras, med hjälp av formuläret `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Tillåt åtkomst efter IP-adressintervall

Om din organisation har principer för att endast tillåta åtkomst till specifika IP-adresser eller adressintervall laddar du ned [AZURE IP-intervall och tjänsttaggar – offentligt moln.](https://www.microsoft.com/download/details.aspx?id=56519)

Om du vill hitta ACR REST-slutpunktens IP-intervall som du behöver tillåta åtkomst för söker du efter **AzureContainerRegistry** i JSON-filen.

> [!IMPORTANT]
> IP-adressintervall för Azure-tjänster kan ändras och uppdateringar publiceras varje vecka. Ladda ned JSON-filen regelbundet och gör nödvändiga uppdateringar i dina åtkomstregler. Om ditt scenario omfattar konfiguration av regler för nätverkssäkerhetsgrupp i ett virtuellt Azure-nätverk eller om du använder Azure Firewall använder du **tjänsttaggen AzureContainerRegistry** [i](#allow-access-by-service-tag) stället.
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-adresser för alla regioner

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-adresser för en viss region

Sök efter den specifika regionen, till exempel **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Ip-lagringsadresser för alla regioner

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>IP-lagringsadresser för specifika regioner

Sök efter den specifika regionen, till exempel **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Tillåt åtkomst efter tjänsttagg

I ett virtuellt Azure-nätverk använder du nätverkssäkerhetsregler för att filtrera trafik från en resurs, till exempel en virtuell dator, till ett containerregister. Använd tjänsttaggen **AzureContainerRegistry** för att förenkla skapandet av [Azure-nätverksregler.](../virtual-network/network-security-groups-overview.md#service-tags) En tjänsttagg representerar en grupp med IP-adressprefix för åtkomst till en Azure-tjänst globalt eller per Azure-region. Taggen uppdateras automatiskt när adresserna ändras. 

Skapa till exempel en regel för utgående nätverkssäkerhetsgrupp med **Målet AzureContainerRegistry** för att tillåta trafik till ett Azure-containerregister. Om du bara vill tillåta åtkomst till tjänsttaggen i en viss region anger du regionen i följande format: **AzureContainerRegistry**. [*regionnamn*].

## <a name="enable-dedicated-data-endpoints"></a>Aktivera dedikerade dataslutpunkter 

> [!WARNING]
> Om du tidigare har konfigurerat klientbrandväggens åtkomst till de befintliga slutpunkterna påverkar bytet till dedikerade `*.blob.core.windows.net` dataslutpunkter klientanslutningen, vilket orsakar pull-fel. För att säkerställa att klienterna har konsekvent åtkomst lägger du till de nya dataslutpunktsreglerna i klientens brandväggsregler. När det är klart aktiverar du dedikerade dataslutpunkter för dina register med hjälp av Azure CLI eller andra verktyg.

Dedikerade dataslutpunkter är en valfri funktion på **tjänstnivån** premiumcontainerregister. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry tjänstnivåer](container-registry-skus.md). 

Du kan aktivera dedikerade dataslutpunkter med hjälp Azure Portal eller Azure CLI. Dataslutpunkterna följer ett regionalt mönster, `<registry-name>.<region>.data.azurecr.io` . I ett geo-replikerat register möjliggör aktivering av dataslutpunkter slutpunkter i alla replikregioner.

### <a name="portal"></a>Portalen

Så här aktiverar du dataslutpunkter med hjälp av portalen:

1. Gå till containerregistret.
1. Välj **Offentlig åtkomst** för  >  **nätverk.**
1. Markera kryssrutan **Aktivera dedikerad dataslutpunkt.**
1. Välj **Spara**.

Dataslutpunkter eller slutpunkter visas i portalen.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Dedikerade dataslutpunkter i portalen":::

### <a name="azure-cli"></a>Azure CLI

Om du vill aktivera dataslutpunkter med hjälp av Azure CLI använder du Azure CLI version 2.4.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Följande [az acr update-kommando][az-acr-update] aktiverar dedikerade dataslutpunkter på ett register *myregistry*. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Om du vill visa dataslutpunkterna använder du [kommandot az acr show-endpoints:][az-acr-show-endpoints]

```azurecli
az acr show-endpoints --name myregistry
```

Utdata i demonstrationssyfte visar två regionala slutpunkter

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

När du har ställt in dedikerade dataslutpunkter för registret kan du aktivera klientbrandväggens åtkomstregler för dataslutpunkterna. Aktivera åtkomstregler för dataslutpunkter för alla nödvändiga registerregioner.

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurera klientbrandväggsregler för MCR

Om du behöver åtkomst till Microsoft Container Registry (MCR) bakom en brandvägg kan du gå till vägledningen för att konfigurera [MCR-klientbrandväggsregler.](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) MCR är det primära registret för alla Microsoft-publicerade Docker-avbildningar, till exempel Windows Server-avbildningar.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer [om metodtips för nätverkssäkerhet i Azure](../security/fundamentals/network-best-practices.md)

* Läs mer om [säkerhetsgrupper i](../virtual-network/network-security-groups-overview.md) ett virtuellt Azure-nätverk

* Läs mer om att konfigurera [Private Link](container-registry-private-link.md) för ett containerregister

* Läs mer om [dedikerade dataslutpunkter](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) för Azure Container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-show-endpoints]: /cli/azure/acr#az_acr_show_endpoints