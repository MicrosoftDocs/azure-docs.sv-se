---
title: Felsöka Azure FarmBeats
description: Den här artikeln beskriver hur du felsöker Azure-FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: c45b6196b82682b37e253a33eed3940b68b4d61e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172992"
---
# <a name="troubleshoot-azure-farmbeats"></a>Felsöka Azure FarmBeats

Den här artikeln innehåller lösningar på vanliga problem med Azure FarmBeats. Om du behöver ytterligare hjälp kan du kontakta vår [Q&ett support forum](/answers/topics/azure-farmbeats.html) eller skicka ett e-postmeddelande till oss farmbeatssupport@microsoft.com .

> [!NOTE]
  > Om du har installerat FarmBeats under april och jobben Miss lyckas med ett tomt fel meddelande kanske inte installationen har tilldelats någon batch-kvot för att prioritera support för kritiska hälso-och säkerhets organisationer. Mer information finns [här](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/). Du måste begära att virtuella datorer ska tilldelas batch-kontot för att kunna köra jobben.

## <a name="install-issues"></a>Installations problem

  > [!NOTE]
  > Om du startar om installationen på grund av ett fel, se till att ta bort **resurs gruppen** eller ta bort alla resurser från resurs gruppen innan du återaktiverar installationen.

### <a name="invalid-sentinel-credentials"></a>Ogiltiga Sentinel-autentiseringsuppgifter

Indikatorns autentiseringsuppgifter som angavs under installationen är felaktiga. Starta om installationen med rätt autentiseringsuppgifter.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Den regionala konto kvoten för batch-konton för den angivna prenumerationen har uppnåtts

Öka kvoten eller ta bort de oanvända batch-kontona och starta om installationen.

### <a name="invalid-resource-group-location"></a>Ogiltig resurs grupps plats

Se till att **resurs gruppen** finns på samma plats som den **region** som angavs under installationen.

### <a name="other-install-issues"></a>Andra installations problem

Kontakta oss med följande information:

- Ditt prenumerations-ID
- Resurs grupps namn
- Följ stegen nedan för att bifoga logg filen för distributions problemet:

    1. Navigera till **resurs gruppen** i Azure Portal.

    2. Välj **distributioner** under avsnittet **Inställningar** till vänster.

    3. För varje distribution **som visas kan** du välja igenom informationen och hämta distributions informationen. Bifoga filen till e-postmeddelandet.

## <a name="sensor-telemetry"></a>Sensor telemetri

### <a name="cant-view-telemetry-data"></a>Det går inte att Visa telemetridata

**Symptom**: enheter eller sensorer distribueras och du har länkat FarmBeats med din enhets partner, men du kan inte hämta eller Visa telemetridata på FarmBeats.

**Korrigerande åtgärd**

1. Gå till FarmBeats-resurs gruppen.
2. Välj **Event Hub** -namnområdet ("sensor-partner-händelsehubbnamnområde-namespace-xxxx"), klicka på "Event Hubs" och Sök efter antalet inkommande meddelanden i händelsehubben som är tilldelad till partnern
3. Gör något av följande:

   - Kontakta din enhets partner om det inte finns *några inkommande meddelanden*.  
   - Om det finns *inkommande meddelanden* kan du kontakta oss med dina Datahub-och Accelerator-loggar och fånga in telemetri.

