---
title: Distribuera modeller till beräknings instanser
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning modeller som en webb tjänst med hjälp av beräknings instanser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: d8b1c2a5384e479e39d169d368554f16c300a33e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954554"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Distribuera en modell för att Azure Machine Learning beräknings instanser



Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på din Azure Machine Learning beräknings instans. Använd beräknings instanser om något av följande villkor är uppfyllt:

- Du måste snabbt distribuera och validera din modell.
- Du testar en modell som är under utveckling.

> [!TIP]
> Att distribuera en modell från en Jupyter Notebook på en beräknings instans till en webb tjänst på samma virtuella dator är en _lokal distribution_. I det här fallet är den lokala datorn beräknings instansen. Mer information om distributioner finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta med en beräknings instans som körs. Mer information finns i [installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Distribuera till beräknings instanserna

En exempel-anteckningsbok som visar lokala distributioner ingår i din beräknings instans. Använd följande steg för att läsa in den bärbara datorn och distribuera modellen som en webb tjänst på den virtuella datorn:

1. Välj "Notebooks" från [Azure Machine Learning Studio](https://ml.azure.com)och välj sedan How-to-use-azureml/Deployment/Deploy-to-Local/register-Model-Deploy-Local. ipynb under "exempel på bärbara datorer". Klona den här antecknings boken till din användar-mapp.

1. Leta upp den bärbara datorn som klonades i steg 1, Välj eller skapa en beräknings instans för att köra antecknings boken.

    ![Skärm bild av den lokala tjänsten som körs på antecknings boken](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. Antecknings boken visar den URL och port som tjänsten körs på. Ett exempel är `https://localhost:6789`. Du kan också köra cellen som innehåller `print('Local service port: {}'.format(local_service.port))` för att Visa porten.

    ![Skärm bild av den aktiva porten för den lokala tjänsten](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Använd URL: en för att testa tjänsten från en beräknings instans `https://localhost:<local_service.port>` . Om du vill testa från en fjärran sluten klient hämtar du den offentliga URL: en för tjänsten som körs på beräknings instansen. Den offentliga URL: en kan fastställas med hjälp av följande formel. 
    * VM för virtuell dator: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Beräknings instans: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Exempel: 
    * VM för virtuell dator: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Beräknings instans: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testa tjänsten

Använd följande kod för att skicka exempel data till den aktiva tjänsten. Ersätt värdet för `service_url` med URL: en från föregående steg:

> [!NOTE]
> Vid autentisering till en distribution på beräknings instansen görs autentiseringen med hjälp av Azure Active Directory. Anropet till `interactive_auth.get_authentication_header()` i exempel koden autentiserar dig med hjälp av AAD och returnerar en rubrik som sedan kan användas för att autentisera till tjänsten på beräknings instansen. Mer information finns i [Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden](how-to-setup-authentication.md#interactive-authentication).
>
> När du autentiserar till en distribution på Azure Kubernetes service eller Azure Container Instances, används en annan autentiseringsmetod. Mer information finns i [Konfigurera autentisering för Azure Machine Model-distribuerade som webb tjänster](how-to-authenticate-web-service.md).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Nästa steg

* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
