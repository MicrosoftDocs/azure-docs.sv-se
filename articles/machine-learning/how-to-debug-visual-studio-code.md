---
title: Interaktiv fel sökning med Visual Studio Code
titleSuffix: Azure Machine Learning
description: Felsöka Azure Machine Learning kod, pipelines och distributioner interaktivt med Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: e572f1f6a9452ccab9deddb62a5e219a81df5d47
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740002"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interaktiv fel sökning med Visual Studio Code



Lär dig att interaktivt felsöka Azure Machine Learning experiment, pipelines och distributioner med Visual Studio Code (VS Code) och [debugpy](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Kör och Felsök experiment lokalt

Använd Azure Machine Learning-tillägget för att verifiera, köra och felsöka Machine Learning-experiment innan du skickar dem till molnet.

### <a name="prerequisites"></a>Förutsättningar

* Azure Machine Learning VS Code Extension (för hands version). Mer information finns i [konfigurera Azure Machine Learning vs Code Extension](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop för Mac och Windows
  * Docker-motor för Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> I Windows, se till att [Konfigurera Docker att använda Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> För Windows, även om det inte krävs, rekommenderar vi starkt att du [använder Docker med Windows-undersystem för Linux (Wsl) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> Innan du kör ditt experiment lokalt ska du kontrol lera att Docker körs.

### <a name="debug-experiment-locally"></a>Felsök experiment lokalt

1. I VS Code öppnar du vyn Azure Machine Learning tillägg.
1. Expandera noden prenumeration som innehåller din arbets yta. Om du inte redan har en, kan du [skapa en Azure Machine Learning-arbetsyta](how-to-manage-resources-vscode.md#create-a-workspace) med hjälp av tillägget.
1. Expandera noden arbets yta.
1. Högerklicka på noden **experiment** och välj **Skapa experiment**. När meddelandet visas anger du ett namn på experimentet.
1. Expandera noden **experiment** , högerklicka på det experiment som du vill köra och välj **Kör experiment**.
1. I listan med alternativ för att köra experimentet väljer du **lokalt**.
1. **Första gången används endast i Windows**. När du uppmanas att tillåta fil resurs väljer du **Ja**. När du aktiverar fil resurs tillåter Docker Docker att montera katalogen som innehåller ditt skript till behållaren. Dessutom tillåter det också Docker att lagra loggar och utdata från din körning i en tillfällig katalog i systemet.
1. Välj **Ja** om du vill felsöka experimentet. Annars väljer du **Nej**. Om du väljer Nej körs ditt experiment lokalt utan att du ansluter till fel söknings programmet.
1. Välj **Skapa ny körnings konfiguration** för att skapa din körnings konfiguration. Körnings konfigurationen definierar det skript som du vill köra, beroenden och data uppsättningar som används. Alternativt, om du redan har en, väljer du den i list rutan.
    1. Välj din miljö. Du kan välja någon av de [Azure Machine Learning som granskats](resource-curated-environments.md) eller skapa en egen.
    1. Ange namnet på skriptet som du vill köra. Sökvägen är relativ i förhållande till den katalog som öppnats i VS Code.
    1. Välj om du vill använda en Azure Machine Learning data uppsättning eller inte. Du kan skapa [Azure Machine Learning data uppsättningar](how-to-manage-resources-vscode.md#create-dataset) med hjälp av tillägget.
    1. Debugpy krävs för att du ska kunna koppla fel sökaren till behållaren och köra experimentet. Om du vill lägga till debugpy som ett beroende väljer du **Lägg till debugpy**. Annars väljer du **hoppa över**. Om du inte lägger till debugpy som ett beroende körs experimentet utan att kopplas till fel söknings programmet.
    1. En konfigurations fil som innehåller dina konfigurations inställningar för körning öppnas i redigeraren. Om du är nöjd med inställningarna väljer du **Skicka experiment**. Du kan också öppna kommando paletten (**visa > kommando paletten**) från meny raden och ange `Azure ML: Submit experiment` kommandot i text rutan.
1. När experimentet har skickats skapas en Docker-avbildning som innehåller ditt skript och de konfigurationer som anges i körnings konfigurationen.

    När Bygg processen för Docker-avbildningen börjar börjar innehållet i `60_control_log.txt` fil data strömmen till konsolen utdata i vs Code.

    > [!NOTE]
    > Första gången som Docker-avbildningen skapas kan ta flera minuter.

1. När avbildningen har skapats visas ett meddelande om att starta fel söknings programmet. Ställ in dina Bryt punkter i skriptet och välj **Starta fel sökning** när du är redo att starta fel sökningen. Detta kopplar VS Code-felsökaren till behållaren som kör experimentet. Du kan också hovra över noden för din aktuella körning i Azure Machine Learning-tillägget och välja ikonen spela upp för att starta fel söknings programmet.

    > [!IMPORTANT]
    > Du kan inte ha flera debug-sessioner för ett enda experiment. Du kan dock felsöka två eller fler experiment med flera VS Code-instanser.

Nu bör du kunna stega igenom och felsöka koden med VS Code.

Om du vill avbryta körningen högerklickar du på noden kör och väljer **Avbryt körning**.

Precis som fjärrexperiment körs kan du expandera noden kör för att kontrol lera loggar och utdata.

> [!TIP]
> Docker-avbildningar som använder samma beroenden som definierats i din miljö återanvänds mellan körningar. Men om du kör ett experiment med en ny eller en annan miljö skapas en ny avbildning. Eftersom dessa bilder sparas i din lokala lagring rekommenderas du att ta bort gamla Docker-avbildningar. Om du vill ta bort avbildningar från systemet använder du [Docker-tillägget Docker CLI](https://docs.docker.com/engine/reference/commandline/rmi/) eller [vs Code Docker](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Felsöka pipelines för maskininlärning

I vissa fall kan du behöva interaktivt felsöka python-koden som används i ML-pipeline. Genom att använda VS Code och debugpy kan du ansluta till koden när den körs i tränings miljön.

### <a name="prerequisites"></a>Förutsättningar

* En __Azure Machine Learning arbets yta__ som har kon figurer ATS för att använda en __Azure-Virtual Network__.
* En __Azure Machine Learning pipeline__ som använder Python-skript som en del av stegen i pipelinen. Till exempel en PythonScriptStep.
* Ett Azure Machine Learning beräknings kluster, som finns __i det virtuella nätverket__ och __används av pipelinen för utbildning__.
* En __utvecklings miljö__ som finns __i det virtuella nätverket__. Utvecklings miljön kan vara något av följande:

  * En virtuell Azure-dator i det virtuella nätverket
  * En beräknings instans av den virtuella Notebook-datorn i det virtuella nätverket
  * En klient dator som har privat nätverks anslutning till det virtuella nätverket, antingen via VPN eller via ExpressRoute.

Mer information om hur du använder en Azure-Virtual Network med Azure Machine Learning finns i [Översikt över virtuell nätverks isolering och sekretess](how-to-network-security-overview.md).

> [!TIP]
> Även om du kan arbeta med Azure Machine Learning resurser som inte ligger bakom ett virtuellt nätverk rekommenderas du att använda ett virtuellt nätverk.

### <a name="how-it-works"></a>Så här fungerar det

Dina steg i ML-pipeline kör Python-skript. Dessa skript ändras för att utföra följande åtgärder:

1. Logga IP-adressen för den värd som de körs på. Du använder IP-adressen för att ansluta fel sökaren till skriptet.

2. Starta debugpy debug-komponenten och vänta tills en fel sökare ansluter.

3. I utvecklings miljön övervakar du de loggar som skapats av inlärnings processen för att hitta IP-adressen där skriptet körs.

4. Du anger VS-kod för IP-adressen för att ansluta fel sökaren till med hjälp av en `launch.json` fil.

5. Du kopplar fel söknings programmet och interaktivt steg genom skriptet.

### <a name="configure-python-scripts"></a>Konfigurera Python-skript

Om du vill aktivera fel sökning gör du följande ändringar i python-skripten som används av stegen i ML-pipeline:

1. Lägg till följande import uttryck:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Lägg till följande argument. Med de här argumenten kan du aktivera fel söknings programmet vid behov och ange tids gränsen för att koppla fel söknings programmet:

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

1. Lägg till följande-instruktioner. Dessa instruktioner läser in den aktuella körnings kontexten så att du kan logga IP-adressen för noden som koden körs på:

    ```python
    global run
    run = Run.get_context()
    ```

1. Lägg till en `if` instruktion som startar debugpy och väntar på att ett fel söknings program ska bifogas. Om ingen fel sökare bifogas före tids gränsen fortsätter skriptet som normalt. Se till att ersätta `HOST` värdena och `PORT` är `listen` funktionen med dina egna.

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

Följande python-exempel visar en enkel `train.py` fil som aktiverar fel sökning:

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

Om du vill tillhandahålla python-paket som krävs för att starta debugpy och hämta körnings kontexten skapar du en miljö och anger `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Ändra SDK-versionen så att den matchar den som du använder. Följande kodfragment visar hur du skapar en miljö:

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

I avsnittet [Konfigurera Python-skript](#configure-python-scripts) har nya argument lagts till i de skript som används av dina ml-pipeline-steg. Följande kodfragment visar hur du använder dessa argument för att aktivera fel sökning för komponenten och ange en tids gräns. Den visar också hur du använder miljön som skapats tidigare genom att ställa in `runconfig=run_config` :

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

När pipelinen körs skapar varje steg en underordnad körning. Om fel sökning är aktiverat loggar det ändrade skriptet information som liknar följande text i `70_driver_log.txt` för den underordnade körningen:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Spara `ip_address` värdet. Den används i nästa avsnitt.

> [!TIP]
> Du kan också hitta IP-adressen från körnings loggarna för den underordnade körningen för den här pipeline-steget. Mer information om att visa den här informationen finns i [övervaka Azure ml experiment körningar och mått](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Om du vill installera debugpy i din VS Code Development-miljö använder du följande kommando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Mer information om hur du använder debugpy med VS Code finns i [fjärrfelsökning](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Om du vill konfigurera VS-kod för att kommunicera med Azure Machine Learning beräkning som kör fel söknings programmet, skapar du en ny fel söknings konfiguration:

    1. Från VS Code väljer du __Felsök__ -menyn och väljer sedan __Öppna konfigurationer__. En fil med namnet __launch.jspå__ öppnas.

    1. Leta upp raden som innehåller i __launch.jspå__ filen `"configurations": [` och infoga följande text. Ändra `"host": "<IP-ADDRESS>"` posten till den IP-adress som returnerades i loggarna från föregående avsnitt. Ändra `"localRoot": "${workspaceFolder}/code/step"` posten till en lokal katalog som innehåller en kopia av skriptet som felsöks:

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
        > Om det redan finns andra poster i avsnittet konfigurationer lägger du till ett kommatecken (,) efter den kod som du har infogat.

        > [!TIP]
        > Den bästa metoden, särskilt för pipelines är att hålla resurserna för skript i separata kataloger så att koden endast är relevant för vart och ett av stegen. I det här exemplet `localRoot` refererar exempel till värdet `/code/step1` .
        >
        > Om du felsöker flera skript i olika kataloger skapar du ett separat konfigurations avsnitt för varje skript.

    1. Spara __launch.jspå__ filen.

### <a name="connect-the-debugger"></a>Anslut fel söknings programmet

1. Öppna VS Code och öppna en lokal kopia av skriptet.
2. Ange Bryt punkter där du vill att skriptet ska stoppa när du har kopplat.
3. Medan den underordnade processen kör skriptet och `Timeout for debug connection` visas i loggarna använder du F5-tangenten eller väljer __Felsök__. När du uppmanas väljer du __Azure Machine Learning Compute: konfiguration av fjärrfelsökning__ . Du kan också välja fel söknings ikonen från sido fältet, __Azure Machine Learning: fjärrfelsöknings__ post i list rutan Felsök och Använd sedan den gröna pilen för att koppla fel sökaren.

    Vid det här tillfället ansluter VS Code till debugpy på Compute-noden och stoppas vid den Bryt punkt som du har angett tidigare. Nu kan du gå igenom koden när den körs, Visa variabler osv.

    > [!NOTE]
    > Om loggen visar att en uppgift visas `Debugger attached = False` har tids gränsen gått ut och skriptet fortsatte utan fel sökning. Skicka pipelinen igen och Anslut fel söknings programmet efter `Timeout for debug connection` meddelandet och innan tids gränsen upphör att gälla.

## <a name="debug-and-troubleshoot-deployments"></a>Felsöka och felsöka distributioner

I vissa fall kan du behöva interaktivt felsöka python-koden som finns i modell distributionen. Om Entry-skriptet till exempel inte fungerar och orsaken inte kan fastställas av ytterligare loggning. Genom att använda VS Code och debugpy kan du koppla till koden som körs i Docker-behållaren.

> [!IMPORTANT]
> Den här fel söknings metoden fungerar inte när du använder `Model.deploy()` och `LocalWebservice.deploy_configuration` för att distribuera en modell lokalt. I stället måste du skapa en avbildning med metoden [Model. Package ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-) .

Lokal distribution av webb tjänster kräver en fungerande Docker-installation på det lokala systemet. Mer information om hur du använder Docker finns i [Docker-dokumentationen](https://docs.docker.com/). Observera att när du arbetar med beräknings instanser är Docker redan installerat.

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Om du vill installera debugpy i din lokala VS-kod utvecklings miljö använder du följande kommando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Mer information om hur du använder debugpy med VS Code finns i [fjärrfelsökning](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Om du vill konfigurera VS-kod för att kommunicera med Docker-avbildningen skapar du en ny fel söknings konfiguration:

    1. Från VS Code väljer du __fel söknings__ menyn i __körnings__ omfattningen och väljer sedan __Öppna konfigurationer__. En fil med namnet __launch.jspå__ öppnas.

    1. Leta upp objektet __"konfigurationer"__ (den rad som innehåller) i __launch.jspå__ filen `"configurations": [` och infoga följande text. 

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
        Efter infogningen bör __launch.jspå__ filen likna följande:
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
        > Om det redan finns andra poster i avsnittet konfigurationer lägger du till ett kommatecken ( __,__ ) efter den kod som du har infogat.

        Det här avsnittet bifogar Docker-behållaren med port __5678__.

    1. Spara __launch.jspå__ filen.

### <a name="create-an-image-that-includes-debugpy"></a>Skapa en avbildning som innehåller debugpy

1. Ändra Conda-miljön för distributionen så att den innehåller debugpy. I följande exempel visas hur du lägger till den med hjälp av `pip_packages` parametern:

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

1. Om du vill starta debugpy och vänta en anslutning när tjänsten startas lägger du till följande längst upp i `score.py` filen:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Skapa en avbildning baserat på miljö definitionen och hämta avbildningen till det lokala registret. 

    > [!NOTE]
    > I det här exemplet förutsätts att `ws` du pekar på din Azure Machine Learning arbets yta och det `model` är modellen som distribueras. `myenv.yml`Filen innehåller de Conda-beroenden som skapades i steg 1.

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

    När avbildningen har skapats och hämtats (den här processen kan ta mer än 10 minuter, så vänta på patient), visas avbildnings Sök vägen (inklusive lagrings plats, namn och tagg, som i det här fallet också dess sammandrag) i ett meddelande som liknar följande:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. För att göra det enklare att arbeta med avbildningen lokalt kan du använda följande kommando för att lägga till en tagg för avbildningen. Ersätt `myimagepath` i följande kommando med värdet location från föregående steg.

    ```bash
    docker tag myimagepath debug:1
    ```

    För resten av stegen kan du referera till den lokala avbildningen som `debug:1` i stället för värdet för fullständig avbildnings Sök väg.

### <a name="debug-the-service"></a>Felsöka tjänsten

> [!TIP]
> Om du anger en tids gräns för debugpy-anslutningen i `score.py` filen måste du ansluta vs-kod till felsökningssessionen innan tids gränsen upphör att gälla. Starta VS Code, öppna den lokala kopian av `score.py` , ange en Bryt punkt och låt den vara klar innan du använder stegen i det här avsnittet.
>
> Mer information om fel sökning och inställning av Bryt punkter finns i [fel sökning](https://code.visualstudio.com/Docs/editor/debugging).

1. Om du vill starta en Docker-behållare med hjälp av avbildningen använder du följande kommando:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Detta kopplar ditt `score.py` lokala namn till den i behållaren. Därför avspeglas alla ändringar som görs i redigeraren automatiskt i behållaren

2. För en bättre upplevelse kan du gå till behållaren med ett nytt VS Code-gränssnitt. Välj en `Docker` omfattning från vs Code-sido fältet, leta upp din lokala behållare i den här dokumentationen `debug:1` . Högerklicka på den här behållaren och välj `"Attach Visual Studio Code"` , så öppnas ett nytt vs Code-gränssnitt automatiskt och det här gränssnittet visar inifrån den skapade behållaren.

    ![Gränssnittet för container VS-kod](./media/how-to-troubleshoot-deployment/container-interface.png)

3. I behållaren kör du följande kommando i gränssnittet

    ```bash
    runsvdir /var/runit
    ```
    Sedan kan du se följande utdata i gränssnittet i din behållare:

    ![Utdata för kör konsolen för container](./media/how-to-troubleshoot-deployment/container-run.png)

4. Om du vill bifoga VS Code till debugpy i behållaren öppnar du VS Code och använder F5-tangenten eller väljer __Felsök__. När du uppmanas väljer du __Azure Machine Learning distribution: Docker-felsöknings__ konfiguration. Du kan också välja ikonen __Kör__ omfattning från sido rutan, __Azure Machine Learning distribution: Docker-felsöknings__ post från List rutan Felsök och Använd sedan den gröna pilen för att koppla fel sökaren.

    ![Fel söknings ikonen, starta fel söknings knappen och konfigurations väljaren](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    När du har klickat på den gröna pilen och kopplat fel söknings programmet kan du se viss ny information i gränssnittet för behållare VS-kod:
    
    ![Behållar fel sökaren bifogad information](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    I ditt huvud VS-kod gränssnitt kan du också se följande:

    ![VS Code-brytpunkten i score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

Nu har den lokala `score.py` som är kopplad till behållaren redan stoppad vid de Bryt punkter där du har angett. Vid det här tillfället ansluter VS Code till debugpy i Docker-behållaren och stoppar Docker-behållaren vid den Bryt punkt som du angav tidigare. Nu kan du gå igenom koden när den körs, Visa variabler osv.

Mer information om hur du använder VS Code för att felsöka python finns i [Felsöka python-koden](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Stoppa containern

Om du vill stoppa behållaren använder du följande kommando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat VS Code Remote kan du använda en beräknings instans som fjärrberäkning från VS Code för att interaktivt felsöka din kod. 

Läs mer om fel sökning:

* [Distribution av lokal modell](how-to-troubleshoot-deployment-local.md)
* [Distribution av fjärrmodell](how-to-troubleshoot-deployment.md)
* [Pipelines för maskininlärning](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

