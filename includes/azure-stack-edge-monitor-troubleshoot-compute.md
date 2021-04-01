---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89272140"
---
På en Azure Stack Edge-enhet som har beräknings rollen konfigurerad kan du felsöka eller övervaka enheten med hjälp av två olika kommando uppsättningar.

- Använda `iotedge` kommandon. De här kommandona är tillgängliga för grundläggande åtgärder för din enhet.
- Använda `dkrdbe` kommandon. De här kommandona är tillgängliga för en omfattande uppsättning åtgärder för din enhet.

Om du vill köra någon av ovanstående kommando uppsättningar måste du [ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Använda `iotedge` kommandon

Om du vill se en lista över tillgängliga kommandon [ansluter du till PowerShell-gränssnittet](#connect-to-the-powershell-interface) och använder `iotedge` funktionen.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

Följande tabell innehåller en kort beskrivning av de kommandon som är tillgängliga för `iotedge` :

|command  |Beskrivning |
|---------|---------|
|`check`     | Utföra automatiserade kontroller för vanliga konfigurations-och anslutnings problem       |
|`list`     | Lista med moduler         |
|`logs`     | Hämta loggarna för en modul        |
|`restart`     | Stoppa och starta om en modul         |


### <a name="use-dkrdbe-commands"></a>Använda `dkrdbe` kommandon

Om du vill se en lista över tillgängliga kommandon [ansluter du till PowerShell-gränssnittet](#connect-to-the-powershell-interface) och använder `dkrdbe` funktionen.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
Följande tabell innehåller en kort beskrivning av de kommandon som är tillgängliga för `dkrdbe` :

|command  |Beskrivning |
|---------|---------|
|`image`     | Hantera avbildningar. Om du vill ta bort oanvända avbildningar använder du: `dkrdbe image prune -a -f`       |
|`images`     | Lista bilder         |
|`inspect`     | Returnera låg nivå information om Docker-objekt         |
|`login`     | Logga in på ett Docker-register         |
|`logout`     | Logga ut från ett Docker-register         |
|`logs`     | Hämta loggarna för en behållare        |
|`port`     | Lista port mappningar eller en speciell mappning för behållaren        |
|`ps`     | Visa en lista med containrar        |
|`pull`     | Hämta en avbildning eller en lagrings plats från ett register         |
|`start`     | Starta en eller flera stoppade behållare         |
|`stats`     | Visa en Live-ström med container (s) användnings statistik för resurser         |
|`stop`     | Stoppa en eller flera behållare som körs        |
|`system`     | Hantera Docker         |
|`top`     | Visa processer som körs för en behållare         |

Om du vill ha hjälp med ett tillgängligt kommando använder du `dkrdbe <command-name> --help` .

Om du till exempel vill förstå användningen av `port` kommandot skriver du:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

De tillgängliga kommandona för `dkrdbe` funktionen använder samma parametrar som de som används för de vanliga Docker-kommandona. För de alternativ och parametrar som används med Docker-kommandot går du till [Använd Docker kommando raden](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Kontrol lera om modulen har distribuerats

Compute-moduler är behållare som har en affärs logik implementerad. Kontrol lera att en Compute-modul har distribuerats genom att köra `ps` kommandot och kontrol lera om behållaren (som motsvarar Compute-modulen) körs.

Kör kommandot för att hämta listan över alla behållare (inklusive de som har pausats) `ps -a` .

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Om det uppstod ett fel när behållar avbildningen skulle skapas eller när avbildningen hämtades kör du `logs edgeAgent` .  `EdgeAgent` är den IoT Edge runtime-behållare som ansvarar för att tillhandahålla andra behållare.

Eftersom `logs edgeAgent` dumpar alla loggar, är ett bra sätt att se de senaste felen att använda alternativet `--tail 20` .


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Hämta behållar loggar

Om du vill hämta loggar för en speciell behållare ska du först lista behållaren och sedan hämta loggarna för den behållare som du är intresse rad av.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Kör kommandot för att hämta listan över behållare som körs `ps` .

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Anteckna behållar-ID: t för den behållare som du behöver loggarna för.

4. Om du vill hämta loggarna för en speciell behållare kör du `logs` kommandot som tillhandahåller behållar-ID.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Övervaka enhetens användnings statistik

Använd kommandot för att övervaka minne, CPU-användning och IO på enheten `stats` .

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Kör `stats` kommandot så att du kan inaktivera Live Stream och bara hämta det första resultatet.

   ```powershell
   dkrdbe stats --no-stream
   ```

   I följande exempel visas användningen av denna cmdlet:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

