---
title: Data kryptering med Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning-beräkningar och data lager tillhandahåller data kryptering i vila och under överföring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 13d5c02fbb4ae06c7a5279ab7c5d3af90c263f71
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521075"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Data kryptering med Azure Machine Learning

Azure Machine Learning använder en mängd olika Azure-tjänster för data lagring och beräknings resurser när de tränar modeller och utför en bedömning. Vart och ett av dessa har sin egen berättelse om hur de tillhandahåller kryptering för data i vila och under överföring. I den här artikeln får du veta mer om var och en som passar bäst för ditt scenario.

> [!IMPORTANT]
> För produktions klass kryptering under __utbildningen__ rekommenderar Microsoft att du använder Azure Machine Learning beräknings kluster. Microsoft rekommenderar att du använder Azure Kubernetes-tjänsten för kryptering av produktions klass under __härledningen__.
>
> Azure Machine Learning beräknings instans är en utvecklings-/test miljö. När du använder den rekommenderar vi att du lagrar dina filer, till exempel antecknings böcker och skript, i en fil resurs. Dina data ska lagras i ett data lager.

## <a name="encryption-at-rest"></a>Kryptering i vila

> [!IMPORTANT]
> Om din arbets yta innehåller känsliga data rekommenderar vi att du ställer in [hbi_workspace flagga](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) när du skapar din arbets yta. Det `hbi_workspace` går bara att ange flaggan när en arbets yta skapas. Den kan inte ändras för en befintlig arbets yta.

