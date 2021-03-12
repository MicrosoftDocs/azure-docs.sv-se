---
title: Starta, övervaka och avbryta inlärnings körningar i python
titleSuffix: Azure Machine Learning
description: Lär dig hur du startar, status och hanterar dina Machine Learning-experiment körs med Azure Machine Learning python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 977498abb17fe592cef344f407a662d3b79749b7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634782"
---
# <a name="start-monitor-and-track-runs"></a>Starta, övervaka och spåra körningar 

[Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro), [Machine Learning CLI](reference-azure-machine-learning-cli.md)och [Azure Machine Learning Studio](https://ml.azure.com) tillhandahåller olika metoder för att övervaka, organisera och hantera dina körningar för utbildning och experimentering.

Den här artikeln innehåller exempel på följande uppgifter:

* Övervaka körnings prestanda.
* Övervaka körnings statusen per e-postavisering.
* Tagga och hitta körningar.
* Lägg till en körnings beskrivning. 
* Kör sökningen. 
* Avbryt eller kör inte.
* Skapa underordnade körningar.
 

> [!TIP]
> Om du vill ha information om hur du övervakar Azure Machine Learning tjänsten och tillhör ande Azure-tjänster, se [övervaka Azure Machine Learning](monitor-azure-machine-learning.md).
> Om du letar efter information om övervaknings modeller som har distribuerats som webb tjänster eller IoT Edge moduler, se [samla in modell data](how-to-enable-data-collection.md) och [övervaka med Application Insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande objekt:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* Azure Machine Learning SDK för python (version 1.0.21 eller senare). Information om hur du installerar eller uppdaterar till den senaste versionen av SDK finns i [Installera eller uppdatera SDK](/python/api/overview/azure/ml/install).

    Använd följande kod för att kontrol lera din version av Azure Machine Learning SDK:

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) -och [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Övervaka körnings prestanda

* Starta en körning och dess loggnings process

    # <a name="python"></a>[Python](#tab/python)
    
    1. Konfigurera experimentet genom att importera [arbets ytan](/python/api/azureml-core/azureml.core.workspace.workspace), [experimentet](/python/api/azureml-core/azureml.core.experiment.experiment), [körnings](/python/api/azureml-core/azureml.core.run%28class%29)-och [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) -klasserna från [azureml. Core](/python/api/azureml-core/azureml.core) -paketet.
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Starta en körning och dess loggnings process med- [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metoden.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Gör så här för att starta en körning av experimentet:
    
    1. Använd Azure CLI från ett gränssnitt eller en kommando tolk för att autentisera till din Azure-prenumeration:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Koppla en arbets ytans konfiguration till den mapp som innehåller ditt utbildnings skript. Ersätt `myworkspace` med din Azure Machine Learning-arbetsyta. Ersätt `myresourcegroup` med Azure-resurs gruppen som innehåller din arbets yta:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Det här kommandot skapar en `.azureml` under katalog som innehåller exempel på runconfig-och Conda-miljöfiler. Den innehåller också en `config.json` fil som används för att kommunicera med din Azure Machine Learning-arbetsyta.
    
        Mer information finns i [AZ ml Folder Attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Starta körningen med hjälp av följande kommando. När du använder det här kommandot anger du namnet på runconfig-filen (texten före \* . runconfig om du tittar på ditt fil system) mot parametern-c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach`Kommandot skapade en `.azureml` under katalog, som innehåller två exempel runconfig-filer.
        >
        > Om du har ett Python-skript som skapar ett kör konfigurations objekt program mässigt kan du använda [RunConfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) för att spara det som en RunConfig-fil.
        >
        > Fler exempel på runconfig-filer finns i [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Mer information finns i [AZ ml Run Submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Ett exempel på hur du tränar en modell i Azure Machine Learning Designer finns i [Självstudier: förutsäga bil priser med designern](tutorial-designer-automobile-price-train-score.md).

    ---

* Övervaka status för en körning

    # <a name="python"></a>[Python](#tab/python)
    
    * Hämta status för en körning med- [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) metoden.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Använd-metoden för att hämta körnings-ID, körnings tid och ytterligare information om körningen [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) .
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * När körningen har slutförts använder du [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) metoden för att markera den som slutförd.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Om du använder python `with...as` : s design mönster markeras körningen automatiskt som slutförd när körningen är utanför omfånget. Du behöver inte markera kör som slutförd manuellt.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * Om du vill visa en lista över körningar för experimentet använder du följande kommando. Ersätt `experiment` med namnet på ditt experiment:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Det här kommandot returnerar ett JSON-dokument som visar information om körningar för det här experimentet.
    
        Mer information finns i [AZ ml experiment List](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Använd följande kommando om du vill visa information om en speciell körning. Ersätt `runid` med ID: t för körningen:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Det här kommandot returnerar ett JSON-dokument som visar information om körningen.
    
        Mer information finns i [AZ ml Run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Så här visar du dina körningar i Studio: 
    
    1. Gå till fliken **experiment** .
    
    1. Välj antingen **alla experiment** för att visa alla körningar i ett experiment eller Välj **alla körningar** för att visa alla körningar som skickats in i arbets ytan.
    
        På sidan **alla körningar** kan du filtrera körnings listan efter taggar, experiment, Compute Target och mer för att bättre organisera och begränsa ditt arbete.  
    
    1. Gör anpassningar på sidan genom att välja körningar för att jämföra, lägga till diagram eller använda filter. Dessa ändringar kan sparas som en **anpassad vy** så att du enkelt kan gå tillbaka till ditt arbete. Användare med behörighet för arbets ytan kan redigera eller Visa den anpassade vyn. Du kan också dela den anpassade vyn med grupp medlemmar för förbättrat samarbete genom att välja **vyn dela**.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Skärm bild: skapa en anpassad vy":::
    
    1. Om du vill visa körnings loggarna väljer du en speciell körning och på fliken **utdata + loggar** kan du hitta diagnostik-och fel loggar för din körning.
    
    ---

## <a name="monitor-the-run-status-by-email-notification"></a>Övervaka körnings status via e-postavisering

1. I [Azure Portal](https://ms.portal.azure.com/)i det vänstra navigerings fältet väljer du fliken **övervaka** . 

1. Välj **diagnostikinställningar** och välj sedan **+ Lägg till diagnostisk inställning**.

    ![Skärm bild av diagnostikinställningar för e-postavisering](./media/how-to-manage-runs/diagnostic-setting.png)

1. I den diagnostiska inställningen 
    1. under **kategori Detaljer** väljer du **AmlRunStatusChangedEvent**. 
    1. I **mål informationen** väljer du **arbets ytan skicka till Log Analytics** och anger **arbets ytan** för **prenumeration** och Log Analytics. 

    > [!NOTE]
    > **Azure Log Analytics-arbetsytan** är en annan typ av Azure-resurs än **Azure Machine Learning service-arbetsytan**. Om det inte finns några alternativ i listan kan du [skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace). 
    
    ![Var du ska spara e-postavisering](./media/how-to-manage-runs/log-location.png)

1. Lägg till en **ny aviserings regel** på fliken **loggar** . 

    ![Ny varnings regel](./media/how-to-manage-runs/new-alert-rule.png)

1. Se [hur du skapar och hanterar logg aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="run-description"></a>Körnings Beskrivning 

En körnings beskrivning kan läggas till i en körning för att ge mer kontext och information till körningen. Du kan också söka efter de här beskrivningarna i listan körningar och lägga till körnings beskrivningen som en kolumn i körnings listan. 

Gå till sidan med **körnings information** för din körning och välj ikonen Redigera eller Penn för att lägga till, redigera eller ta bort beskrivningar för din körning. Spara ändringarna i den befintliga anpassade vyn eller en ny anpassad vy om du vill spara ändringarna i körnings listan. Markdown-formatet stöds för körnings beskrivningar som gör att bilder kan bäddas in och djup länkas enligt nedan.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="Skärm bild: skapa en körnings Beskrivning"::: 

## <a name="tag-and-find-runs"></a>Tagga och hitta körningar

I Azure Machine Learning kan du använda egenskaper och taggar för att organisera och fråga dina körningar efter viktig information.

* Lägg till egenskaper och Taggar

    # <a name="python"></a>[Python](#tab/python)
    
    Om du vill lägga till sökbara metadata i dina körningar använder du- [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) metoden. Följande kod lägger till exempel till `"author"` egenskapen i kör:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Egenskaperna är oföränderliga så att de skapar en permanent post för gransknings syfte. Följande kod exempel resulterar i ett fel eftersom vi redan har lagts till `"azureml-user"` som `"author"` egenskaps värde i föregående kod:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Till skillnad från egenskaper är taggarna föränderligt. Använd-metoden för att lägga till sökbar och meningsfull information för användare av experimentet [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) .
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Du kan också lägga till enkla sträng taggar. När taggarna visas i kod ord listan som nycklar har de värdet `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > Med CLI kan du bara lägga till eller uppdatera taggar.
    
    Använd följande kommando om du vill lägga till eller uppdatera en tagg:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Mer information finns i [AZ ml kör Update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Du kan lägga till, redigera eller ta bort Run-taggar från Studio. Gå till sidan med **körnings information** för din körning och välj ikonen Redigera eller Penn för att lägga till, redigera eller ta bort taggar för dina körningar. Du kan också söka efter och filtrera dessa taggar från körnings List sidan.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Skärm bild: Lägg till, redigera eller ta bort körnings Taggar":::
    
    ---

* Frågeegenskaper och Taggar

    Du kan köra frågor i ett experiment för att returnera en lista över körningar som matchar vissa egenskaper och taggar.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI stöder [JMESPath](http://jmespath.org) -frågor som kan användas för att filtrera körningar baserat på egenskaper och taggar. Om du vill använda en JMESPath-fråga med Azure CLI anger du den med `--query` parametern. I följande exempel visas några frågor med hjälp av egenskaper och Taggar:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Mer information om hur du frågar Azure CLI-resultat finns i [läsa utdata från Azure CLI-kommandot](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Om du vill söka efter vissa körningar navigerar du till listan  **alla körningar** . Där finns det två alternativ:
    
    1. Använd knappen **Lägg till filter** och välj filtrera efter taggar för att filtrera dina körningar efter tagg som har tilldelats till körningen. <br><br>
    ELLER
    
    1. Använd Sök fältet för att snabbt hitta körningar genom att söka på metadata för körning, t. ex. körnings status, beskrivningar, experiment namn och sändnings namn. 
    
## <a name="cancel-or-fail-runs"></a>Avbryt eller kör inte

Om du ser ett fel eller om körningen tar för lång tid att slutföra, kan du avbryta körningen.

# <a name="python"></a>[Python](#tab/python)

Om du vill avbryta en körning med hjälp av SDK använder du [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) metoden:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Om din körning är klar, men den innehåller ett fel (till exempel om ett felaktigt utbildnings skript användes), kan du använda [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) metoden för att markera den som misslyckad.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill avbryta en körning med hjälp av CLI använder du följande kommando. Ersätt `runid` med ID: t för körningen

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Mer information finns i [AZ ml kör Avbryt](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Om du vill avbryta en körning i Studio använder du följande steg:

1. Gå till pipelinen som körs i avsnittet **experiment** eller **pipelines** . 

1. Välj det körnings nummer för pipelinen som du vill avbryta.

1. I verktygsfältet väljer du **Avbryt**

---

## <a name="create-child-runs"></a>Skapa underordnade körningar

Skapa underordnade körningar för att gruppera relaterade körningar, t. ex. för olika upprepningar i en parameter.

> [!NOTE]
> Underordnade körningar kan bara skapas med SDK: n.

I det här kod exemplet används `hello_with_children.py` skriptet för att skapa en grupp med fem underordnade körningar från en skickad körning med hjälp av [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) metoden:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> När de flyttas utanför omfånget markeras underordnade körningar automatiskt som slutförda.

Använd metoden för att skapa många underordnade körningar effektivt [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) . Eftersom varje skapande resulterar i ett nätverks anrop är det mer effektivt att skapa en batch med körningar än att skapa en i taget.

### <a name="submit-child-runs"></a>Skicka underordnade körningar

Underordnade körningar kan också skickas från en överordnad körning. På så sätt kan du skapa hierarkier med överordnade och underordnade körningar. Det går inte att skapa en överordnad underordnad körning: även om den överordnade körningen inte gör något men starta underordnade körningar, är det fortfarande nödvändigt att skapa hierarkin. Status för alla körningar är oberoende: en överordnad kan ha `"Completed"` tillståndet lyckades även om en eller flera underordnade körningar avbröts eller misslyckades.  

Du kanske vill att ditt underordnade ska köras för att använda en annan körnings konfiguration än den överordnade körningen. Till exempel kan du använda en mindre kraftfull, PROCESSORbaserade konfiguration för den överordnade, samtidigt som du använder GPU-baserade konfigurationer för dina barn. En annan vanlig önskan är att skicka alla underordnade olika argument och data. Om du vill anpassa en underordnad körning skapar du ett `ScriptRunConfig` objekt för den underordnade körningen. Följande kod gör följande:

- Hämta en beräknings resurs med namnet `"gpu-cluster"` från arbets ytan `ws`
- Itererar över olika argument värden som ska skickas till underordnade `ScriptRunConfig` objekt
- Skapar och skickar en ny underordnad körning med hjälp av den anpassade beräknings resursen och argumentet
- Block tills alla underordnade har slutförts

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Använd metoden för att skapa många underordnade körningar med identiska konfigurationer, argument och indata [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) . Eftersom varje skapande resulterar i ett nätverks anrop är det mer effektivt att skapa en batch med körningar än att skapa en i taget.

I en underordnad körning kan du Visa överordnad körnings-ID:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Frågans underordnade körningar

Använd metoden för att fråga de underordnade körningarna av en speciell överordnad [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) . Med ``recursive = True`` argumentet kan du fråga ett kapslat träd med underordnade och Grandchildren.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Logga till överordnad eller root-körning

Du kan använda `Run.parent` fältet för att komma åt körningen som startade den aktuella underordnade körningen. Ett vanligt användnings fall för detta är när du vill konsolidera logg resultat på ett och samma ställe. Observera att underordnade körs asynkront och att det inte finns någon garanti för beställning eller synkronisering utöver det överordnade objektets förmåga att vänta på att dess underordnade körningar ska slutföras.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>Exempelnotebook-filer

Följande antecknings böcker demonstrerar begreppen i den här artikeln:

* Mer information om loggnings-API: er finns i [logg-API-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Mer information om hur du hanterar körningar med Azure Machine Learning SDK finns i [antecknings boken hantera körningar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Nästa steg

* Information om hur du loggar mått för dina experiment finns i [logg mått under inlärnings körningar](how-to-track-experiments.md).
* Information om hur du övervakar resurser och loggar från Azure Machine Learning finns i [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).
