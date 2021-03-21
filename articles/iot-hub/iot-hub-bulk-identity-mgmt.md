---
title: Import/export av Azure IoT Hub enhets identiteter | Microsoft Docs
description: Använda Azure IoT service SDK för att köra Mass åtgärder mot identitets registret för att importera och exportera enhets identiteter. Med import åtgärder kan du skapa, uppdatera och ta bort enhets identiteter i bulk.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e7a725b78fa828ce1286e212ee7de0205968156
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536087"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Massimportera och massexportera IoT Hub-enhetsidentiteter

Varje IoT-hubb har ett identitets register som du kan använda för att skapa resurser per enhet i tjänsten. I identitets registret kan du också kontrol lera åtkomsten till de enhets riktade slut punkterna. I den här artikeln beskrivs hur du importerar och exporterar enhets identiteter i bulk till och från ett identitets register. Om du vill se ett fungerande exempel i C# och lära dig hur du kan använda den här funktionen när du klonar en hubb till en annan region, se [hur du klonar en IoT Hub](iot-hub-how-to-clone.md).

> [!NOTE]
> IoT Hub har nyligen lagt till stöd för virtuellt nätverk i ett begränsat antal regioner. Den här funktionen skyddar import-och export åtgärder och eliminerar behovet av att skicka nycklar för autentisering.  I början är stödet för virtuellt nätverk endast tillgängligt i följande regioner: *WestUS2*, *öster* och *usasödracentrala*. Mer information om stöd för virtuella nätverk och API-anrop för att implementera det finns i [IoT Hub stöd för virtuella nätverk](virtual-network-support.md).

Import-och export åtgärder sker i samband med *jobb* som gör att du kan köra Mass åtgärder mot en IoT-hubb.

Klassen **RegistryManager** innehåller metoderna **ExportDevicesAsync** och **ImportDevicesAsync** som använder **jobb** ramverket. Med dessa metoder kan du exportera, importera och synkronisera hela identitet registret för IoT Hub.

I det här avsnittet beskrivs hur du använder **RegistryManager** -klassen och- **jobb** systemet för att utföra Mass import och export av enheter till och från en IoT Hubs identitets register. Du kan också använda Azure-IoT Hub Device Provisioning Service för att möjliggöra Zero Touch-etablering, just-in-Time-etablering till en eller flera IoT-hubbar utan mänsklig inblandning. Mer information finns i dokumentationen för [etablerings tjänsten](../iot-dps/index.yml).

## <a name="what-are-jobs"></a>Vad är jobb?

Identitets register åtgärder använder **jobb** systemet när åtgärden:

* Har en potentiellt lång körnings tid jämfört med standard körnings åtgärder.

* Returnerar en stor mängd data till användaren.

I stället för ett enda API-anrop som väntar eller blockerar resultatet av åtgärden, skapar åtgärden asynkront ett **jobb** för IoT Hub. Åtgärden returnerar omedelbart ett **JobProperties** -objekt.

Följande C#-kodfragment visar hur du skapar ett export jobb:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Om du vill använda **RegistryManager** -klassen i C#-koden lägger du till paketet **Microsoft. Azure. Devices** NuGet i projektet. **RegistryManager** -klassen finns i namn rymden **Microsoft. Azure. Devices** .

Du kan använda klassen **RegistryManager** för att fråga efter **jobbets** tillstånd med hjälp av returnerade metadata för **JobProperties** . Om du vill skapa en instans av klassen **RegistryManager** använder du **CreateFromConnectionString** -metoden.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Om du vill hitta anslutnings strängen för din IoT-hubb går du till Azure Portal:

- Gå till IoT-hubben.

- Välj **principer för delad åtkomst**.

- Välj en princip med hänsyn till de behörigheter som du behöver.

- Kopiera ConnectionString från panelen till höger på skärmen.

Följande C#-kodfragment visar hur du avsöker var femte sekund för att se om jobbet har körts klart:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

