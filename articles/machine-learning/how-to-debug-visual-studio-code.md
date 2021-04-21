---
title: Interaktiv felsökning med Visual Studio Code
titleSuffix: Azure Machine Learning
description: Felsöka Azure Machine Learning kod, pipelines och distributioner interaktivt med hjälp Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 69a69afedbd86871987a8e62b55dfc070121cc78
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813874"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interaktiv felsökning med Visual Studio Code



Lär dig hur du interaktivt felsöker Azure Machine Learning, pipelines och distributioner med hjälp Visual Studio Code (VS Code) [och debugpy](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Köra och felsöka experiment lokalt

Använd Azure Machine Learning för att verifiera, köra och felsöka dina maskininlärningsexperiment innan du skickar dem till molnet.

### <a name="prerequisites"></a>Förutsättningar

* Azure Machine Learning VS Code-tillägg (förhandsversion). Mer information finns i [Konfigurera Azure Machine Learning VS Code-tillägget](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop för Mac och Windows
  * Docker Engine för Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> Se till att konfigurera [Docker för att använda Linux-containrar i Windows.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

> [!TIP]
> För Windows, även om det inte krävs, rekommenderar vi starkt att du använder [Docker med Windows-undersystem för Linux (WSL) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> Kontrollera att Docker körs innan du kör experimentet lokalt.

### <a name="debug-experiment-locally"></a>Felsöka experiment lokalt

1. I VS Code öppnar du Azure Machine Learning tilläggsvyn.
1. Expandera prenumerationsnoden som innehåller din arbetsyta. Om du inte redan har en kan du skapa en [Azure Machine Learning med](how-to-manage-resources-vscode.md#create-a-workspace) hjälp av tillägget.
1. Expandera noden för arbetsytan.
1. Högerklicka på noden **Experiment** och välj **Skapa experiment**. När frågan visas anger du ett namn för experimentet.
1. Expandera **noden Experiment,** högerklicka på det experiment som du vill köra och välj **Kör experiment.**
1. I listan med alternativ för att köra experimentet väljer du **Lokalt.**
1. **Använd endast första gången på Windows.** När du uppmanas att tillåta filresursen väljer du **Ja.** När du aktiverar filresursen kan Docker montera katalogen som innehåller skriptet i containern. Dessutom kan Docker lagra loggar och utdata från körningen i en tillfällig katalog i systemet.
1. Välj **Ja** för att felsöka experimentet. Annars väljer du **Nej**. Om du väljer Nej körs experimentet lokalt utan att ansluta till felsökningsprogrammet.
1. Välj **Skapa ny körningskonfiguration** för att skapa körningskonfigurationen. Körningskonfigurationen definierar det skript som du vill köra, beroenden och datauppsättningar som används. Om du redan har en kan du välja den i listrutan.
    1. Välj din miljö. Du kan välja något av de här [Azure Machine Learning eller](resource-curated-environments.md) skapa en egen.
    1. Ange namnet på det skript som du vill köra. Sökvägen är relativ till katalogen som öppnades i VS Code.
    1. Välj om du vill använda en Azure Machine Learning datauppsättning eller inte. Du kan skapa [Azure Machine Learning datauppsättningar med](how-to-manage-resources-vscode.md#create-dataset) hjälp av tillägget .
    1. Felsökning krävs för att koppla felsökningsprogrammet till containern som kör experimentet. Om du vill lägga till debugpy som ett beroende väljer **du Lägg till debugpy**. Annars väljer du **Hoppa över**. Om du inte lägger till debugpy som ett beroende körs experimentet utan att det ansluts till felsökningsprogrammet.
    1. En konfigurationsfil som innehåller körningskonfigurationsinställningarna öppnas i redigeraren. Om du är nöjd med inställningarna väljer du **Skicka experiment**. Du kan också öppna kommandopaletten (**Visa > Kommandopalett)** från menyraden och ange `Azure ML: Submit experiment` kommandot i textrutan.
1. När experimentet har skickats skapas en Docker-avbildning som innehåller skriptet och de konfigurationer som anges i körningskonfigurationen.

    När Docker-avbildningsbyggprocessen börjar strömmas innehållet `60_control_log.txt` i filströmmen till utdatakonsolen i VS Code.

    > [!NOTE]
    > Första gången Docker-avbildningen skapas kan det ta flera minuter.

1. När avbildningen har skapats visas en uppmaning om att starta felsökningsprogrammet. Ange brytpunkter i skriptet och välj **Starta felsökning** när du är redo att börja felsöka. Då kopplas VS Code-felsökaren till containern som kör experimentet. Du kan också hovra Azure Machine Learning över noden för den aktuella körningen i Azure Machine Learning och välja uppspelningsikonen för att starta felsökningsprogrammet.

    > [!IMPORTANT]
    > Du kan inte ha flera felsökningssessioner för ett enda experiment. Du kan dock felsöka två eller flera experiment med hjälp av flera VS Code-instanser.

Nu bör du kunna gå igenom och felsöka koden med hjälp av VS Code.

Om du vill avbryta körningen högerklickar du på körningsnoden och väljer **Avbryt körning.**

På liknande sätt som med fjärrexperimentkörningar kan du expandera din körningsnod för att granska loggarna och utdata.

> [!TIP]
> Docker-avbildningar som använder samma beroenden som definierats i din miljö återanvänds mellan körningar. Men om du kör ett experiment med en ny eller annan miljö skapas en ny avbildning. Eftersom dessa avbildningar sparas i din lokala lagring rekommenderar vi att du tar bort gamla eller oanvända Docker-avbildningar. Om du vill ta bort avbildningar från systemet använder [du Docker CLI](https://docs.docker.com/engine/reference/commandline/rmi/) eller [VS Code Docker-tillägget](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Felsöka pipelines för maskininlärning

I vissa fall kan du behöva felsöka Python-koden som används i ML-pipelinen interaktivt. Genom att använda VS Code och felsökningspy kan du koppla till koden när den körs i träningsmiljön.

### <a name="prerequisites"></a>Förutsättningar

* En __Azure Machine Learning arbetsyta__ som är konfigurerad för att använda __en Azure-Virtual Network__.
* En __Azure Machine Learning-pipeline__ som använder Python-skript som en del av pipelinestegen. Till exempel PythonScriptStep.
* Ett Azure Machine Learning Compute-kluster, som __finns i det virtuella nätverket__ och som används av __pipelinen för träning .__
* En __utvecklingsmiljö__ som finns __i det virtuella nätverket__. Utvecklingsmiljön kan vara något av följande:

  * En virtuell Azure-dator i det virtuella nätverket
  * En beräkningsinstans av en virtuell notebook-dator i det virtuella nätverket
  * En klientdator som har privat nätverksanslutning till det virtuella nätverket, antingen via VPN eller ExpressRoute.

Mer information om hur du använder en Azure Virtual Network med Azure Machine Learning finns i Översikt [över isolering och sekretess för virtuella nätverk.](how-to-network-security-overview.md)

> [!TIP]
> Även om du kan arbeta Azure Machine Learning resurser som inte finns bakom ett virtuellt nätverk rekommenderas det att du använder ett virtuellt nätverk.

### <a name="how-it-works"></a>Så här fungerar det

Dina ML-pipelinesteg kör Python-skript. De här skripten ändras för att utföra följande åtgärder:

1. Logga IP-adressen för den värd som de körs på. Du använder IP-adressen för att ansluta felsökaren till skriptet.

2. Starta felsökningskomponenten debugpy och vänta tills en felsökare ansluter.

3. Från utvecklingsmiljön övervakar du loggarna som skapats av träningsprocessen för att hitta IP-adressen där skriptet körs.

4. Du talar om för VS Code ip-adressen att ansluta felsökaren till med hjälp av en `launch.json` -fil.

5. Du kopplar felsökaren och går interaktivt igenom skriptet.

### <a name="configure-python-scripts"></a>Konfigurera Python-skript

Om du vill aktivera felsökning gör du följande ändringar i Python-skripten som används av stegen i ML-pipelinen:

1. Lägg till följande importutdrag:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Lägg till följande argument. Med de här argumenten kan du aktivera felsökaren efter behov och ange tidsgränsen för att koppla felsökningsprogrammet:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Lägg till följande -instruktioner. De här isatserna läser in den aktuella körningskontexten så att du kan logga IP-adressen för noden som koden körs på:

    ```python
    global run
    run = Run.get_context()
    ```

1. Lägg till `if` en -instruktion som startar debugpy och väntar på att ett felsökningsprogrammet ska ansluta. Om inget felsökningsprogrammet kopplas före tidsgränsen fortsätter skriptet som vanligt. Se till att ersätta `HOST` värdena och är funktionen med dina `PORT` `listen` egna.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

I följande Python-exempel visas `train.py` en enkel fil som möjliggör felsökning:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurera ML-pipeline

Om du vill tillhandahålla de Python-paket som behövs för att starta debugpy och hämta körningskontexten skapar du en miljö och anger `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Ändra SDK-versionen så att den matchar den som du använder. Följande kodfragment visar hur du skapar en miljö:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

I avsnittet [Konfigurera Python-skript har](#configure-python-scripts) nya argument lagts till i skripten som används av dina ML-pipelinesteg. Följande kodfragment visar hur du använder dessa argument för att aktivera felsökning för komponenten och ange en tidsgräns. Den visar också hur du använder miljön som skapades tidigare genom att ange `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

När pipelinen körs skapar varje steg en underordnad körning. Om felsökning är aktiverat loggar det ändrade skriptet information som liknar följande text i `70_driver_log.txt` för den underordnade körningen:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Spara `ip_address` värdet. Det används i nästa avsnitt.

> [!TIP]
> Du kan också hitta IP-adressen från körningsloggarna för den underordnade körningen för det här pipelinesteget. Mer information om hur du visar den här informationen finns [i Övervaka Azure ML-experimentkörningar och mått.](how-to-log-view-metrics.md)

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Om du vill installera debugpy i VS Code-utvecklingsmiljön använder du följande kommando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Mer information om hur du använder felsökning med VS Code finns i [Fjärrfelsökning.](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)

1. Skapa en ny felsökningskonfiguration för att konfigurera VS Code Azure Machine Learning med den beräkning som kör felsökningsprogrammet:

    1. I VS Code väljer du __menyn Felsök__ och sedan __Öppna konfigurationer.__ En fil __medlaunch.jspå__ öppnas.

    1. Leta reda __launch.js__ rad som innehåller i filen och `"configurations": [` infoga följande text efter den. Ändra posten `"host": "<IP-ADDRESS>"` till den IP-adress som returnerades i loggarna från föregående avsnitt. Ändra posten `"localRoot": "${workspaceFolder}/code/step"` till en lokal katalog som innehåller en kopia av skriptet som felsöks:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Om det redan finns andra poster i konfigurationsavsnittet lägger du till ett kommatecken (,) efter den kod som du infogade.

        > [!TIP]
        > Det bästa sättet, särskilt för pipelines, är att behålla resurserna för skript i separata kataloger så att koden endast är relevant för varje steg. I det här exemplet `localRoot` refererar exempelvärdet till `/code/step1` .
        >
        > Om du felsöker flera skript i olika kataloger skapar du ett separat konfigurationsavsnitt för varje skript.

    1. Spara __launch.jsfilen.__

### <a name="connect-the-debugger"></a>Ansluta felsökningsprogrammet

1. Öppna VS Code och öppna en lokal kopia av skriptet.
2. Ange brytpunkter där du vill att skriptet ska stoppas när du har kopplat det.
3. När den underordnade processen kör skriptet och visas i loggarna använder du `Timeout for debug connection` tangenten F5 eller väljer __Felsök.__ När du uppmanas väljer du __Azure Machine Learning Compute: fjärrfelsökningskonfiguration.__ Du kan också välja felsökningsikonen i sidofältet, __Azure Machine Learning:__ fjärrfelsökningsposten från listrutan Felsök och sedan använda den gröna pilen för att koppla felsökaren.

    I det här läget ansluter VS Code till felsökningspyen på beräkningsnoden och stoppas vid den brytpunkt som du konfigurerade tidigare. Nu kan du stega igenom koden när den körs, visa variabler osv.

    > [!NOTE]
    > Om loggen visar en post som anger har tidsgränsen gått ut och `Debugger attached = False` skriptet har fortsatt utan felsökningsprogrammet. Skicka pipelinen igen och anslut felsökaren efter `Timeout for debug connection` meddelandet och innan tidsgränsen går ut.

## <a name="debug-and-troubleshoot-deployments"></a>Felsöka distributioner

I vissa fall kan du behöva felsöka Python-koden interaktivt i modelldistributionen. Om till exempel inmatningsskriptet misslyckas och orsaken inte kan fastställas av ytterligare loggning. Genom att använda VS Code och felsökningspyen kan du ansluta till koden som körs i Docker-containern.

> [!IMPORTANT]
> Den här felsökningsmetoden fungerar inte när du använder och `Model.deploy()` `LocalWebservice.deploy_configuration` distribuerar en modell lokalt. I stället måste du skapa en avbildning med [hjälp av metoden Model.package().](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Distribution av lokala webbtjänster kräver en fungerande Docker-installation i det lokala systemet. Mer information om hur du använder Docker finns i [Docker-dokumentationen.](https://docs.docker.com/) Observera att docker redan är installerat när du arbetar med beräkningsinstanser.

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Om du vill installera debugpy i din lokala VS Code-utvecklingsmiljö använder du följande kommando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Mer information om hur du använder felsökning med VS Code finns [i Fjärrfelsökning.](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)

1. Om du vill konfigurera VS Code för att kommunicera med Docker-avbildningen skapar du en ny felsökningskonfiguration:

    1. Från VS Code väljer du __menyn Felsök__ i __kör-utökningen__ och väljer __sedan Öppna konfigurationer.__ En fil __medlaunch.jspå__ öppnas.

    1. Leta reda __launch.js__ __"konfigurationer"__ (raden som innehåller ) i filen och `"configurations": [` infoga följande text efter det. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        Efter __infogning börlaunch.js__ på filen se ut ungefär så här:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Om det redan finns andra poster i konfigurationsavsnittet lägger du till ett kommatecken ( __,__ ) efter koden som du infogade.

        Det här avsnittet ansluter till Docker-containern med port __5678__.

    1. Spara __launch.jsfilen.__

### <a name="create-an-image-that-includes-debugpy"></a>Skapa en avbildning som innehåller felsökning

1. Ändra conda-miljön för distributionen så att den innehåller felsökning. I följande exempel visas hur du lägger till den med `pip_packages` parametern :

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Om du vill starta debugpy och vänta på en anslutning när tjänsten startar lägger du till följande överst i `score.py` filen:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Skapa en avbildning baserat på miljödefinitionen och hämta avbildningen till det lokala registret. 

    > [!NOTE]
    > Det här exemplet `ws` förutsätter att pekar på Azure Machine Learning arbetsyta och `model` det är den modell som distribueras. Filen `myenv.yml` innehåller conda-beroendena som skapades i steg 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    När avbildningen har skapats och laddats ned (den här processen kan ta mer än 10 minuter, så vänta lite), visas avbildningssökvägen (inklusive lagringsplats, namn och tagg, som i det här fallet även är dess sammanfattning) slutligen i ett meddelande som liknar följande:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Om du vill göra det enklare att arbeta med avbildningen lokalt kan du använda följande kommando för att lägga till en tagg för avbildningen. Ersätt `myimagepath` i följande kommando med platsvärdet från föregående steg.

    ```bash
    docker tag myimagepath debug:1
    ```

    För resten av stegen kan du referera till den lokala avbildningen som i `debug:1` stället för värdet för den fullständiga avbildningssökvägen.

### <a name="debug-the-service"></a>Felsöka tjänsten

> [!TIP]
> Om du anger en tidsgräns för felsökningsanslutningen i filen måste du ansluta VS Code till felsökningssessionen innan `score.py` tidsgränsen går ut. Starta VS Code, öppna den lokala kopian av , ange en brytpunkt och gör den redo att gå innan du `score.py` använder stegen i det här avsnittet.
>
> Mer information om felsökning och inställning av brytpunkter finns [i Felsöka](https://code.visualstudio.com/Docs/editor/debugging).

1. Om du vill starta en Docker-container med avbildningen använder du följande kommando:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Detta kopplar `score.py` din lokalt till den i containern. Därför återspeglas alla ändringar som görs i redigeraren automatiskt i containern

2. För en bättre upplevelse kan du gå till containern med ett nytt VS-kodgränssnitt. Välj `Docker` utökningen från VS Code-sidofältet, leta reda på den lokala container som skapats. I den här dokumentationen är det `debug:1` . Högerklicka på den här containern och välj , så öppnas ett nytt VS Code-gränssnitt automatiskt och det här gränssnittet visar `"Attach Visual Studio Code"` inuti den skapade containern.

    ![VS Code-gränssnittet för containern](./media/how-to-troubleshoot-deployment/container-interface.png)

3. I containern kör du följande kommando i gränssnittet

    ```bash
    runsvdir /var/runit
    ```
    Sedan kan du se följande utdata i gränssnittet i containern:

    ![Utdata från containerkörningskonsolen](./media/how-to-troubleshoot-deployment/container-run.png)

4. Om du vill koppla VS Code till felsökningspyen i containern öppnar du VS Code och använder F5-nyckeln eller väljer __Felsök.__ När du uppmanas väljer du __Azure Machine Learning Distribution: Docker Debug configuration (Docker-felsökningskonfiguration).__ Du kan också  välja körningsutökningsikonen i sidofältet, __posten Azure Machine Learning Deployment: Docker Debug (Azure Machine Learning Distribution: Docker Debug)__ i listrutan Felsök och sedan använda den gröna pilen för att koppla felsökaren.

    ![Felsökningsikonen, knappen Starta felsökning och konfigurationsväljaren](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    När du har klickat på den gröna pilen och bifogat felsökaren kan du se ny information i VS Code-gränssnittet för containern:
    
    ![Bifogad information om containerfelsökningsprogrammet](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    I huvudgränssnittet för VS Code kan du se följande:

    ![VS Code-brytpunkten i score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

Och nu har den lokala `score.py` som är kopplad till containern redan stoppats vid brytpunkterna där du anger. Nu ansluter VS Code till debugpy i Docker-containern och stoppar Docker-containern vid den brytpunkt som du konfigurerade tidigare. Nu kan du stega igenom koden när den körs, visa variabler osv.

Mer information om hur du använder VS Code för att felsöka Python finns [i Felsöka Python-koden.](https://code.visualstudio.com/docs/python/debugging)

### <a name="stop-the-container"></a>Stoppa containern

Om du vill stoppa containern använder du följande kommando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Nästa steg

Nu när du har ställt in VS Code Remote kan du använda en beräkningsinstans som fjärrbearbetning från VS Code för att interaktivt felsöka koden. 

Läs mer om felsökning:

* [Distribution av lokal modell](how-to-troubleshoot-deployment-local.md)
* [Fjärrmodellsdistribution](how-to-troubleshoot-deployment.md)
* [Pipelines för maskininlärning](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

