---
title: Skapa arbets ytor i portalen
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar, visar och tar bort Azure Machine Learning arbets ytor i Azure Portal eller med SDK för python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 472bc66c75881d622e8ecfe23031f58db773a919
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518933"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Skapa och hantera Azure Machine Learning arbets ytor 

I den här artikeln skapar du, visar och tar bort [**Azure Machine Learning arbets ytor**](concept-workspace.md) för [Azure Machine Learning](overview-what-is-azure-ml.md)med hjälp av Azure Portal eller [SDK för python](/python/api/overview/azure/ml/)

När du behöver ändra eller krav på Automation-höjning kan du också skapa och ta bort arbets ytor [med hjälp av CLI](reference-azure-machine-learning-cli.md), eller [via vs Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.
* Om du använder python SDK [installerar du SDK: n](/python/api/overview/azure/ml/install).

## <a name="limitations"></a>Begränsningar

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

När du skapar en arbets yta skapas också en Azure Container Registry (ACR) som standard.  Eftersom ACR för närvarande inte stöder Unicode-tecken i resurs grupp namn använder du en resurs grupp som inte innehåller dessa tecken.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

# <a name="python"></a>[Python](#tab/python)

* **Standard specifikation.** Som standard skapas beroende resurser och resurs gruppen automatiskt. Den här koden skapar en arbets yta med namnet `myworkspace` och en resurs grupp med namnet `myresourcegroup` i `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Ange `create_resource_group` till false om du har en befintlig Azure-resurs grupp som du vill använda för arbets ytan.

* <a name="create-multi-tenant"></a>**Flera klienter.**  Om du har flera konton lägger du till klient-ID: t för den Azure Active Directory som du vill använda.  Hitta klient-ID: t från [Azure Portal](https://portal.azure.com) under **Azure Active Directory, externa identiteter**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Suveräna moln](reference-machine-learning-cloud-parity.md)**. Du behöver extra kod för att autentisera till Azure om du arbetar i ett suveränt moln.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Använd befintliga Azure-resurser**.  Du kan också skapa en arbets yta som använder befintliga Azure-resurser med ID-formatet för Azure-resurs. Hitta de aktuella Azure-resurs-ID: na i Azure Portal eller med SDK. I det här exemplet förutsätts att resurs gruppen, lagrings kontot, nyckel valvet, App Insights-och container registret redan finns.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Mer information finns i [SDK-referens för arbets yta](/python/api/azureml-core/azureml.core.workspace.workspace).

Om du har problem med att komma åt din prenumeration kan du läsa [Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden](how-to-setup-authentication.md)samt [autentiseringen i Azure Machine Learning](https://aka.ms/aml-notebook-auth) Notebook.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration. 

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

      ![Skapa en ny resurs](./media/how-to-manage-workspace/create-workspace.gif)

1. Använd Sök fältet för att hitta **Machine Learning**.

1. Välj **Machine Learning**.

1. I fönstret **Machine Learning** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Fält|Beskrivning 
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra. Namnet på arbets ytan är Skift läges okänsligt.
   Prenumeration |Välj den Azure-prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i din prenumeration eller ange ett namn för att skapa en ny resursgrupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. Du behöver *deltagar* -eller *ägar* rollen för att använda en befintlig resurs grupp.  Mer information om åtkomst finns i [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md).
   Region | Välj den Azure-region som är närmast dina användare och data resurserna för att skapa din arbets yta.
   | Lagringskonto | Standard lagrings kontot för arbets ytan. Som standard skapas en ny. |
   | Key Vault | Azure Key Vault som används av arbets ytan. Som standard skapas en ny. |
   | Application Insights | Application Insights-instansen för arbets ytan. Som standard skapas en ny. |
   | Container Registry | Arbets ytans Azure Container Registry. Som standard skapas en ny _inte_ i början av arbets ytan. I stället skapas den när du behöver den när du skapar en Docker-avbildning under utbildning eller distribution. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Konfigurera din arbets yta.":::

1. När du är klar med konfigurationen av arbets ytan väljer du **Granska + skapa**. Du kan också använda avsnitten [nätverk](#networking) och [Avancerat](#advanced) för att konfigurera fler inställningar för arbets ytan.

1. Granska inställningarna och gör eventuella ytterligare ändringar eller korrigeringar. När du är nöjd med inställningarna väljer du **skapa**.

   > [!Warning] 
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.
 
---



### <a name="networking"></a>Nätverk  

> [!IMPORTANT]  
> Mer information om hur du använder en privat slut punkt och ett virtuellt nätverk med din arbets yta finns i [nätverks isolering och sekretess](how-to-network-security-overview.md).


# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning python SDK tillhandahåller klassen [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) , som kan användas med [arbets ytan. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) för att skapa en arbets yta med en privat slut punkt. Den här klassen kräver ett befintligt virtuellt nätverk.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Standard nätverks konfigurationen är att använda en __offentlig slut punkt__ som är tillgänglig på det offentliga Internet. Om du vill begränsa åtkomsten till din arbets yta till en Azure-Virtual Network du har skapat, kan du i stället välja __privat slut punkt__ (för hands version) som __anslutnings metod__ och sedan använda __+ Lägg__ till för att konfigurera slut punkten.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Val av privat slut punkt":::  

1. I formuläret __skapa privat slut punkt__ anger du den plats, det namn och det virtuella nätverk som ska användas. Om du vill använda slut punkten med en Privat DNS zon väljer du __integrera med privat DNS-zon__ och väljer zonen i fältet __privat DNS zon__ . Välj __OK__ för att skapa slut punkten.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Skapa privat slut punkt":::   

1. När du är färdig med konfigurationen av nätverk kan du välja __Granska + skapa__ eller gå vidare till den valfria __avancerade__ konfigurationen.

---

> [!IMPORTANT]  
> Att använda en privat slut punkt med Azure Machine Learning arbets ytan är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.     
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Flera arbets ytor med privat slut punkt

När du skapar en privat slut punkt skapas en ny Privat DNS zon med namnet __privatelink.API.azureml.MS__ . Innehåller en länk till det virtuella nätverket. Om du skapar flera arbets ytor med privata slut punkter i samma resurs grupp, kan endast det virtuella nätverket för den första privata slut punkten läggas till i DNS-zonen. Använd följande steg för att lägga till poster för de virtuella nätverk som används av ytterligare arbets ytor/privata slut punkter:

1. I [Azure Portal](https://portal.azure.com)väljer du den resurs grupp som innehåller arbets ytan. Välj sedan Privat DNS zon resurs med namnet __privatelink.API.azureml.MS__
2. I __inställningarna__ väljer du __virtuella nätverks länkar__.
3. Välj __Lägg till__. På sidan __Lägg till virtuell nätverks länk__ anger du ett unikt __länk namn__ och väljer sedan det __virtuella nätverk__ som ska läggas till. Välj __OK__ för att lägga till nätverks länken.

Mer information finns i [Azures DNS-konfiguration för privat slut punkt](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Sårbarhetsgenomsökning

Azure Security Center erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Du bör tillåta Azure Security Center att söka igenom dina resurser och följa rekommendationerna. Mer information finns i  [Azure Container Registry avbildnings genomsökning av Security Center](../security-center/defender-for-container-registries-introduction.md) och [integrering med Azure Kubernetes Services med Security Center](../security-center/defender-for-kubernetes-introduction.md).

### <a name="advanced"></a>Avancerat

Som standard lagras metadata för arbets ytan i en Azure Cosmos DB-instans som Microsoft underhåller. Dessa data är krypterade med Microsoft-hanterade nycklar.

Om du vill begränsa de data som Microsoft samlar in på din arbets yta väljer du __arbets ytan hög arbets yta för affärs påverkan__ i portalen eller ställer in `hbi_workspace=true ` i python. Mer information om den här inställningen finns i [kryptering i vila](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> Du kan bara välja hög påverkan på verksamheten när du skapar en arbets yta. Du kan inte ändra den här inställningen när du har skapat arbets ytan.   

#### <a name="use-your-own-key"></a>Använd din egen nyckel

Du kan ange din egen nyckel för data kryptering. Om du gör det skapas Azure Cosmos DB-instansen som lagrar metadata i din Azure-prenumeration.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Använd följande steg för att ange din egen nyckel:

> [!IMPORTANT]  
> Innan du följer de här stegen måste du först utföra följande åtgärder:   
>
> 1. Auktorisera __Machine Learning-appen__ (i identitets-och åtkomst hantering) med deltagar behörigheter för din prenumeration.  
> 1. Följ stegen i [Konfigurera Kundhanterade nycklar](../cosmos-db/how-to-setup-cmk.md) för att:
>     * Registrera Azure Cosmos DB-providern
>     * Skapa och konfigurera en Azure Key Vault
>     * Generera en nyckel
>   
>     Du behöver inte skapa Azure Cosmos DB-instansen manuellt, en skapas automatiskt när du skapar arbets ytan. Den här Azure Cosmos DB-instansen skapas i en separat resurs grupp med hjälp av ett namn baserat på det här mönstret: `<your-workspace-resource-name>_<GUID>` .   
>   
> Du kan inte ändra den här inställningen när du har skapat arbets ytan. Om du tar bort Azure Cosmos DB som används av din arbets yta, måste du också ta bort arbets ytan som använder den.

# <a name="python"></a>[Python](#tab/python)

Använd `cmk_keyvault` och `resource_cmk_uri` för att ange kundens hanterade nyckel.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj __Kundhanterade nycklar__ och välj sedan __knappen Klicka för att välja nyckel__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Kundhanterade nycklar":::

1. I formuläret __Välj nyckel från Azure Key Vault__ väljer du en befintlig Azure Key Vault, en nyckel som den innehåller och nyckelns version. Den här nyckeln används för att kryptera data som lagras i Azure Cosmos DB. Använd slutligen knappen __Välj__ för att använda den här nyckeln.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Välj nyckeln":::

---

### <a name="download-a-configuration-file"></a>Hämta en konfigurations fil

Om du kommer att skapa en [beräknings instans](tutorial-1st-experiment-sdk-setup.md#azure)hoppar du över det här steget.  Compute-instansen har redan skapat en kopia av den här filen åt dig.

# <a name="python"></a>[Python](#tab/python)

Om du planerar att använda kod i din lokala miljö som refererar till den här arbets ytan ( `ws` ) skriver du konfigurations filen:

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Om du planerar att använda kod i din lokala miljö som hänvisar till den här arbets ytan väljer du  **hämta config.jspå** i **översikts** avsnittet i arbets ytan.  

   ![Ladda ned config.jspå](./media/how-to-manage-workspace/configure.png)

---

Placera filen i katalog strukturen med dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml* eller i en överordnad katalog. När du skapar en beräknings instans läggs den här filen till i rätt katalog på den virtuella datorn åt dig.

## <a name="connect-to-a-workspace"></a>Anslut till en arbetsyta

I python-koden skapar du ett objekt för arbets ytan för att ansluta till din arbets yta.  Den här koden kommer att läsa innehållet i konfigurations filen för att hitta din arbets yta.  Du får en uppfråga om du vill logga in om du inte redan har autentiserats.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Flera klienter.**  Om du har flera konton lägger du till klient-ID: t för den Azure Active Directory som du vill använda.  Hitta klient-ID: t från [Azure Portal](https://portal.azure.com) under **Azure Active Directory, externa identiteter**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Suveräna moln](reference-machine-learning-cloud-parity.md)**. Du behöver extra kod för att autentisera till Azure om du arbetar i ett suveränt moln.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Om du har problem med att komma åt din prenumeration kan du läsa [Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden](how-to-setup-authentication.md)samt [autentiseringen i Azure Machine Learning](https://aka.ms/aml-notebook-auth) Notebook.

## <a name="find-a-workspace"></a><a name="view"></a>Hitta en arbets yta

Se en lista över alla arbets ytor som du kan använda.

# <a name="python"></a>[Python](#tab/python)

Hitta dina prenumerationer på [sidan prenumerationer i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  Kopiera ID: t och Använd det i koden nedan för att se alla tillgängliga arbets ytor för den prenumerationen.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I det övre Sök fältet skriver du **Machine Learning**.  

1. Välj **Machine Learning**.

   ![Sök efter Azure Machine Learning arbets yta](./media/how-to-manage-workspace/find-workspaces.png)

1. Titta igenom listan med arbets ytor som har hittats. Du kan filtrera baserat på prenumeration, resurs grupper och platser.  

1. Välj en arbets yta för att visa dess egenskaper.

---


## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

När du inte längre behöver en arbets yta tar du bort den.  

# <a name="python"></a>[Python](#tab/python)

Ta bort arbets ytan `ws` :

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

Standard åtgärden är inte att ta bort resurser som är associerade med arbets ytan, t. ex. behållar registret, lagrings kontot, nyckel valvet och Application Insights.  Ställ in `delete_dependent_resources` på sant för att ta bort dessa resurser också.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I [Azure Portal](https://portal.azure.com/)väljer du **ta bort**  överst i arbets ytan som du vill ta bort.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Ta bort arbetsyta":::

---

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Felsökning

* **Webbläsare som stöds i Azure Machine Learning Studio**: Vi rekommenderar att du använder den senaste aktuella webbläsaren som är kompatibel med ditt operativ system. Följande webbläsare stöds:
  * Microsoft Edge (den nya Microsoft Edge, senaste versionen. Inte Microsoft Edge Legacy)
  * Safari (senaste versionen, endast Mac)
  * Chrome (senaste versionen)
  * Firefox (senaste versionen)

* **Azure Portal**: 
  * Om du går direkt till din arbets yta från en resurs länk från SDK eller Azure Portal kan du inte Visa standard **översikts** sidan med prenumerations information i tillägget. I det här scenariot kan du inte heller byta till en annan arbets yta. Om du vill visa en annan arbets yta går du direkt till [Azure Machine Learning Studio](https://ml.azure.com) och söker efter namnet på arbets ytan.
  * Alla till gångar (data uppsättningar, experiment, beräkningar och så vidare) är bara tillgängliga i [Azure Machine Learning Studio](https://ml.azure.com). De är *inte* tillgängliga från Azure Portal.

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbets ytan

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Tar bort Azure Container Registry

I arbets ytan Azure Machine Learning används Azure Container Registry (ACR) för vissa åtgärder. En ACR-instans skapas automatiskt när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Exempel

Exempel på hur du skapar en arbets yta:
* Använd Azure Portal för att [skapa en arbets yta och en beräknings instans](tutorial-1st-experiment-sdk-setup.md)
* Använd python SDK för att [skapa en arbets yta i din egen miljö](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>Nästa steg

När du har en arbets yta kan du lära dig hur du [tränar och distribuerar en modell](tutorial-train-models-with-aml.md).