`hbi_workspace`Flaggan styr mängden [data som Microsoft samlar in i diagnostiska syfte](#microsoft-collected-data) och möjliggör [ytterligare kryptering i Microsoft-hanterade miljöer](../security/fundamentals/encryption-atrest.md). Dessutom kan du använda följande åtgärder:

* Startar kryptering av den lokala grunden-disken i Azure Machine Learning beräknings kluster förutsatt att du inte har skapat några tidigare kluster i den prenumerationen. Annars måste du skapa ett support ärende för att aktivera kryptering av den virtuella datorns arbets kluster 
* Rensar den lokala disken för tillfällig lagring mellan körningar
* Skickar autentiseringsuppgifter på ett säkert sätt för ditt lagrings konto, behållar registret och SSH-kontot från körnings skiktet till dina beräknings kluster med hjälp av nyckel valvet
* Aktiverar IP-filtrering för att säkerställa att underliggande batch-pooler inte kan anropas av andra externa tjänster än AzureMachineLearningService
* Observera att beräknings instanser inte stöds i HBI-arbetsytan

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning lagrar ögonblicks bilder, utdata och loggar i Azure Blob Storage-kontot som är knutet till Azure Machine Learning arbets ytan och din prenumeration. Alla data som lagras i Azure Blob Storage krypteras i vila med Microsoft-hanterade nycklar.

Information om hur du använder dina egna nycklar för data som lagras i Azure Blob Storage finns i [Azure Storage kryptering med Kundhanterade nycklar i Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md).

Tränings data lagras vanligt vis i Azure Blob Storage så att det är tillgängligt för att träna beräknings mål. Den här lagringen hanteras inte av Azure Machine Learning men monteras för att beräkna mål som ett fjärrfilsystem.

Om du behöver __Rotera eller återkalla__ din nyckel kan du när som helst göra det. När du roterar en nyckel börjar lagrings kontot använda den nya nyckeln (den senaste versionen) för att kryptera data i vila. När du återkallar (inaktiverar) en nyckel tar lagrings kontot hand om misslyckade begär Anden. Det tar vanligt vis en timme för rotationen eller återkallning att vara effektiv.

Information om hur du återskapar åtkomst nycklarna finns i [Återskapa lagrings åtkomst nycklar](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning lagrar metadata i en Azure Cosmos DB-instans. Den här instansen är associerad med en Microsoft-prenumeration som hanteras av Azure Machine Learning. Alla data som lagras i Azure Cosmos DB krypteras i vila med Microsoft-hanterade nycklar.

Om du vill använda egna (Kundhanterade) nycklar för att kryptera Azure Cosmos DB-instansen kan du skapa en dedikerad Cosmos DB-instans för användning med din arbets yta. Vi rekommenderar den här metoden om du vill lagra dina data, t. ex. information om körnings historik, utanför Cosmos DB-instansen för flera innehavare som finns i vår Microsoft-prenumeration. 

Om du vill aktivera etablering av en Cosmos DB instans i din prenumeration med Kundhanterade nycklar utför du följande åtgärder:

* Registrera Microsoft. MachineLearning-och Microsoft.DocumentDB-resurs leverantörerna i din prenumeration, om de inte redan har gjort det.

* Använd följande parametrar när du skapar arbets ytan Azure Machine Learning. Båda parametrarna är obligatoriska och stöds i SDK, CLI, REST API: er och Resource Manager-mallar.

    * `resource_cmk_uri`: Den här parametern är den fullständiga resurs-URI: n för kundens hanterade nyckel i ditt nyckel valv, inklusive [versions informationen för nyckeln](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Den här parametern är resurs-ID för nyckel valvet i din prenumeration. Det här nyckel valvet måste finnas i samma region och prenumeration som du ska använda för Azure Machine Learning-arbetsytan. 
    
        > [!NOTE]
        > Den här Key Vault-instansen kan vara annorlunda än nyckel valvet som skapas av Azure Machine Learning när du etablerar arbets ytan. Om du vill använda samma Key Vault-instans för arbets ytan skickar du samma nyckel valv medan du konfigurerar arbets ytan med hjälp av [parametern key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Om du behöver __Rotera eller återkalla__ din nyckel kan du när som helst göra det. När du roterar en nyckel börjar Cosmos DB att använda den nya nyckeln (senaste versionen) för att kryptera data i vila. När du återkallar (inaktiverar) en nyckel tar Cosmos DB hand om misslyckade begär Anden. Det tar vanligt vis en timme för rotationen eller återkallning att vara effektiv.

Mer information om kund hanterade nycklar med Cosmos DB finns i [Konfigurera Kundhanterade nycklar för ditt Azure Cosmos DB-konto](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Azure Container Registry

Alla behållar avbildningar i registret (Azure Container Registry) är krypterade i vila. Azure krypterar automatiskt en avbildning innan den lagras och dekrypterar den när Azure Machine Learning hämtar avbildningen.

Om du vill använda dina egna (Kundhanterade) nycklar för att kryptera din Azure Container Registry måste du skapa en egen ACR och koppla den medan du konfigurerar arbets ytan eller kryptera standard instansen som skapas vid tidpunkten för etablering av arbets ytor.

> [!IMPORTANT]
> Azure Machine Learning kräver att administratörs kontot är aktiverat på din Azure Container Registry. Som standard är den här inställningen inaktive rad när du skapar ett behållar register. Information om hur du aktiverar administratörs kontot finns i [administratörs konto](../container-registry/container-registry-authentication.md#admin-account).
>
> När en Azure Container Registry har skapats för en arbets yta ska du inte ta bort den. Om du gör det bryts Azure Machine Learning arbets ytan.

Ett exempel på hur du skapar en arbets yta med en befintlig Azure Container Registry finns i följande artiklar:

* [Skapa en arbets yta för Azure Machine Learning med Azure CLI](how-to-manage-workspace-cli.md).
* [Skapa en arbets yta med python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Använd en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container-instans

Du kan kryptera en distribuerad Azure Container instance-resurs (ACI) med Kundhanterade nycklar. Den Kundhanterade nyckeln som används för ACI kan lagras i Azure Key Vault för din arbets yta. Information om hur du skapar en nyckel finns i [kryptera data med en kundhanterad nyckel](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Om du vill använda nyckeln när du distribuerar en modell till Azure Container instance skapar du en ny distributions konfiguration med hjälp av `AciWebservice.deploy_configuration()` . Ange viktig information med hjälp av följande parametrar:

* `cmk_vault_base_url`: URL: en för nyckel valvet som innehåller nyckeln.
* `cmk_key_name`: Namnet på nyckeln.
* `cmk_key_version`: Nyckelns version.

Mer information om hur du skapar och använder en distributions konfiguration finns i följande artiklar:

* [AciWebservice.deploy_configuration ()-](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) referens
* [Var och hur du distribuerar](how-to-deploy-and-where.md)
* [Distribuera en modell till Azure Container Instances](how-to-deploy-azure-container-instance.md)

Mer information om hur du använder en kundhanterad nyckel med ACI finns i [kryptera data med en kundhanterad nyckel](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Du kan kryptera en distribuerad Azure Kubernetes service-resurs med hjälp av Kundhanterade nycklar när som helst. Mer information finns i [ta med dina egna nycklar med Azure Kubernetes-tjänsten](../aks/azure-disk-customer-managed-keys.md). 

Med den här processen kan du kryptera både data och OS-disken för de distribuerade virtuella datorerna i Kubernetes-klustret.

> [!IMPORTANT]
> Den här processen fungerar bara med AKS K8s version 1,17 eller senare. Azure Machine Learning lade till stöd för AKS 1,17 den 13 januari 2020.

### <a name="machine-learning-compute"></a>Machine Learning-beräkning

OS-disken för varje Compute-nod som lagras i Azure Storage krypteras med Microsoft-hanterade nycklar i Azure Machine Learning lagrings konton. Detta beräknings mål är tillfälligt och kluster skalas vanligt vis ned när inga körningar placeras i kö. Den underliggande virtuella datorn är avetablerad och OS-disken tas bort. Azure Disk Encryption stöds inte för OS-disken.

Varje virtuell dator har också en lokal temporär disk för OS-åtgärder. Om du vill kan du använda disken för att mellanlagra tränings data. Disken är krypterad som standard för arbets ytor där `hbi_workspace` parametern har angetts till `TRUE` . Den här miljön är endast kort livs längd under körningen och krypterings stödet är begränsat till endast systemhanterade nycklar.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks kan användas i Azure Machine Learning pipeliner. Som standard krypteras DBFS (Databricks File System) som används av Azure Databricks med hjälp av en Microsoft-hanterad nyckel. Information om hur du konfigurerar Azure Databricks att använda Kundhanterade nycklar finns i [Konfigurera Kundhanterade nycklar på standard-DBFS (root)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="microsoft-generated-data"></a>Microsoft-genererade data

När du använder tjänster som automatiserade Machine Learning kan Microsoft generera en tillfällig, förbehandlad data för att träna flera modeller. Dessa data lagras i ett data lager i din arbets yta, vilket gör att du kan tillämpa åtkomst kontroller och kryptering på lämpligt sätt.

Du kanske också vill kryptera [diagnostikinformation som loggats från den distribuerade slut punkten](how-to-enable-app-insights.md) till din Azure Application insikter-instans.

## <a name="encryption-in-transit"></a>Kryptering under överföring

Azure Machine Learning använder TLS för att skydda intern kommunikation mellan olika Azure Machine Learning mikrotjänster. Alla Azure Storage åtkomst sker också över en säker kanal.

Azure Machine Learning använder TLS för att skydda externa anrop till poäng slut punkten. Mer information finns i [använda TLS för att skydda en webb tjänst via Azure Machine Learning](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Insamling och hantering av data

### <a name="microsoft-collected-data"></a>Microsoft-insamlade data

Microsoft kan samla in information om icke-användare, t. ex. resurs namn (till exempel data uppsättningens namn eller namnet på Machine Learning-experimentet) eller jobbets miljövariabler för diagnostisk användning. Alla sådana data lagras med Microsoft-hanterade nycklar i lagring som finns i Microsofts ägda prenumerationer och följer [Microsofts standard sekretess policy och data hanterings standarder](https://privacy.microsoft.com/privacystatement).

Microsoft rekommenderar även att inte lagra känslig information (till exempel konto nyckel hemligheter) i miljövariabler. Miljövariabler loggas, krypteras och lagras av oss. Undvik att använda känslig information som användar namn eller namn på hemliga projekt på samma sätt som när du namnger [run_id](/python/api/azureml-core/azureml.core.run%28class%29). Den här informationen kan visas i telemetri loggar som är tillgängliga för Microsoft Support tekniker.

Du kan välja att inte använda diagnostikdata som samlas in genom `hbi_workspace` att ange parametern till `TRUE` medan du konfigurerar arbets ytan. Den här funktionen stöds när du använder AzureML python SDK, CLI, REST API: er eller Azure Resource Manager mallar.

## <a name="using-azure-key-vault"></a>Använda Azure Key Vault

Azure Machine Learning använder Azure Key Vault-instansen som är kopplad till arbets ytan för att lagra autentiseringsuppgifter av olika typer:

* Den associerade anslutnings strängen för lagrings kontot
* Lösen ord till Azure Container databas instanser
* Anslutnings strängar till data lager

SSH-lösenord och nycklar för att beräkna mål som Azure HDInsight och virtuella datorer lagras i ett separat nyckel valv som är associerat med Microsoft-prenumerationen. Azure Machine Learning lagrar inte lösen ord eller nycklar som tillhandahålls av användarna. I stället genererar, auktoriserar och lagrar sina egna SSH-nycklar för att ansluta till virtuella datorer och HDInsight för att köra experimenten.

Varje arbets yta har en associerad systemtilldelad hanterad identitet som har samma namn som arbets ytan. Den här hanterade identiteten har åtkomst till alla nycklar, hemligheter och certifikat i nyckel valvet.

## <a name="next-steps"></a>Nästa steg

* [Ansluta till Azure-lagring](how-to-access-data.md)
* [Hämta data från ett datalager](how-to-create-register-datasets.md)
* [Ansluta till data](how-to-connect-data-ui.md)
* [Träna med datauppsättningar](how-to-train-with-datasets.md)