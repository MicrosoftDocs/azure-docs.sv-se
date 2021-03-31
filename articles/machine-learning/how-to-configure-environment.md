---
title: Konfigurera python-utvecklings miljö
titleSuffix: Azure Machine Learning
description: Konfigurera Azure Machine Learning python-utvecklings miljöer i Jupyter notebooks, Visual Studio Code, Azure Databricks och data science Virtual Machines.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 7e493404e7a36b60e8f9e62cd4ac2f2b32845bb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102501625"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Konfigurera en python-utvecklings miljö för Azure Machine Learning

Lär dig hur du konfigurerar en python-utvecklings miljö för Azure Machine Learning.

I följande tabell visas varje utvecklings miljö som beskrivs i den här artikeln, tillsammans med-och-och nack delar.

| Miljö | Fördelar | Nackdelar |
| --- | --- | --- |
| [Lokal miljö](#local) | Fullständig kontroll över utvecklings miljön och beroenden. Kör med valfri build-verktyg, miljö eller IDE som du väljer. | Det tar längre tid att komma igång. Nödvändiga SDK-paket måste vara installerade och en miljö måste också installeras om du inte redan har ett. |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Precis som den molnbaserade Compute-instansen (python och SDK är förinstallerade), men med ytterligare populära data vetenskaps-och maskin inlärnings verktyg förinstallerade. Lätt att skala och kombinera med andra anpassade verktyg och arbets flöden. | En långsammare upplevelse som är igång jämfört med den molnbaserade beräknings instansen. |
| [Azure Machine Learning-beräkningsinstans](#compute-instance) | Enkelt sätt att komma igång. Hela SDK är redan installerat på den virtuella arbets ytan och själv studie kurser för bärbara datorer är förklonade och redo att köras. | Brist på kontroll över utvecklings miljön och beroenden. Ytterligare kostnader för virtuella Linux-datorer (VM kan stoppas när de inte används för att undvika avgifter). Se [pris information](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | Perfekt för att köra storskaliga arbets flöden för storskalig dator inlärning på den skalbara Apache Sparks plattformen. | Överkurs för experimentell maskin inlärning, eller mindre skalade experiment och arbets flöden. Ytterligare kostnader för Azure Databricks. Se [pris information](https://azure.microsoft.com/pricing/details/databricks/). |

Den här artikeln innehåller också ytterligare användnings tips för följande verktyg:

* Jupyter Notebooks: om du redan använder Jupyter notebook-datorer, har SDK en del tillägg som du bör installera.

* Visual Studio Code: om du använder Visual Studio Code innehåller [Azure Machine Learning tillägget](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) omfattande språk stöd för python samt funktioner för att arbeta med Azure Machine Learning mycket bekvämare och produktivitet.

## <a name="prerequisites"></a>Förutsättningar

* Azure Machine Learning arbets yta. Om du inte har någon kan du skapa en Azure Machine Learning arbets yta via [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)och [Azure Resource Manager mallar](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> Endast lokalt och DSVM: skapa en konfigurations fil för arbets ytor

Konfigurations filen för arbets ytan är en JSON-fil som talar om för SDK hur du kommunicerar med din Azure Machine Learning-arbetsyta. Filen heter *config.jspå* och har följande format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Den här JSON-filen måste finnas i katalog strukturen som innehåller dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml* eller i en överordnad katalog.

Använd-metoden för att använda den här filen från din kod [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace#from-config-path-none--auth-none---logger-none---file-name-none-) . Den här koden läser in informationen från filen och ansluter till din arbets yta.

Skapa en konfigurations fil för arbets ytor på något av följande sätt:

* Azure Portal

    **Hämta filen**: i [Azure Portal](https://ms.portal.azure.com)väljer du  **Hämta config.jspå** från **översikts** avsnittet på din arbets yta.

    ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning python SDK

    Skapa ett skript för att ansluta till din Azure Machine Learning-arbetsyta och Använd [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace#write-config-path-none--file-name-none-) metoden för att generera filen och spara den som *. azureml/config.jspå*. Se till att ersätta `subscription_id` , `resource_group` och `workspace_name` med din egen.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>Lokal dator eller fjärran sluten VM-miljö

Du kan ställa in en miljö på en lokal dator eller en virtuell dator, till exempel en Azure Machine Learning beräknings instans eller Data Science VM. 

Konfigurera en lokal utvecklings miljö eller virtuell fjärrdator:

1. Skapa en virtuell python-miljö (virtuell miljö, Conda).

    > [!NOTE]
    > Även om det inte krävs rekommenderar vi att du använder [Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://www.anaconda.com/download/) för att hantera python-virtuella miljöer och installera paket.

    > [!IMPORTANT]
    > Om du använder Linux eller macOS och använder ett annat skal än bash (till exempel zsh) kan du få fel när du kör vissa kommandon. Undvik det här problemet genom `bash` att använda kommandot för att starta ett nytt bash-gränssnitt och köra kommandona där.

1. Aktivera din nyligen skapade python Virtual-miljö.
1. Installera [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install).
1. Om du vill konfigurera din lokala miljö för att använda din Azure Machine Learning arbets yta [skapar du en konfigurations fil för arbets ytor](#workspace) eller använder en befintlig.

Nu när du har konfigurerat din lokala miljö är du redo att börja arbeta med Azure Machine Learning. Kom igång genom att gå till [Azure Machine Learning python-guiden](tutorial-1st-experiment-sdk-setup-local.md) för att komma igång.

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

När du kör en lokal Jupyter Notebook server rekommenderar vi att du skapar en IPython-kernel för din virtuella python-miljö. Detta säkerställer det förväntade beteendet för kernel-och paket import.

1. Aktivera miljöbaserade IPython-kärnor

    ```bash
    conda install notebook ipykernel
    ```

1. Skapa en kernel för din virtuella python-miljö. Ersätt `<myenv>` med namnet på din virtuella python-miljö.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Starta Jupyter Notebook-servern

Se [Azure Machine Learning Notebooks-lagringsplatsen](https://github.com/Azure/MachineLearningNotebooks) för att komma igång med Azure Machine Learning-och Jupyter-anteckningsböcker.

> [!NOTE]
> Du hittar en community-driven databas med exempel på https://github.com/Azure/azureml-examples .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visuell Studio-kod

Så här använder du Visual Studio Code för utveckling:

1. Installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Installera [Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md) (för hands version).

När du har installerat Visual Studio Code-tillägget kan du hantera dina [Azure Machine Learning-resurser](how-to-manage-resources-vscode.md), [köra och felsöka experiment](how-to-debug-visual-studio-code.md)och [distribuera utbildade modeller](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning-beräkningsinstans

Azure Machine Learning [Compute-instansen](concept-compute-instance.md) är en säker, molnbaserad Azure-arbetsstation som tillhandahåller data experter med en Jupyter Notebook-Server, JupyterLab och en helt hanterad maskin inlärnings miljö.

Det finns inget att installera eller konfigurera för en beräknings instans.  

Skapa en när som helst i din Azure Machine Learning-arbetsyta. Ange ett namn och ange en typ av virtuell Azure-dator. Prova nu med den här [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md).

Mer information om beräknings instanser, inklusive hur du installerar paket finns i [skapa och hantera en Azure Machine Learning beräknings instans](how-to-create-manage-compute-instance.md).

> [!TIP]
> [Stoppa beräknings instansen](how-to-create-manage-compute-instance.md#manage)för att förhindra kostnader för en oanvänd beräknings instans.

Förutom en Jupyter Notebook Server-och JupyterLab kan du använda beräknings instanser i den [integrerade Notebook-funktionen i Azure Machine Learning Studio](how-to-run-jupyter-notebooks.md).

Du kan också använda tillägget Azure Machine Learning Visual Studio Code för att [Konfigurera en Azure Machine Learning beräknings instans som en fjärran sluten Jupyter Notebook Server](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuell dator för datavetenskap

Data Science VM är en anpassad avbildning av virtuell dator (VM) som du kan använda som utvecklings miljö. Det är utformat för data vetenskaps arbete som är förkonfigurerade verktyg och program som:

  - Paket som TensorFlow, PyTorch, Scikit-lära, XGBoost och Azure Machine Learning SDK
  - Populära verktyg för data vetenskap som Spark standalone och detalj nivå
  - Azure-verktyg som Azure CLI, AzCopy och Storage Explorer
  - Integrerade utvecklings miljöer (IDE: er) som Visual Studio Code och pycharm med
  - Jupyter Notebook Server

En mer omfattande lista över verktygen finns i [Guide för data science VM verktyg](data-science-virtual-machine/tools-included.md).

> [!IMPORTANT]
> Om du planerar att använda Data Science VM som ett [beräknings mål](concept-compute-target.md) för utbildnings-eller inferencing-jobb stöds endast Ubuntu.

Så här använder du Data Science VM som utvecklings miljö:

1. Skapa en Data Science VM med någon av följande metoder:

    * Använd Azure Portal för att skapa en [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) -eller [Windows](data-science-virtual-machine/provision-vm.md) -DSVM.
    * [Skapa en data science VM med ARM-mallar](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Använda Azure CLI

        Om du vill skapa en Ubuntu Data Science VM använder du följande kommando:

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Om du vill skapa en Windows-DSVM använder du följande kommando:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Aktivera Conda-miljön som innehåller Azure Machine Learning SDK.

    * För Ubuntu Data Science VM:

        ```bash
        conda activate py36
        ```

    * För Windows-Data Science VM:

        ```bash
        conda activate AzureML
        ```

1. Om du vill konfigurera Data Science VM att använda arbets ytan Azure Machine Learning [skapar du en konfigurations fil för arbets ytor](#workspace) eller använder en befintlig.

Precis som i lokala miljöer kan du använda Visual Studio Code och [tillägget Azure Machine Learning Visual Studio Code](#vscode) för att interagera med Azure Machine Learning.

Mer information finns i [Virtual Machines för data vetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).


## <a name="next-steps"></a>Nästa steg

- [Träna en modell](tutorial-train-models-with-aml.md) på Azure Machine Learning med MNIST-datauppsättningen.
- Se [Azure Machine Learning SDK för python-referens](/python/api/overview/azure/ml/intro). 