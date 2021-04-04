---
title: Hämta väder data från väder partner
description: Den här artikeln beskriver hur du hämtar väder data från partner.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460878"
---
# <a name="get-weather-data-from-weather-partners"></a>Hämta väder data från väder partner

Med Azure FarmBeats kan du hämta väder data från dina väder data leverantörer med hjälp av ett Docker-baserat anslutnings ramverk. Med hjälp av det här ramverket implementerar väder data leverantörer en Docker som kan integreras med FarmBeats. För närvarande stöds följande väder data leverantör.

  ![FarmBeats-partner](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Väder data kan användas för att generera instruktions bara insikter och bygga AI-eller ML-modeller i FarmBeats.

## <a name="before-you-start"></a>Innan du börjar

Se till att du har [installerat FarmBeats](./install-azure-farmbeats.md)för att få väder data. Väder integrering stöds i versionerna 1.2.11 och senare. 

## <a name="enable-weather-integration-with-farmbeats"></a>Aktivera väder integrering med FarmBeats

För att komma igång med väder data på din FarmBeats-Datahub:

1. Gå till din FarmBeats Datahub-Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. Gå till/partner-API: et och gör sedan en POST-begäran. Använd följande nytto last:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Om du till exempel vill hämta väder data från DTN använder du följande nytto Last. Du kan ändra namnet och beskrivningen enligt dina önskemål.

   > [!NOTE]
   > Följande steg kräver en API-nyckel. Kontakta DTN om du vill hämta en nyckel för din DTN-prenumeration.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Mer information om partner-objektet finns i [bilagan](get-weather-data-from-weather-partner.md#appendix) i den här artikeln.

   Föregående steg etablerar resurserna för att tillåta Docker att köras i kundens FarmBeats-miljö.  

   Det tar cirka 10 till 15 minuter att etablera resurserna.

3. Kontrol lera status för/partner-objektet som du skapade i föregående steg. Kontrol lera statusen genom att göra en GET-begäran på/partner-API: et och kontrol lera status för partner-objektet. När FarmBeats har tagit ställning till partnern, är statusen **aktive** rad.

4. Gör en GET-begäran i/JobType-API: et. Sök efter de väder jobb som du skapade tidigare i samarbets processen. I väder jobben har fältet **pipelineName** följande format: **partner-name_partner-type_job-Name**.

      Nu har din FarmBeats-instans en aktiv väderleks data partner. Du kan köra jobb för att begära väder data för en viss plats (latitud och longitud) och ett datum intervall. Jobb typerna kommer att ha information om vilka parametrar som krävs för att köra väder jobb.

      För DTN skapas till exempel följande jobb typer:
   
      - **get_dtn_daily_observations**: få dagliga observationer för en plats och en tids period.
      - **get_dtn_daily_forecasts**: få dagliga prognoser för en plats och en tids period.
      - **get_dtn_hourly_observations**: få Tim observationer för en plats och en tids period.
      - **get_dtn_hourly_forecasts**: få Tim prognoser för en plats och en tids period.

6. Anteckna ID: t och parametrarna för jobb typerna.

7. Gå till/Jobs-API: et och gör en POST-begäran på/Jobs. Använd följande nytto last:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Om du till exempel vill köra **get_dtn_daily_observations** använder du följande nytto last:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Föregående steg kör väder jobben enligt definitionen i partner Docker och matar in väder data i FarmBeats. Du kan kontrol lera jobbets status genom att göra en GET-begäran på/Jobs. Leta efter **currentState** i svaret. När du är klar har **currentState** angetts till **lyckades**.

## <a name="query-ingested-weather-data"></a>Fråga med inmatade väder data

När väder jobben har slutförts kan du fråga inmatade väder data för att bygga modeller eller åtgärds bara insikter med hjälp av FarmBeats Datahub REST-API: er.

Fråga efter väder data med hjälp av en FarmBeats REST API:

1. I din FarmBeats Datahub- [Swagger](https://yourdatahub.azurewebsites.net/swagger)går du till/WeatherDataLocation-API: et och gör en get-begäran. Svaret innehåller/WeatherDataLocation-objekt som har skapats för den plats (latitud och longitud) som jobb körningen har angett. Anteckna **ID: t** och **weatherDataModelId** för objekten.

2. Gör en GET/{ID}-begäran i/WeatherDataModel-API: t för **weatherDataModelId** som du gjorde tidigare. Väder data modellen visar alla metadata och all information om inmatade väder data. I objektet väder data modell kan du till exempel se information om väder mått som innehåller väder information och i vilka typer och enheter. Exempel:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Anteckna svaret från GET/{ID}-anropet för väder data modellen.

3. Gå till telemetri-API: et och gör en POST-begäran. Använd följande nytto last:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    Svaret visar de väder data som är tillgängliga under det angivna tidsintervallet:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

I föregående exempel visar svaret data för två tidsstämplar. Det visar också måttets namn (temperatur) och värden för rapporterade väder data i de två tidsstämplar. Referera till den associerade väder data modellen för att tolka typen och enheten för de rapporterade värdena.

## <a name="troubleshoot-job-failures"></a>Felsöka jobb fel

Om du vill felsöka jobb fel [kontrollerar du jobb loggarna](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Bilaga

|        Partner   |  Information   |
| ------- | -------             |
|     DockerDetails – imageName         |          Namn på Docker-avbildning. Till exempel docker.io/mydockerimage (bild i hub.docker.com) eller myazureacr.azurecr.io/mydockerimage (bild i Azure Container Registry) och så vidare. Om inget register anges är standardvärdet hub.docker.com.      |
|          DockerDetails - imageTag             |         Taggnamn för Docker-avbildningen. Standardvärdet är "senaste".     |
|  DockerDetails – autentiseringsuppgifter      |  Autentiseringsuppgifter för att få åtkomst till den privata Docker. Partnern tillhandahåller autentiseringsuppgifterna.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Mer information finns i [alla tillgängliga virtuella Linux-datorer](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> Giltiga värden är "Small", "ExtraLarge", "Large", "A8", "A9", "medium", "A5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1" , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2" och "STANDARD_A8m_V2". *Standardvärdet är STANDARD_D2_V2.*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Antal dedikerade noder per batch-pool. Standardvärdet är 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch-ID för Node-agent. För närvarande stöds endast batch Node-agenten "batch. Node. Ubuntu 18,04".    |
| DockerDetails - partnerCredentials | Autentiseringsuppgifter för att anropa partner-API: t i Docker. Partnern tillhandahåller den här informationen baserat på mekanismen för auktorisering som stöds. till exempel användar namn och lösen ord eller API-nycklar. |
| partnerType | "Väder". Andra partner typer i FarmBeats är "sensor" och "bilder".  |
|  name   |   Partnerns önskade namn i FarmBeats-systemet.   |
|  beskrivning |  Beskrivning.   |

## <a name="next-steps"></a>Nästa steg

Nu när du har efterfrågat sensor data från din Azure FarmBeats-instans kan du läsa om hur du [genererar kartor](generate-maps-in-azure-farmbeats.md#generate-maps) för dina grupper.