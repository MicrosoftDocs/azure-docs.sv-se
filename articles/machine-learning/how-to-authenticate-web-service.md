---
title: Konfigurera autentisering för modeller som distribueras som webb tjänster
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar autentisering för Machine Learning-modeller som distribueras till webb tjänster i Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94447780"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Konfigurera autentisering för modeller som distribueras som webb tjänster

Med Azure Machine Learning kan du distribuera dina utbildade maskin inlärnings modeller som webb tjänster. I den här artikeln lär du dig hur du konfigurerar autentisering för dessa distributioner.

Modell distributioner som skapas av Azure Machine Learning kan konfigureras för att använda en av två autentiseringsmetoder:

* **nyckelbaserad**: en statisk nyckel används för att autentisera till webb tjänsten.
* **tokenbaserad**: en tillfällig token måste hämtas från Azure Machine Learning arbets ytan (med Azure Active Directory) och användas för att autentisera till webb tjänsten. Denna token upphör att gälla efter en viss tids period och måste uppdateras för att fortsätta arbeta med webb tjänsten.

    > [!NOTE]
    > Tokenbaserad autentisering är endast tillgängligt när du distribuerar till Azure Kubernetes-tjänsten.

## <a name="key-based-authentication"></a>Nyckelbaserad autentisering

Webb tjänster som distribueras på Azure Kubernetes service (AKS) har nyckelbaserad autentisering *aktiverat* som standard.

Azure Container Instances (ACI) distribuerade tjänster har nyckelbaserad autentisering *inaktive rad* som standard, men du kan aktivera den genom att ange `auth_enabled=True` när du skapar ACI-webb tjänsten. Följande kod är ett exempel på hur du skapar en distributions konfiguration för ACI med nyckelbaserad autentisering aktive rad.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Sedan kan du använda den anpassade ACI-konfigurationen i distributionen med hjälp av- `Model` klassen.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Använd för att hämta auth-nycklarna `aci_service.get_keys()` . Om du vill återskapa en nyckel använder du `regen_key()` funktionen och skickar antingen **primär** eller **sekundär**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Tokenbaserad autentisering

När du aktiverar token-autentisering för en webb tjänst måste användarna presentera en Azure Machine Learning JSON Web Token till webb tjänsten för att komma åt den. Token upphör att gälla efter en angiven tidsperiod och måste uppdateras för att anrop ska fortsätta att skickas.

* Token-autentisering **inaktive ras som standard** när du distribuerar till Azure Kubernetes-tjänsten.
* Token-autentisering **stöds inte** när du distribuerar till Azure Container instances.
* Token **-autentisering kan inte användas på samma gång som nyckelbaserad autentisering**.

Om du vill kontrol lera token-autentisering använder `token_auth_enabled` du parametern när du skapar eller uppdaterar en-distribution:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Om token-autentisering har Aktiver ATS kan du använda `get_token` metoden för att hämta en JSON Web token (JWT) och den tokens förfallo tid:

> [!TIP]
> Om du använder ett huvud namn för tjänsten för att hämta token och vill att den ska ha den lägsta åtkomst som krävs för att hämta en token tilldelar du den till rollen **läsare** för arbets ytan.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Du behöver begära en ny token efter tokens `refresh_by`-tid. Om du behöver uppdatera token utanför python SDK är ett alternativ att använda REST API med tjänstens huvudsakliga autentisering för att regelbundet `service.get_token()` anropa anropet, enligt beskrivningen ovan.
>
> Vi rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster.
>
> För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om din arbets områdes region inte är tillgänglig kan du inte hämta en token för din webb tjänst, även om klustret finns i en annan region än din arbets yta. Resultatet är att Azure AD-autentisering inte är tillgängligt förrän din region för arbets ytan är tillgänglig igen.
>
> Dessutom ökar avståndet mellan ditt klusters region och din region för arbets ytan, desto längre tid tar det att hämta en token.

## <a name="next-steps"></a>Nästa steg

Mer information om autentisering till en distribuerad modell finns i [skapa en klient för en modell som distribueras som en webb tjänst](how-to-consume-web-service.md).