Information om hur du hämtar loggar finns i avsnittet ["samla in loggar manuellt"](#collect-logs-manually) .  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Det går inte att Visa telemetridata efter att du har matat in historiska/strömmande data från sensorer

**Symptom**: enheter eller sensorer distribueras och du har skapat enheter/sensorer på FarmBeats och inmatad telemetri till EventHub, men du kan inte hämta eller Visa telemetridata på FarmBeats.

**Korrigerande åtgärd**

1. Se till att du har utfört partner registreringen korrekt – du kan kontrol lera detta genom att gå till Datahub-Swagger, navigera till/partner-API, göra en get-och kontrol lera om partnern är registrerad. Om inte, följer du dessa [steg](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) för att lägga till partner.

2. Kontrol lera att du har använt rätt format för telemetri-meddelande:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Du har inte Azure Event Hubs-anslutningssträng

**Korrigerande åtgärd**

1. I Datahub Swagger går du till partner-API: et.
2. Välj **Hämta**  >  **prova** att  >  **köra**.

> [!NOTE]
> Partner-ID för den sensor partner som du är intresse rad av.

3. Gå tillbaka till partner-API: et och välj **get \<ID> /**.
4. Ange partner-ID: t från steg 3 och välj sedan **Kör**.

   API-svaret ska ha Event Hubs anslutnings strängen.

### <a name="device-appears-offline"></a>Enheten visas offline

**Symptom**: enheterna är installerade och du har länkat FarmBeats med din enhets partner. Enheterna är online och skickar telemetridata, men de visas offline.

**Korrigerande åtgärd** Rapporterings intervallet har inte kon figurer ATS för den här enheten. Kontakta enhets tillverkaren för att ange rapporterings intervallet. 

### <a name="error-deleting-a-device"></a>Fel vid borttagning av en enhet

När du tar bort en enhet kan du stöta på något av följande vanliga fel scenarier:  

**Meddelande**: "enheten refereras till sensorer: det finns en eller flera sensorer som är kopplade till enheten. Ta bort sensorer och ta sedan bort enheten. "  

**Betydelse**: enheten är associerad med flera sensorer som distribueras i Server gruppen.

**Korrigerande åtgärd**  

1. Ta bort sensorer som är kopplade till enheten via Accelerator.  
2. Om du vill koppla sensorer till en annan enhet ber du din enhets partner att göra det.  
3. Ta bort enheten med hjälp av ett `DELETE API` anrop och ange parametern Force som *True*.  

**Meddelande**: "enheten refereras till i enheter som ParentDeviceId: det finns en eller flera enheter som är associerade med den här enheten som underordnade enheter. Ta bort dem och ta sedan bort enheten. "  

**Innebär**: andra enheter är kopplade till enheten.  

**Korrigerande åtgärd**

1. Ta bort de enheter som är associerade med den här enheten.  
2. Ta bort den aktuella enheten.  

    > [!NOTE]
    > Du kan inte ta bort en enhet om sensorer är kopplade till den. Mer information om hur du tar bort tillhör ande sensorer finns i avsnittet **ta bort sensor** i [Hämta sensor data från sensor partner](get-sensor-data-from-sensor-partner.md).
    > Partner har inte behörighet att ta bort en enhet eller sensor. Endast administratörer har behörighet att ta bort.

## <a name="issues-with-jobs"></a>Problem med jobb

### <a name="farmbeats-internal-error"></a>Internt FarmBeats-fel

**Meddelande**: "FarmBeats internt fel, se fel söknings guiden för mer information."

**Korrigerande åtgärd** Det här problemet kan bero på ett tillfälligt fel i data pipelinen. Skapa jobbet igen. Om felet kvarstår kan du kontakta oss med fel meddelandet/loggarna.

## <a name="accelerator-troubleshooting"></a>Accelerator-felsökning

### <a name="access-control"></a>Åtkomstkontroll

**Problem**: du får ett fel meddelande när du lägger till en roll tilldelning.

**Meddelande**: "Det gick inte att hitta matchande användare".

**Korrigerande åtgärd** Kontrol lera e-postadressen som du försöker lägga till en roll tilldelning för. E-post-ID: t måste vara en exakt matchning av ID: t som har registrerats för den användaren i Active Directory. Om felet kvarstår kan du kontakta oss med fel meddelandet/loggarna.

### <a name="unable-to-log-in-to-accelerator"></a>Det gick inte att logga in på Accelerator

**Meddelande**: "fel: du har inte behörighet att anropa tjänsten. Kontakta administratören för auktorisering. "

**Korrigerande åtgärd** Be administratören att ge dig åtkomst till FarmBeats-distributionen. Detta kan göras genom att göra ett inlägg i RoleAssignment-API: erna eller via Access Control i fönstret **Inställningar** i Accelerator.  

Om du redan har beviljats åtkomst och använt det här felet kan du försöka igen genom att uppdatera sidan. Om felet kvarstår kan du kontakta oss med fel meddelandet/loggarna.

![Skärm bild som visar ett auktoriseringsfel.](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator problem  

**Problem**: du har fått ett Accelerator-fel av den obestämda orsaken.

**Meddelande**: "fel: ett okänt fel har inträffat."

**Korrigerande åtgärd** Det här felet uppstår om du lämnar sidan inaktiv i för lång tid. Uppdatera sidan. Om felet kvarstår kan du kontakta oss med fel meddelandet/loggarna.

**Problem**: FarmBeats Accelerator visar inte den senaste versionen, även efter att du har uppgraderat FarmBeatsDeployment.

**Korrigerande åtgärd** Det här felet inträffar på grund av en beständighet i en tjänste arbetar i webbläsaren. Gör följande:

1. Stäng alla flikar för webbläsare som har en Accelerator öppen och Stäng webbläsarfönstret.
2. Starta en ny instans av webbläsaren och Läs in Accelerator-URI igen. Den här åtgärden läser in den nya versionen av Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: bilder problem

### <a name="wrong-username-or-password"></a>Felaktigt användar namn eller lösen ord

**Jobb fel meddelande**: "fullständig autentisering krävs för att få åtkomst till den här resursen".

**Korrigerande åtgärd**: gör något av följande:

- Uppdatera FarmBeats med rätt användar namn/lösen ord med hjälp av nedanstående steg och försök utföra jobbet igen.

  **Uppdatera Sentinel-användarnamn**

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Sök efter resurs gruppen FarmBeats Datahub i **sökrutan.**
    3. Välj lagrings konto lagring * * * * * > **behållare**  >  **batch-prep-filer**  >  **to_vm**  >  **config.ini**
    4. Välj **Redigera**
    5. Uppdatera användar namnet i avsnittet sentinel_account

  **Uppdatera kontroll lösen ord**

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Sök efter resurs gruppen FarmBeats Datahub i **sökrutan.**
    3. Välj nyckel valv-* * * * *
    4. Välj åtkomst principer under Inställningar
    5. Välj **Lägg till åtkomst princip**
    6. Använd **hemlig hantering** för konfigurera från mall och Lägg till dig själv i huvud kontot
    7. Välj **Lägg till** och välj sedan **Spara** på sidan **åtkomst principer**
    8. Välj **hemligheter** under **Inställningar**
    9. Välj **Sentinel – lösen ord**
    10. Skapa en ny version av värdet och aktivera den.

- Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan för att se om jobbet lyckades.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel Hub: felaktig URL eller plats är inte tillgänglig

**Jobb fel meddelande**: "Hoppsan, något gick fel. Sidan som du försökte komma åt är (tillfälligt) inte tillgänglig. "

**Korrigerande åtgärd**:

1. Öppna [Sentinel](https://scihub.copernicus.eu/dhus/) i webbläsaren för att se om webbplatsen är tillgänglig.
2. Om webbplatsen inte är tillgänglig kontrollerar du om någon brand vägg, företags nätverk eller annan blockerande program vara förhindrar åtkomst till webbplatsen och vidtar sedan nödvändiga steg för att tillåta kontroll-URL: en. 
3. Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan om jobbet lyckades.  

### <a name="sentinel-server-down-for-maintenance"></a>Kontroll Server: nere för underhåll

**Jobb fel meddelande**: "Copernicus öppna Access Hub kommer snart att vara tillbaka! Vi beklagar besväret, men vi utför vissa underhåll för tillfället. Vi kommer att bli online igen snart! " 

**Korrigerande åtgärd**:

Det här problemet kan inträffa om några underhålls aktiviteter utförs på Sentinel-servern.

1. Om ett jobb eller en pipeline Miss lyckas eftersom underhåll utförs, skicka om jobbet efter en stund. 

   Information om planerade eller oplanerade aktiviteter finns i [Copernicus öppna Access Hub nyhets](https://scihub.copernicus.eu/news/) webbplats.  

2. Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan om jobbet lyckades.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: högsta antalet anslutningar har uppnåtts

**Jobb fel meddelande**: "maximalt antal samtidiga flöden som uppnås av användaren" " \<username> ."

**Betydelse**: om ett jobb Miss lyckas eftersom det maximala antalet anslutningar har uppnåtts, används samma kontroll konto i flera jobb.

**Korrigerande åtgärd**: prova något av följande:

* Vänta tills de andra jobben har slutförts innan du kör det misslyckade jobbet igen.
* Skapa ett nytt Sentinel-konto och uppdatera sedan Sentinel-användarnamnet och lösen ordet i FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Sentinel-Server: nekad anslutning

**Jobb fel meddelande**: "servern nekade anslutning på: http://172.30.175.69:8983/solr/dhus ."

**Korrigerande åtgärd**: det här problemet kan inträffa om några underhålls aktiviteter utförs på Sentinel-servern.

1. Om ett jobb eller en pipeline Miss lyckas eftersom underhåll utförs, skicka om jobbet efter en stund.

   Information om planerade eller oplanerade aktiviteter finns i [Copernicus öppna Access Hub nyhets](https://scihub.copernicus.eu/news/) webbplats.  

2. Kör det misslyckade jobbet igen eller kör ett satellit index jobb för ett datum intervall på 5 till 7 dagar och kontrol lera sedan om jobbet lyckades.

### <a name="soil-moisture-map-has-white-areas"></a>Jord fuktighets kartan har vita områden

**Problem**: **jord fukt kartan** genererades, men kartan har huvudsakligen vita områden.

**Korrigerande åtgärd**: det här problemet kan inträffa om de satellit index som genereras för den tid då kartan begärdes har NDVI-värden som är mindre än 0,3. Mer information finns [i teknisk guide från kontroll](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Kör jobbet igen under ett annat datum intervall och kontrol lera om NDVI-värdena i satellit index är fler än 0,3.

## <a name="collect-logs-manually"></a>Samla in loggar manuellt

[Installera och distribuera Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Samla in Azure Data Factory jobb loggar eller App Service loggar i Datahub

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter resurs gruppen FarmBeats Datahub i **sökrutan.**
3. På instrument panelen för **resurs gruppen** söker du efter *datahublogs \** lagrings konto. Till exempel *datahublogsmvxmq*.  
4. I kolumnen **namn** väljer du lagrings konto för att visa instrument panelen för **lagrings kontot** .
5. I **rutan datahubblogs \* *_ väljer du _* öppna i Utforskaren** för att visa **Open Azure Storage Explorer** -programmet.
6. I det vänstra fönstret väljer du **BLOB-behållare** och väljer sedan **jobb loggar** för Azure Data Factory loggar eller **appinsights-loggar** för App Service loggar.
7. Välj **Hämta** och hämta loggarna till en lokal mapp på datorn.

    ![Skärm bild som visar hämtade loggfiler.](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Samla in Azure Data Factory jobb loggar eller App Service loggar för Accelerator

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter resurs gruppen FarmBeats Accelerator i **sökrutan.**
3. På instrument panelen för **resurs gruppen** söker du efter *lagrings \** lagrings kontot. Till exempel *storagedop4k \**.
4. Välj lagrings kontot i kolumnen **namn** om du vill visa instrument panelen för **lagrings kontot** .
5. I **rutan lagrings plats \* *väljer du _* öppna i utforskaren** för att öppna Azure Storage Explorer programmet.
6. I det vänstra fönstret väljer du **BLOB-behållare** och väljer sedan **jobb loggar** för Azure Data Factory loggar eller **appinsights-loggar** för App Service loggar.
7. Välj **Hämta** och hämta loggarna till en lokal mapp på datorn.

## <a name="high-cpu-usage"></a>Hög CPU-användning

**Fel**: du får en e-postavisering som refererar till en **hög CPU-användnings avisering**.

**Korrigerande åtgärd**:

1. Gå till resurs gruppen FarmBeats Datahub.
2. Välj **App Service**.  
3. Gå till sidan för att skala upp [App Service priser](https://azure.microsoft.com/pricing/details/app-service/windows/)och välj sedan en lämplig pris nivå.

## <a name="weather-data-job-failures"></a>Data jobbs avbrott för väder

**Fel**: du kan köra jobb för att få väder data men jobbet Miss lyckas

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Samla in loggar för att felsöka väder data jobb fel

1. Gå till resurs gruppen FarmBeats i Azure Portal.
2. Klicka på den Data Factory-tjänst som är en del av resurs gruppen. Tjänsten kommer att ha en tagg "SKU: Datahub"

> [!NOTE]
> Om du vill visa taggarna för tjänsterna i resurs gruppen klickar du på "Redigera kolumner" och lägger till "Taggar" i vyn resurs grupp

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Skärm bild som visar taggen SKU: Datahub.":::

3. På sidan Översikt i data fabriken klickar du på **författare och övervakare**. En ny flik öppnas i webbläsaren. Klicka på **övervakaren**

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Skärm bild som visar meny alternativet övervaka.":::

4. Du ser en lista över pipeline-körningar som ingår i körningen av väder jobb. Klicka på det jobb som du vill samla in loggar för
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Skärm bild som visar meny alternativet pipelines-körningar och det valda jobbet.":::

5. På sidan Översikt över pipelinen visas en lista över aktivitets körningar. Anteckna körnings-ID: na för de aktiviteter som du vill samla in loggar för
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Skärm bild som visar listan över aktivitets körningar.":::

6. Gå tillbaka till resurs gruppen FarmBeats i Azure Portal och klicka på lagrings kontot med namnet **datahublogs-xxxx**
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Skärm bild som visar lagrings kontot med namnet datahublogs-XXXX.":::

7. Klicka på **behållare**  ->  **adfjobs**. I rutan Sök anger du det jobb körnings-ID som du antecknade i steg 5 ovan.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="Project-FarmBeats":::

8. Sök resultatet innehåller mappen som innehåller de loggar som hör till jobbet. Hämta loggarna och skicka den till farmbeatssupport@microsoft.com för att få hjälp med att felsöka problemet.