---
title: Felsöka Azure SQL Edge-distributioner
description: Lär dig mer om möjliga fel vid distribution av Azure SQL Edge
keywords: SQL Edge, fel sökning, distributions fel
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333111"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Felsöka Azure SQL Edge-distributioner 

Den här artikeln innehåller information om möjliga fel som kan uppstå när du distribuerar och använder Azure SQL Edge-behållare och ger fel söknings tekniker som hjälper dig att lösa problemen. 

Azure SQL Edge stöder två distributions modeller: 
- Ansluten distribution via Azure IoT Edge: Azure SQL Edge är tillgängligt på Azure Marketplace och kan distribueras som en modul för [Azure IoT Edge](../iot-edge/about-iot-edge.md). Mer information finns i [Distribuera Azure SQL Edge](deploy-portal.md).<br>

- Frånkopplad distribution: bilder i Azure SQL Edge-behållare kan hämtas från Docker Hub och distribueras antingen som en fristående Docker-behållare eller i ett Kubernetes-kluster. Mer information finns i [Distribuera Azure SQL Edge med Docker](disconnected-deployment.md) och [distribuera en Azure SQL Edge-behållare i Kubernetes](deploy-kubernetes.md).

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>Felsöka IoT Edge enhet och distributioner

Om du får ett fel meddelande när du distribuerar SQL Edge via Azure IoT Edge kontrollerar du att `iotedge` tjänsten är korrekt konfigurerad och körs. Följande dokument kan vara till hjälp vid fel sökning av problem som rör Azure IoT Edge:
- [Vanliga problem och lösningar för Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md).
- [Felsöka IoT Edge-enheten](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Docker kommando fel

Om du får fel meddelanden för några `docker` kommandon kontrollerar du att Docker-tjänsten körs och försöker köra med förhöjd behörighet.

I Linux kan du till exempel få följande fel när du kör `docker` kommandon:

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Om du får det här felet i Linux kan du prova med att köra samma kommandon som föregås av `sudo` . Om detta Miss lyckas kontrollerar du Docker-tjänsten körs och startar den vid behov.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

I Windows kontrollerar du att du startar PowerShell eller kommando tolken som administratör.

## <a name="azure-sql-edge-container-startup-errors"></a>Start fel för Azure SQL Edge-behållare

Om SQL Edge-behållaren inte kan köras kan du prova följande test:

- Om du använder Azure IoT Edge kontrollerar du att modulerna har laddats ned och att miljövariablerna och behållar skapande alternativen är korrekt angivna i manifestet för modulen.

- Om du använder Docker eller Kubernetes-baserad distribution kontrollerar du att `docker run` kommandot är korrekt formaterat. Mer information finns i [Distribuera Azure SQL Edge med Docker](disconnected-deployment.md) och [distribuera en Azure SQL Edge-behållare i Kubernetes](deploy-kubernetes.md).

- Om du får ett fel meddelande som `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` , försöker du mappa behållar porten 1433 till en port som redan används. Detta kan inträffa om du kör SQL Edge lokalt på värddatorn. Det kan också inträffa om du startar två SQL Edge-behållare och försöker mappa dem både till samma värd port. Om detta händer använder du `-p` parametern för att mappa behållar porten 1433 till en annan värd port. Exempel: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Om du får ett fel meddelande `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` när du försöker starta en behållare lägger du till användaren i Docker-gruppen i Ubuntu. Logga sedan ut och logga in igen när ändringen kommer att påverka nya sessioner. 

   ```bash
    usermod -aG docker $USER
   ```

- Kontrol lera om det finns några fel meddelanden från behållaren.

   ```bash
   docker logs e69e056c702d
   ```

- Om du använder en program vara för hantering av behållare kontrollerar du att den stöder container processer som körs som rot. Sqlservr-processen i behållaren körs som rot.

- Som standard körs Azure SQL Edge-behållare som en icke-rot användare med namnet `mssql` . Om du använder monterings punkter eller data volymer för att bevara data måste du se till att `mssql` användaren har rätt behörighet på volymen. Mer information finns i [Kör som icke-rot användare](configure.md#run-azure-sql-edge-as-non-root-user) och [bevara data](configure.md#persist-your-data).

- Om din SQL Edge Docker-behållare avslutas direkt efter att du har startat kontrollerar du Docker-loggarna. Om du använder PowerShell i Windows med `docker run` kommandot ska du använda dubbla citat tecken i stället för enkla citat tecken. Använd enkla citat tecken med PowerShell Core.

- Granska [fel loggarna för SQL Edge](#errorlogs).

## <a name="sql-edge-connection-failures"></a>SQL Edge-anslutningsfel

Om du inte kan ansluta till SQL Edge-instansen som körs i din behållare kan du prova följande test:

- Kontrol lera att din SQL Edge-behållare körs genom att titta i kolumnen **status** i `docker ps -a` utdata. Annars använder `docker start <Container ID>` du för att starta det.

- Om du har mappat till en värd port som inte är standard (inte 1433) ser du till att du anger porten i anslutnings strängen. Du kan se port mappningen i kolumnen **portar** i `docker ps -a` utdata. Mer information om hur du ansluter till Azure SQL Edge finns i [ansluta och fråga Azure SQL Edge](connect.md)

- Om du tidigare har distribuerat SQL Edge med mappad data volym eller data volym behållare och nu använder den befintliga mappad data volym eller data volym behållare, ignorerar SQL Edge värdet för `MSSQL_SA_PASSWORD` miljövariabeln. I stället används det tidigare konfigurerade SA-användarkontot. Detta inträffar eftersom SQL Edge återanvänder befintliga Master-databasfiler i den mappade volymen eller data volym behållaren. Om du stöter på det här problemet kan du använda följande alternativ:

    - Anslut med det tidigare använda lösen ordet, om det fortfarande är tillgängligt.
    - Konfigurera SQL Edge för att använda en annan mappad volym eller data volym behållare.
    - Ta bort de befintliga Master-databasfilerna (Master. MDF och mastlog. MDF) från den mappade volymen eller data volym containern.

- Granska [fel loggarna för SQL Edge](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> SQL Edge-konfiguration och fel loggar

SQL Edge-felloggarna finns som standard i **/var/opt/MSSQL/log** -katalogen i behållaren och kan nås på något av följande sätt:

- Om du har monterat en värd katalog på **/var/opt/MSSQL** när du skapade din behållare, kan du i stället titta i **logg** under katalogen på den mappade sökvägen på värden.
- Genom att använda en interaktiv kommando tolk kan du ansluta till behållaren. Om behållaren inte körs startar du först behållaren. Använd sedan en interaktiv kommando tolk för att kontrol lera loggarna. Du kan hämta container-ID genom att köra kommandot `docker ps` .

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    Kör följande kommandon från bash-sessionen i din behållare:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Om SQL Edge-behållaren är igång och du kan ansluta till instansen med hjälp av klient verktyg kan du använda den lagrade proceduren `sp_readerrorlog` för att läsa innehållet i SQL Edge-felloggen.

## <a name="execute-commands-in-a-container"></a>Köra kommandon i en behållare

Om du har en behållare som körs kan du köra kommandon i behållaren från en värd-Terminal.

Så här hämtar du behållar-ID:

```bash
docker ps -a
```

Starta en bash-Terminal i container-körningen:

```bash
docker exec -it <Container ID> /bin/bash
```

Nu kan du köra kommandon som om du kör dem i terminalen i behållaren. När du är färdig skriver du `exit` . Detta avslutas i den interaktiva kommando sessionen, men behållaren fortsätter att köras.

### <a name="enabling-verbose-logging"></a>Aktivera utförlig loggning

Om standard logg nivån för streaming-motorn inte tillhandahåller tillräckligt med information kan fel söknings loggning för strömnings motorn aktive ras i SQL Edge. Aktivera fel söknings loggning Lägg till `RuntimeLogLevel=debug` miljövariabeln i SQL Edge-distributionen. När du har aktiverat fel söknings loggning försöker du återskapa problemet och kontrollerar loggarna för alla relevanta meddelanden eller undantag. 

> [!NOTE]
> Alternativet utförlig loggning bör endast användas för fel sökning och inte för vanlig produktions belastning. 


## <a name="next-steps"></a>Nästa steg

- [Machine Learning och artificiell intelligens med ONNX i SQL Edge](onnx-overview.md)
- [Data strömning i Azure SQL Edge](stream-data.md)
- [Data kvarhållning och rensning](data-retention-overview.md)
- [Fylla tid luckor och tillräknade värden som saknas](imputing-missing-values.md)







