---
title: Skydda webb tjänster med TLS
titleSuffix: Azure Machine Learning
description: Lär dig hur du aktiverar HTTPS med TLS version 1,2 för att skydda en webb tjänst som distribueras via Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.date: 03/11/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 71cb2e9e112c49d77a2a0b47c24c49cabfa86589
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149026"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Använda TLS för att skydda en webbtjänst via Azure Machine Learning


Den här artikeln visar hur du skyddar en webb tjänst som distribueras via Azure Machine Learning.

Du använder [https](https://en.wikipedia.org/wiki/HTTPS) för att begränsa åtkomsten till webb tjänster och säkra de data som klienter skickar. HTTPS skyddar kommunikationen mellan en klient och en webb tjänst genom att kryptera kommunikationen mellan de två. Kryptering använder [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS kallas ibland även *Secure Sockets Layer* (SSL), som var den föregående aktiviteten TLS.

> [!TIP]
> Azure Machine Learning SDK använder termen "SSL" för egenskaper som är relaterade till säker kommunikation. Detta innebär inte att din webb tjänst inte använder *TLS*. SSL är bara en oftare erkänd term.
>
> Mer specifikt distribueras webb tjänster via Azure Machine Learning stöd för TLS version 1,2 för AKS och ACI. Om du använder en äldre TLS-version, rekommenderar vi att du distribuerar om för att få den senaste TLS-versionen för ACI-distributioner.
>
> TLS-version 1,3 för Azure Machine Learning-AKS-härledning stöds inte.

TLS och SSL är beroende av *digitala certifikat*, som hjälper till med kryptering och identitets verifiering. Mer information om hur digitala certifikat fungerar finns i avsnittet om infrastrukturen för [offentliga nycklar](https://en.wikipedia.org/wiki/Public_key_infrastructure)i Wikipedia-ämnet.

> [!WARNING]
> Om du inte använder HTTPS för din webb tjänst kan data som skickas till och från tjänsten vara synliga för andra på Internet.
>
> HTTPS gör det också möjligt för klienten att verifiera äktheten på den server som den ansluter till. Den här funktionen skyddar klienter mot [man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) -attacker.

Detta är den allmänna processen för att skydda en webb tjänst:

1. Hämta ett domän namn.

2. Skaffa ett digitalt certifikat.

3. Distribuera eller uppdatera webb tjänsten med TLS aktiverat.

4. Uppdatera din DNS så att den pekar på webb tjänsten.

> [!IMPORTANT]
> Om du distribuerar till Azure Kubernetes service (AKS) kan du köpa ditt eget certifikat eller använda ett certifikat från Microsoft. Om du använder ett certifikat från Microsoft behöver du inte skaffa ett domän namn eller TLS/SSL-certifikat. Mer information finns i avsnittet [Aktivera TLS och Deploy](#enable) i den här artikeln.

Det finns små skillnader när du skyddar er över [distributions mål](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Skaffa ett domännamn

Om du inte redan har ett domän namn kan du köpa ett från en *domän namns registrator*. Processen och priset skiljer sig mellan registratorn. Registratorn innehåller verktyg för att hantera domän namnet. Du använder dessa verktyg för att mappa ett fullständigt kvalificerat domän namn (FQDN) (till exempel www- \. contoso.com) till den IP-adress som är värd för webb tjänsten.

## <a name="get-a-tlsssl-certificate"></a>Hämta ett TLS/SSL-certifikat

Det finns många sätt att hämta ett TLS/SSL-certifikat (digitalt certifikat). Det vanligaste är att köpa en från en *certifikat utfärdare* (ca). Oavsett var du får certifikatet behöver du följande filer:

* Ett **certifikat**. Certifikatet måste innehålla den fullständiga certifikat kedjan och måste vara "PEM-kodad".
* En **nyckel**. Nyckeln måste också vara PEM-kodad.

När du begär ett certifikat måste du ange det fullständiga domän namnet för den adress som du planerar att använda för webb tjänsten (till exempel www- \. contoso.com). Adressen som stämplas in i certifikatet och den adress som klienterna använder jämförs för att verifiera webb tjänstens identitet. Om dessa adresser inte matchar får klienten ett fel meddelande.

> [!TIP]
> Om certifikat utfärdaren inte kan ange certifikatet och nyckeln som PEM-kodade filer kan du använda ett verktyg som [openssl](https://www.openssl.org/) för att ändra formatet.

> [!WARNING]
> Använd endast *självsignerade* certifikat för utveckling. Använd dem inte i produktions miljöer. Självsignerade certifikat kan orsaka problem i dina klient program. Mer information finns i dokumentationen för de nätverks bibliotek som används av klient programmet.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Aktivera TLS och distribuera

**För AKS-distribution** kan du Aktivera TLS-avslutning när du [skapar eller ansluter ett AKS-kluster](how-to-create-attach-kubernetes.md) i AML-arbetsytan. Vid distributions tiden för AKS-modellen kan du inaktivera TLS-terminering med distributions konfigurations objekt, annars kommer alla AKS modell distribution som standard att ha TLS-avslutning aktiverat på AKS-klustret skapa eller koppla tid.

För ACI-distribution kan du Aktivera TLS-avslutning vid modell distributions tid med ett distributions konfigurations objekt.


### <a name="deploy-on-azure-kubernetes-service"></a>Distribuera på Azure Kubernetes service

  > [!NOTE]
  > Informationen i det här avsnittet gäller även när du distribuerar en säker webb tjänst för designern. Om du inte är bekant med att använda python SDK, se [Vad är Azure Machine Learning SDK för python?](/python/api/overview/azure/ml/intro).

När du [skapar eller ansluter ett AKS-kluster](how-to-create-attach-kubernetes.md) i AML-arbetsytan kan du Aktivera TLS-avslutning med **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** och **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** konfigurations objekt. Båda metoderna returnerar ett konfigurations objekt som har en **enable_ssl** -Metod och du kan använda **enable_ssl** -metoden för att aktivera TLS.

Du kan aktivera TLS antingen med Microsoft-certifikat eller ett anpassat certifikat som har köpts från CA. 

* **När du använder ett certifikat från Microsoft** måste du använda *leaf_domain_label* -parametern. Den här parametern genererar DNS-namnet för tjänsten. Till exempel skapar värdet "contoso" domän namnet "contoso \<six-random-characters> . \<azureregion> . cloudapp.azure.com ", där \<azureregion> är den region som innehåller tjänsten. Alternativt kan du använda parametern *overwrite_existing_domain* för att skriva över den befintliga *leaf_domain_label*. Följande exempel visar hur du skapar en konfiguration som aktiverar en TLS med Microsoft-certifikat:

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > När du använder ett certifikat från Microsoft behöver du inte köpa ditt eget certifikat eller domän namn.

    > [!WARNING]
    > Om ditt AKS-kluster har kon figurer ATS med en intern belastningsutjämnare kan du __inte__ använda ett certifikat från Microsoft och du måste använda det anpassade certifikatet för att aktivera TLS.

* **När du använder ett anpassat certifikat som du har köpt** använder du parametrarna *ssl_cert_pem_file*, *ssl_key_pem_file* och *ssl_cname* . Följande exempel visar hur du använder. PEM-filer för att skapa en konfiguration som använder ett TLS/SSL-certifikat som du har köpt:
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Mer information om *enable_ssl* finns i [AksProvisioningConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) och [AksAttachConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Distribuera på Azure Container Instances

När du distribuerar till Azure Container Instances anger du värden för TLS-relaterade parametrar, som följande kodfragment visar:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Mer information finns i [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Uppdatera din DNS

För en AKS-distribution med anpassad certifikat-eller ACI-distribution måste du uppdatera DNS-posten så att den pekar mot IP-adressen för poäng slut punkten.

  > [!IMPORTANT]
  > När du använder ett certifikat från Microsoft för AKS-distribution behöver du inte uppdatera DNS-värdet manuellt för klustret. Värdet ska anges automatiskt.

Du kan följa stegen nedan för att uppdatera DNS-posten för ditt anpassade domän namn:
* Hämta IP-adressen för poäng slut punkten från bedömnings slut punktens URI, som vanligt vis är i formatet *http://104.214.29.152:80/api/v1/service/<service-name>/score* . 
* Använd verktygen från din domän namns registrator för att uppdatera DNS-posten för ditt domän namn. Posten måste peka på IP-adressen för bedömnings slut punkten.
* Efter uppdateringen av DNS-posten kan du verifiera DNS-matchning med kommandot *nslookup Custom-Domain-Name* . Om DNS-posten är korrekt uppdaterad kommer det anpassade domän namnet att peka mot IP-adressen för bedömnings slut punkten.
* Det kan vara en fördröjning på minuter eller timmar innan klienter kan matcha domän namnet, beroende på registratorn och TTL-värdet (Time to Live) som har kon figurer ATS för domän namnet.


## <a name="update-the-tlsssl-certificate"></a>Uppdatera TLS/SSL-certifikatet

TLS/SSL-certifikat upphör att gälla och måste förnyas. Detta sker vanligt vis varje år. Använd informationen i följande avsnitt för att uppdatera och förnya ditt certifikat för modeller som distribueras till Azure Kubernetes-tjänsten:

### <a name="update-a-microsoft-generated-certificate"></a>Uppdatera ett Microsoft-genererat certifikat

Om certifikatet ursprungligen genererades av Microsoft (när du använder *leaf_domain_label* för att skapa tjänsten) **förnyas det automatiskt när det** behövs. Om du vill förnya den manuellt kan du använda något av följande exempel för att uppdatera certifikatet:

> [!IMPORTANT]
> * Om det befintliga certifikatet fortfarande är giltigt använder du `renew=True` (SDK) eller `--ssl-renew` (CLI) för att tvinga konfigurationen att förnya det. Om det befintliga certifikatet fortfarande är giltigt i 10 dagar och du inte använder `renew=True` , kan det hända att certifikatet inte förnyas.
> * När tjänsten ursprungligen distribuerades `leaf_domain_label` används den för att skapa ett DNS-namn med hjälp av mönstret `<leaf-domain-label>######.<azure-region>.cloudapp.azure.com` . Använd det ursprungliga värdet för att bevara det befintliga namnet (inklusive de 6 siffror som ursprungligen genererades) `leaf_domain_label` . Ta inte med de 6 siffror som genererades.

**Använd SDK: n**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Använda CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Mer information finns i följande referens dokument:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

### <a name="update-custom-certificate"></a>Uppdatera anpassat certifikat

Om certifikatet ursprungligen genererades av en certifikat utfärdare kan du använda följande steg:

1. Använd dokumentationen från certifikat utfärdaren för att förnya certifikatet. Den här processen skapar nya certifikatfiler.

1. Använd antingen SDK eller CLI för att uppdatera tjänsten med det nya certifikatet:

    **Använd SDK: n**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Använda CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Mer information finns i följande referens dokument:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

## <a name="disable-tls"></a>Inaktivera TLS

Om du vill inaktivera TLS för en modell som distribueras till Azure Kubernetes-tjänsten skapar du en `SslConfiguration` med `status="Disabled"` och utför sedan en uppdatering:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Nästa steg
Lär dig att:
+ [Använda en maskin inlärnings modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
+ [Översikt över virtuella nätverks isolering och sekretess](how-to-network-security-overview.md)