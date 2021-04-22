---
title: Spåra, övervaka och analysera körningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du startar, övervakar och spårar dina maskininlärningsexperimentkörningar med Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: af591a5defcef1e8d043d54f08447324a34a10c4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876303"
---
# <a name="start-monitor-and-track-run-history"></a>Starta, övervaka och spåra körningshistorik

I [Azure Machine Learning SDK för Python,](/python/api/overview/azure/ml/intro)Machine Learning [CLI](reference-azure-machine-learning-cli.md) [och Azure Machine Learning-studio](https://ml.azure.com) du olika metoder för att övervaka, organisera och spåra körningar för träning och experimentering. Din ML-körningshistorik är en viktig del av en förklarande och repeterbar ML-utvecklingsprocess.

Den här artikeln visar hur du utför följande uppgifter:

* Övervaka körningsprestanda.
* Skapa en anpassad vy. 
* Lägg till en körningsbeskrivning. 
* Tagga och hitta körningar.
* Kör sökning över din körningshistorik. 
* Avbryt eller misslyckas körningar.
* Skapa underordnade körningar.
* Övervaka körningsstatusen via e-postavisering.
 

> [!TIP]
> Om du letar efter information om övervakning av Azure Machine Learning Service och associerade Azure-tjänster kan du gå [till Så här övervakar du Azure Machine Learning](monitor-azure-machine-learning.md).
> Om du letar efter information om övervakningsmodeller som distribueras som webbtjänster eller IoT Edge-moduler kan du gå till Samla in [modelldata](how-to-enable-data-collection.md) och [Övervaka med Application Insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning arbetsyta](how-to-manage-workspace.md).

* Den Azure Machine Learning SDK för Python (version 1.0.21 eller senare). Information om hur du installerar eller uppdaterar till den senaste versionen av SDK finns [i Installera eller uppdatera SDK.](/python/api/overview/azure/ml/install)

    Använd följande kod för att kontrollera Azure Machine Learning SDK-versionen:

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI och](/cli/azure/?preserve-view=true&view=azure-cli-latest) [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Övervaka körningsprestanda

* Starta en körning och dess loggningsprocess

    # <a name="python"></a>[Python](#tab/python)
    
    1. Konfigurera experimentet genom att importera [klasserna Workspace](/python/api/azureml-core/azureml.core.workspace.workspace), [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29)och [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) från [paketet azureml.core.](/python/api/azureml-core/azureml.core)
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Starta en körning och dess loggningsprocess med [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metoden .
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Använd följande steg för att starta en körning av experimentet:
    
    1. Från ett gränssnitt eller en kommandotolk använder du Azure CLI för att autentisera till din Azure-prenumeration:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Bifoga en arbetsytekonfiguration till mappen som innehåller träningsskriptet. Ersätt `myworkspace` med din Azure Machine Learning arbetsyta. Ersätt `myresourcegroup` med den Azure-resursgrupp som innehåller din arbetsyta:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Det här kommandot skapar `.azureml` en underkatalog som innehåller exempel på runconfig- och conda-miljöfiler. Den innehåller också en `config.json` fil som används för att kommunicera med Azure Machine Learning arbetsyta.
    
        Mer information finns i [az ml folder attach](/cli/azure/ml/folder?preserve-view=true&view=azure-cli-latest#az_ml_folder_attach).
    
    2. Starta körningen med hjälp av följande kommando. När du använder det här kommandot anger du namnet på runconfig-filen (texten före .runconfig om du tittar på filsystemet) mot \* parametern -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > Kommandot `az ml folder attach` skapade en `.azureml` underkatalog som innehåller två runconfig-exempelfiler.
        >
        > Om du har ett Python-skript som skapar ett körningskonfigurationsobjekt programmatiskt kan du använda [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) för att spara det som en runconfig-fil.
        >
        > Fler runconfig-exempelfiler finns i [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Mer information finns i [az ml run submit-script](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Ett exempel på hur du tränar en modell i Azure Machine Learning designern [finns i Självstudie: Förutsäga bilpriser med designern](tutorial-designer-automobile-price-train-score.md).

    ---

* Övervaka status för en körning

    # <a name="python"></a>[Python](#tab/python)
    
    * Hämta status för en körning med [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) metoden .
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Om du vill hämta körnings-ID, körningstid och annan information om körningen använder du [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) metoden .
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * När körningen är klar använder du metoden [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) för att markera den som slutförd.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Om du använder Pythons `with...as` designmönster markeras körningen automatiskt som slutförd när körningen är utanför omfånget. Du behöver inte manuellt markera körningen som slutförd.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * Om du vill visa en lista över körningar för experimentet använder du följande kommando. Ersätt `experiment` med namnet på experimentet:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Det här kommandot returnerar ett JSON-dokument som innehåller information om körningar för det här experimentet.
    
        Mer information finns i [az ml experiment list](/cli/azure/ml/experiment?preserve-view=true&view=azure-cli-latest#az_ml_experiment_list).
    
    * Om du vill visa information om en specifik körning använder du följande kommando. Ersätt `runid` med ID:t för körningen:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Det här kommandot returnerar ett JSON-dokument som innehåller information om körningen.
    
        Mer information finns i [az ml run show](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    ---
## <a name="custom-view"></a>Anpassad vy 
    
Så här visar du dina körningar i Studio: 
    
1. Gå till **fliken** Experiment.
    
1. Välj **antingen Alla experiment** för att visa alla körningar i ett experiment eller välj **Alla körningar** för att visa alla körningar som skickats i arbetsytan.
    
På sidan **Alla körningar kan** du filtrera körningslistan efter taggar, experiment, beräkningsmål med mera för att bättre organisera och begränsa ditt arbete.  
    
1. Gör anpassningar på sidan genom att välja körningar för att jämföra, lägga till diagram eller tillämpa filter. Dessa ändringar kan sparas som en anpassad **vy så** att du enkelt kan återgå till ditt arbete. Användare med arbetsytebehörigheter kan redigera eller visa den anpassade vyn. Dela även den anpassade vyn med gruppmedlemmar för förbättrat samarbete genom att välja **Dela vy.**   
    
:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views.gif" alt-text="Skärmbild: Skapa en anpassad vy":::
    
1. Om du vill visa körningsloggarna väljer du en specifik körning. På fliken Utdata **+** loggar hittar du diagnostik- och felloggar för körningen.

## <a name="run-description"></a>Körningsbeskrivning 

En körningsbeskrivning kan läggas till i en körning för att ge körningen mer kontext och information. Du kan också söka efter dessa beskrivningar från körningslistan och lägga till körningsbeskrivningen som en kolumn i körningslistan. 

Gå till sidan **Körningsinformation** för körningen och välj redigerings- eller pennikonen för att lägga till, redigera eller ta bort beskrivningar för körningen. Spara ändringarna i körningslistan genom att spara ändringarna i din befintliga anpassade vy eller en ny anpassad vy. Markdown-format stöds för körningsbeskrivningar, vilket gör att bilder kan bäddas in och djuplänkas enligt nedan.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description.gif" alt-text="Skärmbild: Skapa en körningsbeskrivning"::: 

## <a name="tag-and-find-runs"></a>Tagga och hitta körningar

I Azure Machine Learning kan du använda egenskaper och taggar för att organisera och fråga körningar efter viktig information.

* Lägga till egenskaper och taggar

    # <a name="python"></a>[Python](#tab/python)
    
    Om du vill lägga till sökbara metadata i dina körningar använder du [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) metoden . Följande kod lägger till exempel till `"author"` egenskapen i körningen:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Egenskaper är oföränderliga, så de skapar en permanent post i granskningssyfte. Följande kodexempel resulterar i ett fel, eftersom vi redan `"azureml-user"` har lagt till som `"author"` egenskapsvärde i föregående kod:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Till skillnad från egenskaper är taggar föränderliga. Om du vill lägga till sökbar och meningsfull information för användare av experimentet använder du [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) metoden .
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Du kan också lägga till enkla strängtaggar. När de här taggarna visas i taggordlistan som nycklar har de värdet `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > Med HJÄLP av CLI kan du bara lägga till eller uppdatera taggar.
    
    Om du vill lägga till eller uppdatera en tagg använder du följande kommando:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Mer information finns i [az ml run update](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Du kan lägga till, redigera eller ta bort körningstaggar från Studio. Gå till sidan **Körningsinformation** för körningen och välj redigerings- eller pennikonen för att lägga till, redigera eller ta bort taggar för dina körningar. Du kan också söka efter och filtrera på dessa taggar från sidan med körningslistan.
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="Skärmbild: Lägga till, redigera eller ta bort körningstaggar":::
    
    ---

* Frågeegenskaper och taggar

    Du kan köra frågor i ett experiment för att returnera en lista över körningar som matchar specifika egenskaper och taggar.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI stöder [JMESPath-frågor,](http://jmespath.org) som kan användas för att filtrera körningar baserat på egenskaper och taggar. Om du vill använda en JMESPath-fråga med Azure CLI anger du den med `--query` parametern . I följande exempel visas några frågor med hjälp av egenskaper och taggar:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Mer information om hur du kör frågor mot Azure CLI-resultat finns i [Fråga Azure CLI-kommandoutdata.](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Om du vill söka efter specifika körningar går du  **till listan Alla** körningar. Därifrån har du två alternativ:
    
    1. Använd knappen **Lägg till filter** och välj filter på taggar för att filtrera körningarna efter tagg som har tilldelats körningarna. <br><br>
    ELLER
    
    1. Använd sökfältet för att snabbt hitta körningar genom att söka efter körningsmetadata som körningsstatus, beskrivningar, experimentnamn och submitter-namn. 
    
## <a name="cancel-or-fail-runs"></a>Avbryta eller misslyckas körningar

Om du upptäcker ett misstag eller om körningen tar för lång tid att slutföras kan du avbryta körningen.

# <a name="python"></a>[Python](#tab/python)

Om du vill avbryta en körning med hjälp av SDK:n använder du [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) metoden :

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Om körningen är klar, men den innehåller ett fel (till exempel att det felaktiga träningsskriptet har använts), kan du använda metoden för [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) att markera den som misslyckad.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill avbryta en körning med hjälp av CLI använder du följande kommando. Ersätt `runid` med ID:t för körningen

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Mer information finns i [az ml run cancel](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Så här avbryter du en körning i Studio:

1. Gå till pipelinen som körs i **avsnittet Experiment** **eller Pipelines.** 

1. Välj det pipelinekörningsnummer som du vill avbryta.

1. I verktygsfältet väljer du **Avbryt**

---

## <a name="create-child-runs"></a>Skapa underordnade körningar

Skapa underordnade körningar för att gruppera relaterade körningar, till exempel för olika iterationer av hyperparametrar.

> [!NOTE]
> Underordnade körningar kan bara skapas med hjälp av SDK: n.

Det här kodexeskriptet använder skriptet för att skapa en batch med `hello_with_children.py` fem underordnade körningar inifrån en skickad körning med hjälp av [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) metoden :

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

Om du vill skapa många underordnade körningar effektivt använder du [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metoden . Eftersom varje skapande resulterar i ett nätverks-anrop är det effektivare att skapa en batch med körningar än att skapa dem en i stället för en.

### <a name="submit-child-runs"></a>Skicka underordnade körningar

Underordnade körningar kan också skickas från en överordnad körning. På så sätt kan du skapa hierarkier med överordnade och underordnade körningar. Du kan inte skapa en överordnad underordnad körning: även om den överordnade körningen inte gör något, men startar underordnade körningar, är det fortfarande nödvändigt att skapa hierarkin. Statusen för alla körningar är oberoende: en överordnad kan vara i ett lyckat tillstånd även om en eller flera underordnade körningar `"Completed"` avbröts eller misslyckades.  

Du kanske vill att dina underordnade körningar ska använda en annan körningskonfiguration än den överordnade körningen. Du kan till exempel använda en mindre kraftfull, CPU-baserad konfiguration för den överordnade, när du använder GPU-baserade konfigurationer för dina underordnade konfigurationer. Ett annat vanligt önskemål är att skicka varje underordnad argument och data. Om du vill anpassa en underordnad körning skapar du `ScriptRunConfig` ett -objekt för den underordnade körningen. 

> [!IMPORTANT]
> Om du vill skicka en underordnad körning från en överordnad körning på en fjärrbearbetning måste du först logga in på arbetsytan i den överordnade körningskoden. Som standard har inte körningskontextobjektet i en fjärrkörning autentiseringsuppgifter för att skicka underordnade körningar. Använd tjänstens huvudnamn eller autentiseringsuppgifter för hanterad identitet för att logga in. Mer information om autentisering finns i [Konfigurera autentisering.](how-to-setup-authentication.md)

Koden nedan:

- Hämtar en beräkningsresurs med `"gpu-cluster"` namnet från arbetsytan `ws`
- Itererar över olika argumentvärden som ska skickas till underordnade `ScriptRunConfig` objekt
- Skapar och skickar en ny underordnad körning med hjälp av den anpassade beräkningsresursen och argumentet
- Blockerar tills alla underordnade körningar har slutförts

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

Om du vill skapa många underordnade körningar med identiska konfigurationer, argument och indata effektivt använder du [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metoden . Eftersom varje skapande resulterar i ett nätverkssamtal är det effektivare att skapa en batch med körningar än att skapa dem en i stället för en.

I en underordnad körning kan du visa det överordnade körnings-ID:t:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Köra frågor mot underordnade körningar

Om du vill fråga efter underordnade körningar av en specifik överordnad använder du [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metoden . Med ``recursive = True`` argumentet kan du fråga ett kapslat träd med barn och grandchildren.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Logga in på överordnad körning eller rotkörning

Du kan använda fältet `Run.parent` för att komma åt körningen som startade den aktuella underordnade körningen. Ett vanligt användningsfall för användning `Run.parent` är att kombinera loggresultat på en enda plats. Underordnade körningar körs asynkront och det finns ingen garanti för ordning eller synkronisering utöver den överordnade körningens möjlighet att vänta tills dess underordnade körningar har slutförts.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>Övervaka körningsstatusen via e-postavisering

1. I det [Azure Portal](https://ms.portal.azure.com/)navigeringsfältet väljer du fliken Övervaka i **det vänstra navigeringsfältet.** 

1. Välj **Diagnostikinställningar och** sedan + Lägg **till diagnostikinställning.**

    ![Skärmbild av diagnostikinställningar för e-postaviseringar](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. I diagnostikinställningen 
    1. under **Kategoriinformation väljer** du **AmlRunStatusChangedEvent**. 
    1. I **Målinformation väljer** du arbetsytan **Skicka till Log Analytics och**  anger **prenumerationen och** Log **Analytics-arbetsytan**. 

    > [!NOTE]
    > **Azure Log Analytics-arbetsytan** är en annan typ av Azure-resurs än **Azure Machine Learning Service arbetsyta.** Om det inte finns några alternativ i listan kan du skapa [en Log Analytics-arbetsyta.](../azure-monitor/logs/quick-create-workspace.md) 
    
    ![Var du vill spara e-postaviseringar](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. På fliken **Loggar** lägger du till en **Ny aviseringsregel**. 

    ![Ny aviseringsregel](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. Se [hur du skapar och hanterar logga aviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-log.md).

## <a name="example-notebooks"></a>Exempelnotebook-filer

Följande anteckningsböcker visar begreppen i den här artikeln:

* Mer information om loggnings-API:er finns i [loggnings-API-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Mer information om hur du hanterar körningar med Azure Machine Learning SDK finns i anteckningsboken [manage runs](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Nästa steg

* Information om hur du loggar mått för dina experiment finns i [Logga mått under träningskörningar.](how-to-log-view-metrics.md)
* Information om hur du övervakar resurser och loggar från Azure Machine Learning finns [i Övervaka Azure Machine Learning](monitor-azure-machine-learning.md).