> [!NOTE]
> Om ditt lagrings konto har brand Väggs konfigurationer som begränsar IoT Hubens anslutning, bör du överväga att använda [Microsofts betrodda första part undantag](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (tillgängligt i utvalda regioner för IoT Hub med hanterad tjänst identitet).


## <a name="device-importexport-job-limits"></a>Utskrifts gränser för enhets import/export

Endast 1 aktiv import-eller export jobb för enhet är tillåtet i taget för alla IoT Hub-nivåer. IoT Hub har också gränser för antalet jobb åtgärder. Mer information finns i [referens-IoT Hub kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Exportera enheter

Använd **ExportDevicesAsync** -metoden för att exportera hela identitet registret i IoT Hub till en Azure Storage BLOB-behållare med hjälp av en signatur för delad åtkomst (SAS). Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

Med den här metoden kan du skapa pålitliga säkerhets kopior av enhets informationen i en BLOB-behållare som du styr.

Metoden **ExportDevicesAsync** kräver två parametrar:

* En *sträng* som innehåller en URI för en BLOB-behållare. Denna URI måste innehålla en SAS-token som ger skriv åtkomst till behållaren. Jobbet skapar en block-BLOB i den här behållaren för att lagra serialiserade export enhets data. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Ett *booleskt värde* som anger om du vill utesluta autentiseringsinställningar från dina export data. Om **värdet är false** inkluderas autentiseringsinställningar i Exportera utdata. Annars exporteras nycklar som **Null**.

Följande C#-kodfragment visar hur du startar ett export jobb som innehåller nycklar för enhetsautentisering i exportera data och sedan söker efter slut för ande:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

I jobbet lagras utdata i den tillhandahållna BLOB-behållaren som en Block-Blob med namnet **devices.txt**. Utdatan består av JSON-serialiserade enhets data, med en enhet per rad.

I följande exempel visas utdata-data:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Om en enhet har dubbla data, exporteras även de dubbla data tillsammans med enhets data. I följande exempel visas det här formatet. Alla data från raden "twinETag" tills slutet är dubbla data.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Om du behöver åtkomst till dessa data i kod kan du enkelt deserialisera dessa data med klassen **ExportImportDevice** . Följande C#-kodfragment visar hur du läser enhets information som tidigare har exporter ATS till en Block-Blob:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importera enheter

**ImportDevicesAsync** -metoden i **RegistryManager** -klassen gör det möjligt att utföra Mass import-och synkroniserings åtgärder i identitets registret för IoT Hub. Precis som metoden **ExportDevicesAsync** använder **ImportDevicesAsync** -metoden **jobb** ramverket.

Ta del av metoden **ImportDevicesAsync** eftersom du förutom att tillhandahålla nya enheter i identitets registret, kan även uppdatera och ta bort befintliga enheter.

> [!WARNING]
> Det går inte att återställa en import åtgärd. Säkerhetskopiera alltid befintliga data med **ExportDevicesAsync** -metoden till en annan BLOB-behållare innan du gör Mass ändringar i identitets registret.

Metoden **ImportDevicesAsync** tar två parametrar:

* En *sträng* som innehåller en URI för en [Azure Storage](../storage/index.yml) BLOB-behållare som ska användas som *indatamängd* för jobbet. Denna URI måste innehålla en SAS-token som beviljar Läs behörighet till behållaren. Den här behållaren måste innehålla en blob med namnet **devices.txt** som innehåller de serialiserade enhets data som ska importeras till identitets registret. Import data måste innehålla enhets information i samma JSON-format som **ExportImportDevice** -jobbet använder när den skapar en **devices.txt** -blob. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* En *sträng* som innehåller en URI för en [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) BLOB-behållare som ska användas som *utdata* från jobbet. Jobbet skapar en block-BLOB i den här behållaren för att lagra fel information från det slutförda import **jobbet**. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> De två parametrarna kan peka på samma BLOB-behållare. De separata parametrarna ger bara bättre kontroll över dina data eftersom behållaren för utdata kräver ytterligare behörigheter.

Följande C#-kodfragment visar hur du startar ett import jobb:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Den här metoden kan också användas för att importera data för enheten till dubbla. Formatet för data inmatningen är detsamma som det format som visas i avsnittet **ExportDevicesAsync** . På så sätt kan du importera exporterade data på annat sätt. **$Metadata** är valfritt.

## <a name="import-behavior"></a>Import beteende

Du kan använda metoden **ImportDevicesAsync** för att utföra följande Mass åtgärder i identitets registret:

* Mass registrering av nya enheter
* Mass borttagning av befintliga enheter
* Mass status ändringar (aktivera eller inaktivera enheter)
* Mass tilldelning av nya enhets verifierings nycklar
* Automatisk generering av enhets verifierings nycklar
* Mass uppdatering av dubbla data

Du kan utföra valfri kombination av föregående åtgärder inom ett enda **ImportDevicesAsync** -anrop. Du kan till exempel registrera nya enheter och ta bort eller uppdatera befintliga enheter på samma tidpunkt. När det används tillsammans med **ExportDevicesAsync** -metoden kan du fullständigt migrera alla enheter från en IoT-hubb till en annan.

Om import filen innehåller dubbla metadata skriver denna metadata över de befintliga dubbla metadata. Om import filen inte innehåller dubbla metadata `lastUpdateTime` uppdateras bara metadata med den aktuella tiden.

Använd den valfria egenskapen **importMode** i importens serialiserings data för varje enhet för att styra import processen per enhet. Egenskapen **importMode** har följande alternativ:

| importMode | Beskrivning |
| --- | --- |
| **createOrUpdate** |Om det inte finns någon enhet med det angivna **ID: t** är den nyligen registrerad. <br/>Om enheten redan finns skrivs befintlig information över med angivna indata utan hänsyn till **etag** -värdet. <br> Användaren kan välja att ange dubbla data tillsammans med enhets data. Dubbla etag, om det anges, bearbetas oberoende av enhetens etag. Om det finns ett matchnings fel med de befintliga dubbla etag-filerna skrivs ett fel till logg filen. |
| **fram** |Om det inte finns någon enhet med det angivna **ID: t** är den nyligen registrerad. <br/>Om enheten redan finns skrivs ett fel till logg filen. <br> Användaren kan välja att ange dubbla data tillsammans med enhets data. Dubbla etag, om det anges, bearbetas oberoende av enhetens etag. Om det finns ett matchnings fel med de befintliga dubbla etag-filerna skrivs ett fel till logg filen. |
| **uppdatera** |Om det redan finns en enhet med det angivna **ID: t** skrivs befintlig information över med de angivna indata utan hänsyn till **etag** -värdet. <br/>Om enheten inte finns skrivs ett fel till logg filen. |
| **updateIfMatchETag** |Om det redan finns en enhet med det angivna **ID: t** skrivs befintlig information över med de angivna indata endast om det finns en **etag** -matchning. <br/>Om enheten inte finns skrivs ett fel till logg filen. <br/>Om det finns en **etag** -matchning, skrivs ett fel till logg filen. |
| **createOrUpdateIfMatchETag** |Om det inte finns någon enhet med det angivna **ID: t** är den nyligen registrerad. <br/>Om enheten redan finns skrivs befintlig information över med angivna indata endast om det finns en **etag** -matchning. <br/>Om det finns en **etag** -matchning, skrivs ett fel till logg filen. <br> Användaren kan välja att ange dubbla data tillsammans med enhets data. Dubbla etag, om det anges, bearbetas oberoende av enhetens etag. Om det finns ett matchnings fel med de befintliga dubbla etag-filerna skrivs ett fel till logg filen. |
| **ta bort** |Om det redan finns en enhet med det angivna **ID: t** tas den bort utan hänsyn till **etag** -värdet. <br/>Om enheten inte finns skrivs ett fel till logg filen. |
| **deleteIfMatchETag** |Om det redan finns en enhet med det angivna **ID: t** tas den bara bort om det finns en **etag** -matchning. Om enheten inte finns skrivs ett fel till logg filen. <br/>Om det finns en ETag-matchning, skrivs ett fel till logg filen. |

> [!NOTE]
> Om serialiserings data inte uttryckligen definierar en **importMode** -flagga för en enhet, används standardvärdet **createOrUpdate** under import åtgärden.

## <a name="import-devices-example--bulk-device-provisioning"></a>Exempel på import enheter – Mass enhets etablering

Följande C#-kod exempel illustrerar hur du genererar flera enhets identiteter som:

* Inkludera nycklar för autentisering.
* Skriv enhets informationen till en Block-Blob.
* Importera enheterna till identitets registret.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exempel på import enheter – Mass borttagning

I följande kod exempel visas hur du tar bort de enheter som du har lagt till med föregående kod exempel:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Hämta SAS-URI för behållare

I följande kod exempel visas hur du skapar en [SAS-URI](../storage/common/storage-sas-overview.md) med Läs-, skriv-och borttagnings behörighet för en BLOB-behållare:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utför Mass åtgärder mot identitets registret i en IoT-hubb. Många av de här åtgärderna, inklusive hur du flyttar enheter från en hubb till en annan, används i [avsnittet hantera enheter som är registrerade i IoT Hub för att klona en IoT Hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Klonings artikeln har ett fungerande exempel som är associerat med det, som finns i IoT C#-exempel på den här sidan: [Azure IoT-exempel för C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)med projektet som ImportExportDevicesSample. Du kan hämta exemplet och testa det. Det finns anvisningar i [hur du klonar en IoT Hub](iot-hub-how-to-clone.md) artikel.

Om du vill veta mer om hur du hanterar Azure IoT Hub kan du läsa följande artiklar:

* [Övervaka IoT Hub](monitor-iot-hub.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)

Om du vill utforska med hjälp av IoT Hub Device Provisioning Service för att aktivera Zero-Touch, just-in-Time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](../iot-dps/index.yml)