---
title: Åtkomst regler för brand vägg
description: Konfigurera regler för åtkomst till ett Azure Container Registry från bakom en brand vägg genom att tillåta åtkomst till REST API-och data slut punkts domän namn eller tjänstspecifika IP-adressintervall.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 548d64632c1d726111770dfb49f705d31f5ca714
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935996"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg

Den här artikeln förklarar hur du konfigurerar regler i brand väggen för att tillåta åtkomst till ett Azure Container Registry. Till exempel kan en Azure IoT Edge enhet bakom en brand vägg eller proxyserver behöva komma åt ett behållar register för att hämta en behållar avbildning. Eller så kan en låst server i ett lokalt nätverk behöva åtkomst för att skicka en avbildning.

Om du i stället vill konfigurera inkommande nätverks åtkomst till ett behållar register i ett virtuellt Azure-nätverk kan du läsa [Konfigurera Azure Private Link för ett Azure Container Registry](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Om register slut punkter

För att hämta eller skicka avbildningar eller andra artefakter till ett Azure Container Registry måste en klient, till exempel en Docker-daemon, interagera över HTTPS med två distinkta slut punkter. För klienter som har åtkomst till ett register från bakom en brand vägg måste du konfigurera åtkomst regler för båda slut punkterna. Båda slut punkterna har nåtts via port 443.

* **Registrets REST API slut punkt** – autentiserings-och register hanterings åtgärder hanteras via registrets offentliga REST API-slutpunkt. Den här slut punkten är inloggnings Server namnet för registret. Exempel: `myregistry.azurecr.io`

* **Lagring (data) slut punkt** – Azure [allokerar blob Storage](container-registry-storage.md) i Azure Storage konton för varje registers räkning för att hantera data för behållar avbildningar och andra artefakter. När en klient ansluter till avbildnings lager i ett Azure Container Registry, begär det begär Anden med hjälp av en slut punkt för lagrings kontot som tillhandahålls av registret.

Om ditt register är [geo-replikerat](container-registry-geo-replication.md)kan en klient behöva interagera med data slut punkten i en angiven region eller i flera replikerade regioner.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Tillåt åtkomst till REST-och data slut punkter

* **REST-slutpunkt** – Tillåt åtkomst till det fullständigt kvalificerade registrets inloggnings Server namn, `<registry-name>.azurecr.io` eller ett associerat IP-adressintervall
* **Lagring (data) slut punkt** – Tillåt åtkomst till alla Azure Blob Storage-konton med hjälp av jokertecken `*.blob.core.windows.net` , eller ett associerat IP-adressintervall.
> [!NOTE]
> Azure Container Registry introducerar [dedikerade data slut punkter](#enable-dedicated-data-endpoints)så att du kan begränsa klient brand Väggs reglerna för ditt register lagring. Du kan också aktivera data slut punkter i alla regioner där registret finns eller replikeras med hjälp av formuläret `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Tillåt åtkomst med IP-adressintervall

Om din organisation har principer som endast tillåter åtkomst till vissa IP-adresser eller adress intervall, laddar du ned [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519).

Du hittar IP-intervall för ACR REST-slutpunkt som du måste tillåta åtkomst genom att söka efter **AzureContainerRegistry** i JSON-filen.

> [!IMPORTANT]
> IP-adressintervall för Azure-tjänster kan ändras och uppdateringar publiceras varje vecka. Ladda ned JSON-filen regelbundet och gör nödvändiga uppdateringar i dina åtkomst regler. Om ditt scenario innefattar konfigurering av regler för nätverks säkerhets grupper i ett virtuellt Azure-nätverk eller om du använder Azure-brandväggen, använder du **AzureContainerRegistry** [service tag](#allow-access-by-service-tag) i stället.
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

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-adresser för en angiven region

Sök efter den angivna regionen, till exempel **AzureContainerRegistry. AustraliaEast**.

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

### <a name="storage-ip-addresses-for-all-regions"></a>Lagrings-IP-adresser för alla regioner

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

### <a name="storage-ip-addresses-for-specific-regions"></a>Lagrings-IP-adresser för vissa regioner

Sök efter den angivna regionen, till exempel **Storage. AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Tillåt åtkomst per service tag-tagg

I ett virtuellt Azure-nätverk använder du nätverks säkerhets regler för att filtrera trafik från en resurs, till exempel en virtuell dator i ett behållar register. För att förenkla skapandet av reglerna för Azure-nätverk använder du **AzureContainerRegistry** [service tag](../virtual-network/network-security-groups-overview.md#service-tags). En service-tagg representerar en grupp IP-adressprefix för att få åtkomst till en Azure-tjänst globalt eller per Azure-region. Taggen uppdateras automatiskt när adresserna ändras. 

Du kan till exempel skapa en regel för utgående nätverks säkerhets grupp med mål- **AzureContainerRegistry** för att tillåta trafik till ett Azure Container Registry. Om du bara vill tillåta åtkomst till tjänst tag gen i en angiven region anger du regionen i följande format: **AzureContainerRegistry**. [*regions namn*].

## <a name="enable-dedicated-data-endpoints"></a>Aktivera dedikerade data slut punkter 

> [!WARNING]
> Om du tidigare har konfigurerat klient brand Väggs åtkomst till befintliga `*.blob.core.windows.net` slut punkter påverkar det att växlingen av dedikerade data slut punkter påverkar klient anslutningen, vilket orsakar pull-problem. För att säkerställa att klienterna har konsekvent åtkomst lägger du till de nya data slut reglerna i brand Väggs reglerna för klienter. När du är klar kan du aktivera dedikerade data slut punkter för dina register med hjälp av Azure CLI eller andra verktyg.

Dedikerade data slut punkter är en valfri funktion i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md). 

Du kan aktivera dedikerade data slut punkter med hjälp av Azure Portal eller Azure CLI. Data slut punkterna följer ett regionalt mönster, `<registry-name>.<region>.data.azurecr.io` . I ett geo-replikerat register aktiverar data slut punkter i alla replik regioner.

### <a name="portal"></a>Portal

Så här aktiverar du data slut punkter med hjälp av portalen:

1. Navigera till behållar registret.
1. Välj   >  **offentlig nätverks åtkomst**.
1. Markera kryss rutan **Aktivera dedikerad data slut punkt** .
1. Välj **Spara**.

Data slut punkten eller slut punkterna visas i portalen.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Dedikerade data slut punkter i portalen":::

### <a name="azure-cli"></a>Azure CLI

Om du vill aktivera data slut punkter med Azure CLI använder du Azure CLI version 2.4.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Följande [AZ ACR Update][az-acr-update] -kommando aktiverar dedikerade data slut punkter *i registrets register.* 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Om du vill visa data slut punkterna använder du kommandot [AZ ACR show-endpoints][az-acr-show-endpoints] :

```azurecli
az acr show-endpoints --name myregistry
```

Utdata i demonstrations syfte visar två regionala slut punkter

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

När du har konfigurerat dedikerade data slut punkter för registret kan du aktivera åtkomst regler för klient brand väggen för data slut punkterna. Aktivera åtkomst regler för data slut punkt för alla nödvändiga register områden.

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurera brand Väggs regler för MCR

Om du behöver åtkomst till Microsoft Container Registry (MCR) bakom en brand vägg kan du läsa mer i rikt linjerna för att konfigurera [brand Väggs regler för MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR är det primära registret för alla Microsoft-publicerade Docker-avbildningar, till exempel Windows Server-avbildningar.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azures metod tips för nätverks säkerhet](../security/fundamentals/network-best-practices.md)

* Lär dig mer om [säkerhets grupper](../virtual-network/network-security-groups-overview.md) i ett virtuellt Azure-nätverk

* Läs mer om hur du konfigurerar en [privat länk](container-registry-private-link.md) för ett behållar register

* Läs mer om [dedikerade data slut punkter](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) för Azure Container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints