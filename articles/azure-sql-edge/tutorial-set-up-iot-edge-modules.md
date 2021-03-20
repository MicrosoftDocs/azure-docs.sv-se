---
title: Konfigurera IoT Edge moduler i Azure SQL Edge
description: I del två av den här tre delen av Azure SQL Edge-självstudierna för förutsägelse av järn Malms orenheter, konfigurerar du IoT Edge moduler och anslutningar.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 75e6ebaea4c5ba883820d2309212b35fed128142
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422135"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Konfigurera IoT Edge moduler och anslutningar

I del två av den här självstudien i tre delar om förutsägelse av järn Malms orenheter i Azure SQL Edge ställer du in följande IoT Edge moduler:

- Azure SQL Edge
- IoT Edge modul för data Generator

## <a name="specify-container-registry-credentials"></a>Ange autentiseringsuppgifter för container registret

Autentiseringsuppgifterna för behållar register som är värdar för modulblad måste anges. Dessa finns i behållar registret som skapades i din resurs grupp. Navigera till avsnittet **åtkomst nycklar** . Anteckna följande fält:

- Registernamn
- Inloggningsserver
- Användarnamn
- Lösenord

Ange nu autentiseringsuppgifter för behållare i IoT Edge-modulen.

1. Navigera till IoT-hubben som skapades i din resurs grupp.

2. I avsnittet **IoT Edge** under **Automatisk enhets hantering** klickar du på **enhets-ID**. I den här självstudien är ID: t `IronOrePredictionDevice` .

3. Välj avsnittet **Ange moduler** .

4. Ange följande värden under **container Registry autentiseringsuppgifter**:

   _Fält_|_Värde_
   -------|-------
   Name|Registernamn
   Adress|Inloggningsserver
   Användarnamn|Användarnamn
   Lösenord|Lösenord
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>Bygg, push och distribuera data Generator-modulen

1. Klona [projektfilerna](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) till datorn.
2. Öppna filen **IronOre_Silica_Predict. SLN** med Visual Studio 2019
3. Uppdatera behållar register informationen i **deployment.template.jspå** 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. Uppdatera **modules.jspå** filen för att ange mål behållar registret (eller lagrings platsen för modulen)
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. Kör projektet i antingen fel söknings-eller publicerings läge för att säkerställa att projektet körs utan problem 
6. Skicka projektet till behållar registret genom att högerklicka på projekt namnet och sedan välja **Bygg-och push-IoT Edge moduler**.
7. Distribuera data Generator-modulen som en IoT Edge modul till din Edge-enhet. 

## <a name="deploy-the-azure-sql-edge-module"></a>Distribuera Azure SQL Edge-modulen

1. Distribuera Azure SQL Edge-modulen genom att klicka på **+ Lägg till** och sedan **Marketplace-modul**. 

2. På bladet **Marketplace för IoT Edge module** söker du efter *Azure SQL Edge* och väljer *Azure SQL Edge Developer*. 

3. Konfigurera Azure SQL Edge-modulen genom att klicka på den nyligen tillagda *Azure SQL Edge* -modulen under **IoT Edge moduler** . Mer information om konfigurations alternativen finns i [Distribuera Azure SQL Edge](./deploy-portal.md).

4. Lägg till `MSSQL_PACKAGE` miljövariabeln i *Azure SQL Edge* module-distributionen och ange SAS-URL: en för databasen DACPAC-filen som skapades i steg 8 i [del ett](tutorial-deploy-azure-resources.md) av den här självstudien.

5. Klicka på **Uppdatera**

6. På sidan **Ange moduler på enheten** klickar du på **nästa: vägar >**.

7. I fönstret vägar på sidan **Ange moduler på enhet** anger du vägarna för modulen för att IoT Edge Hub-kommunikation enligt beskrivningen nedan. Se till att uppdatera modulernas namn i flödes definitionerna nedan.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Exempel:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. På sidan **Ange moduler på enheten** klickar du på **Nästa: granska + skapa >**

8. På sidan **Ange moduler på enheten** klickar du på **skapa**

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Skapa och starta T-SQL streaming-jobbet i Azure SQL Edge.

1. Öppna Azure Data Studio.

2. På fliken **Välkommen** startar du en ny anslutning med följande information:

   |_Fält_|_Värde_|
   |-------|-------|
   |Anslutningstyp| Microsoft SQL Server|
   |Server|Offentlig IP-adress som anges i den virtuella datorn som skapades för den här demon|
   |Användarnamn|sa|
   |Lösenord|Det starka lösen ord som användes när Azure SQL Edge-instansen skapades|
   |Databas|Standardvärde|
   |Servergrupp|Standardvärde|
   |Namn (valfritt)|Ange ett valfritt namn|

3. Klicka på **Anslut**

4. Öppna en ny antecknings bok på menyn **Arkiv** och använd kortkommandot CTRL + N.

5. I fönstret ny fråga kör du skriptet nedan för att skapa strömnings jobbet T-SQL. Se till att ändra följande variabler innan du kör skriptet. 
   - *SQL_SA_Password:* Det MSSQL_SA_PASSWORD värde som angavs när Azure SQL Edge-modulen distribuerades. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Använd följande fråga för att kontrol lera att data från modulen datagenerering strömmas i databasen. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


I den här självstudien har vi distribuerat data Generator-modulen och SQL Edge-modulen. Sedan har vi skapat ett strömmande jobb för att strömma data som genererats av modulen för data generering till SQL. 

## <a name="next-steps"></a>Nästa steg

- [Distribuera ML-modell på Azure SQL Edge med ONNX](tutorial-run-ml-model-on-sql-edge.md)