---
title: Distribuera Azure SQL Edge med Docker – Azure SQL Edge
description: Lär dig mer om att distribuera Azure SQL Edge med Docker
keywords: SQL Edge, container, Docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392086"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Distribuera Azure SQL Edge med Docker

I den här snabb starten använder du Docker för att hämta och köra Azure SQL Edge-behållar avbildningen. Anslut sedan med **SQLCMD** för att skapa din första databas och kör frågor.

Den här avbildningen består av Azure SQL Edge baserat på Ubuntu 18,04. Den kan användas med Docker-motorn 1.8 + på Linux eller i Docker för Mac/Windows.

## <a name="prerequisites"></a>Förutsättningar

- Docker-motorn 1.8 + i alla Linux-distributioner som stöds eller Docker för Mac/Windows. Mer information finns i [Installera Docker](https://docs.docker.com/engine/installation/). Eftersom Azure SQL Edge-avbildningarna baseras på Ubuntu 18,04, rekommenderar vi att du använder en Ubuntu 18,04 Docker-värd.
- Docker **overlay2** lagrings driv rutin. Detta är standardinställningen för de flesta användare. Om du upptäcker att du inte använder den här lagrings leverantören och behöver ändra den, se anvisningarna och varningarna i [Docker-dokumentationen för att konfigurera overlay2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver).
- Minst 10 GB disk utrymme.
- Minst 1 GB RAM-minne.
- [Maskin varu krav för Azure SQL Edge](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Hämta och kör behållar avbildningen

Innan du startar följande steg måste du kontrol lera att du har valt det önskade gränssnittet (bash, PowerShell eller cmd) överst i den här artikeln.

1. Hämta behållar avbildningen av Azure SQL Edge från Microsoft Container Registry.

    - Hämta avbildningen av Azure SQL Edge-behållaren
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> För bash-kommandona i den här artikeln `sudo` används. På macOS & Windows kanske sudo inte krävs. Om du inte vill använda sudo för att köra Docker kan du konfigurera en Docker-grupp och lägga till användare i den gruppen i Linux. Mer information finns i avsnittet [efter installationen för Linux](https://docs.docker.com/engine/install/linux-postinstall/).

Föregående kommando hämtar de senaste avbildningarna av Azure SQL Edge-behållare. Om du vill se alla tillgängliga avbildningar går du till [Azure-SQL-egde Docker Hub-sidan](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Om du vill köra behållar avbildningen med Docker kan du använda följande kommando från en bash shell (Linux/macOS) eller en upphöjd PowerShell-kommandotolk.
    
    - Starta en Azure SQL Edge-instans som körs som Developer Edition
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Starta en Azure SQL Edge-instans som körs som Premium-utgåva
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Om du använder PowerShell i Windows för att köra dessa kommandon använder du dubbla citat tecken i stället för enkla citat tecken.


    > [!NOTE]
    > Lösen ordet bör följa standard lösen ords principen för Microsoft SQL Database-motorn, annars kan inte behållaren Konfigurera SQL-motorn och sluta fungera. Som standard måste lösen ordet vara minst 8 tecken långt och innehålla tecken från tre av följande fyra uppsättningar: versaler, gemener, bas 10 siffror och symboler. Du kan granska fel loggen genom att köra kommandot [Docker-loggar](https://docs.docker.com/engine/reference/commandline/logs/) .
    
    Följande tabell innehåller en beskrivning av parametrarna i föregående `docker run` exempel:

    | Parameter | Beskrivning |
    |-----|-----|
    | **-e "ACCEPT_EULA = Y"** |  Ange **ACCEPT_EULA** variabeln till ett värde för att bekräfta ditt godkännande av [licens avtalet för slutanvändare](https://go.microsoft.com/fwlink/?linkid=2139274). Inställning som krävs för Azure SQL Edge-avbildningen. |
    | **-e "MSSQL_SA_PASSWORD = yourStrong (!) Ords** | Ange ett eget starkt lösen ord som består av minst 8 tecken och uppfyller [lösen ords kraven för Azure SQL Edge](/sql/relational-databases/security/password-policy). Inställning som krävs för Azure SQL Edge-avbildningen. |
    | **-p 1433:1433** | Mappa en TCP-port på värd miljön (första värdet) med en TCP-port i behållaren (andra värdet). I det här exemplet lyssnar Azure SQL Edge på TCP 1433 i behållaren och exponeras för porten 1433 på värden. |
    | **--Name azuresqledge** | Ange ett eget namn på behållaren i stället för en slumpmässigt genererad. Om du kör mer än en behållare kan du inte återanvända samma namn. |
    | **d, DDD** | Köra behållaren i bakgrunden (daemon) |

    En fullständig lista över alla Azure SQL Edge-miljövariabler finns i [Konfigurera Azure SQL Edge med miljövariabler](configure.md#configure-by-using-environment-variables). Du kan också använda en [MSSQL. conf-fil](configure.md#configure-by-using-an-mssqlconf-file) för att konfigurera Azure SQL Edge-behållare.

3. Om du vill visa dina Docker-behållare använder du `docker ps` kommandot.
   
   ```bash
    sudo docker ps -a
   ```

4. Om **status** kolumnen visar statusen **upp** körs Azure SQL Edge i behållaren och lyssnar på den port som anges i kolumnen **ports** . Om kolumnen **status** för din Azure SQL Edge-behållare visar att den har **avslut ATS** kan du läsa avsnittet fel sökning i Azure SQL Edge-dokumentationen.

    `-h`Parametern (värd namn) är också användbar, men den används inte i den här självstudien för enkelhetens skull. Detta ändrar behållarens interna namn till ett anpassat värde. Detta är det namn som visas i följande Transact-SQL-fr åga:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    Inställningen `-h` och `--name` till samma värde är ett bra sätt att enkelt identifiera mål containern.

5. Som ett sista steg ändrar du ditt SA-lösenord eftersom `SA_PASSWORD` är synligt i `ps -eax` utdata och lagras i miljövariabeln med samma namn. Se stegen nedan.

## <a name="change-the-sa-password"></a> Ändra SA-lösenordet

**Sa** -kontot är en system administratör på Azure SQL Edge-instansen som skapas under installationen. När du har skapat din Azure SQL Edge-behållare kan `MSSQL_SA_PASSWORD` miljövariabeln som du har angett identifieras genom att köras `echo $SA_PASSWORD` i behållaren. Av säkerhets synpunkt ändrar du SA-lösenordet.

1. Välj ett starkt lösen ord som ska användas för SA-användaren.

2. Använd `docker exec` för att köra **SQLCMD** för att ändra lösen ordet med hjälp av Transact-SQL. I följande exempel ersätter du det gamla lösen ordet, `<YourStrong!Passw0rd>` och det nya lösen ordet, `<YourNewStrong!Passw0rd>` med dina egna lösen ords värden.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Ansluta till Azure SQL Edge

I följande steg används kommando rads verktyget för Azure SQL Edge, **SQLCMD**, inuti behållaren för att ansluta till Azure SQL Edge.

> [!NOTE]
> SQLCMD-verktyget är inte tillgängligt i ARM64-versionen av SQL Edge-behållare.

1. Använd `docker exec -it` kommandot för att starta ett interaktivt bash-gränssnitt i den behållare som körs. I följande exempel `azuresqledge` är namnet som anges av `--name` parametern när du skapade behållaren.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. När du är i behållaren ansluter du lokalt med SQLCMD. SQLCMD finns inte som standard i sökvägen, så du måste ange den fullständiga sökvägen.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Du kan utelämna lösen ordet på kommando raden för att uppmanas att ange det.

3. Om det lyckas, bör du komma till en **SQLCMD** kommando tolk: `1>` .

## <a name="create-and-query-data"></a>Skapa och fråga efter data

I följande avsnitt får du stegvisa anvisningar genom att använda **SQLCMD** och Transact-SQL för att skapa en ny databas, lägga till data och köra en enkel fråga.

### <a name="create-a-new-database"></a>Skapa en ny databas

Följande steg skapar en ny databas med namnet `TestDB` .

1. I kommando tolken **SQLCMD** klistrar du in följande Transact-SQL-kommando för att skapa en test databas:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. På nästa rad skriver du en fråga för att returnera namnet på alla databaser på servern:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Infoga data

Skapa sedan en ny tabell, `Inventory` och infoga två nya rader.

1. Växla kontext till den nya databasen från kommando tolken i **SQLCMD** `TestDB` :

   ```sql
   USE TestDB
   ```

2. Skapa en ny tabell med namnet `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Infoga data i den nya tabellen:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Skriv `GO` för att köra föregående kommandon:

   ```sql
   GO
   ```

### <a name="select-data"></a>Välj data

Kör nu en fråga för att returnera data från `Inventory` tabellen.

1. Från kommando tolken **SQLCMD** anger du en fråga som returnerar rader från `Inventory` tabellen där antalet är större än 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Kör kommandot:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Avsluta kommando tolken SQLCMD

1. För att avsluta **SQLCMD** -sessionen skriver du `QUIT` :

   ```sql
   QUIT
   ```

2. Om du vill avsluta den interaktiva kommando tolken i behållaren skriver du `exit` . Din behållare fortsätter att köras när du har avslutat det interaktiva bash-gränssnittet.

## <a name="connect-from-outside-the-container"></a>Anslut utanför behållaren

Du kan också ansluta till Azure SQL Edge-instansen på din Docker-dator från alla externa Linux-, Windows-eller macOS-verktyg som stöder SQL-anslutningar. Mer information om hur du ansluter till en SQL Edge-behållare från utsidan finns i [Anslut och fråga Azure SQL Edge](connect.md).

## <a name="remove-your-container"></a>Ta bort din behållare

Om du vill ta bort den Azure SQL Edge-behållare som används i den här självstudien kör du följande kommandon:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Om du stoppar och tar bort en behållare permanent raderas alla Azure SQL Edge-data i behållaren. Om du behöver bevara dina data kan du [skapa och kopiera en säkerhets kopia från behållaren](backup-restore.md) eller använda en [beständig teknik för behållar data](configure.md#persist-your-data).

## <a name="next-steps"></a>Nästa steg

- [Machine Learning och artificiell intelligens med ONNX i SQL Edge](onnx-overview.md).
- [Skapa en IoT-lösning från slut punkt till slut punkt med SQL Edge med hjälp av IoT Edge](tutorial-deploy-azure-resources.md).
- [Data strömning i Azure SQL Edge](stream-data.md)