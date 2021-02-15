---
title: Läs in data från SAP Business Warehouse
description: Använd Azure Data Factory för att kopiera data från SAP Business Warehouse (BW)
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 3dabb6d5df0a74cc7ae2fb8b381ad9e0dfe04e63
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370707"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln visar hur du använder Azure Data Factory för att kopiera data från SAP Business Warehouse (BW) via öppen hubb till Azure Data Lake Storage Gen2. Du kan använda en liknande process för att kopiera data till andra [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Allmän information om hur du kopierar data från SAP BW, inklusive SAP BW öppen hubb integrering och delta extraherings flöde, finns i [Kopiera data från SAP Business Warehouse via öppna hubb med Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Förutsättningar

- **Azure Data Factory**: om du inte har något följer du stegen för att [skapa en data fabrik](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW-OHD (Open Hub destination) med måltypen "databas tabell"**: om du vill skapa en OHD eller kontrol lera att din OHD har kon figurer ATS korrekt för Data Factory-integrering, se avsnittet [SAP BW Open Hub destination Configurations](#sap-bw-open-hub-destination-configurations) i den här artikeln.

- **Den SAP BW användaren behöver följande behörigheter**:

  - Auktorisering för fjärr funktions anrop (RFC) och SAP BW.
  - Behörigheter till aktiviteten "kör" för **S_SDSAUTH** Authorization-objektet.

- **En [egen värd för integration Runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) med SAP .net Connector 3,0**. Följ dessa installations steg:

  1. Installera och registrera integration runtime, version 3,13 eller senare med egen värd. (Den här processen beskrivs längre fram i den här artikeln.)

  2. Hämta [64-bitars SAP-anslutaren för Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) från SAP: s webbplats och installera den på samma dator som den lokala IR-enheten. Under installationen ser du till att du väljer **Installera sammansättningar i GAC** i dialog rutan **valfria installations steg** , som följande bild visar:

     ![Dialog rutan konfigurera SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Gör en fullständig kopia från SAP BW öppna hubben

Gå till datafabriken i Azure-portalen. Välj **författar & övervakare** för att öppna data Factory användar gränssnittet på en separat flik.

1. På sidan **nu sätter vi igång** väljer du **Kopiera data** för att öppna kopiera datas verktyget.

2. På sidan **Egenskaper** anger du ett **aktivitets namn** och väljer sedan **Nästa**.

3. På sidan **käll data lager** väljer du **+ Skapa ny anslutning**. Välj **SAP BW öppna hubben** från kopplings galleriet och välj sedan **Fortsätt**. Om du vill filtrera anslutningarna kan du skriva **SAP** i sökrutan.

4. På sidan **ange SAP BW öppna hubben anslutning** följer du stegen nedan för att skapa en ny anslutning.

   ![Skapa SAP BW öppna Hub-länkad tjänst sida](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. I listan **Anslut via integration runtime** väljer du en befintlig egen värd för IR. Eller Välj att skapa en om du inte har en ännu.

      Om du vill skapa en ny egen IR-anslutning väljer du **+ ny** och väljer sedan **egen värd**. Ange ett **namn** och välj sedan **Nästa**. Välj **Express installation** för att installera på den aktuella datorn eller följ de **manuella installations** stegen som tillhandahålls.

      Se till att du har SAP Connector för Microsoft .NET 3,0 installerat på samma dator som den lokala [IR-](#prerequisites)körningen körs på.

   2. Fyll i SAP BW **Server namn**, **system nummer**, **klient-ID,** **språk** (om annat än **en**), **användar namn** och **lösen ord**.

   3. Välj **Testa anslutning** för att validera inställningarna och välj sedan **Slutför**.

   4. En ny anslutning skapas. Välj **Nästa**.

5. På sidan **Välj mål med öppen hubb** bläddrar du till de öppna mål som är tillgängliga i din SAP BW. Välj den OHD som du vill kopiera data från och välj sedan **Nästa**.

   ![Välj SAP BW öppna hubb mål tabellen](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Ange ett filter om du behöver ett. Om din OHD endast innehåller data från en enskild DTP-körning (Data Transfer process) med ett ID för begäran, eller om du är säker på att DTP har slutförts och du vill kopiera data, avmarkerar du kryss rutan **exkludera senaste begäran** .

   Läs mer om de här inställningarna i avsnittet [SAP BW Open Hub destination Configurations](#sap-bw-open-hub-destination-configurations) i den här artikeln. Välj **Verifiera** för att dubbelkolla vilka data som ska returneras. Välj sedan **Nästa**.

   ![Konfigurera SAP BW öppna Hubbs filter](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. På sidan **mål data lager** väljer du **+ Skapa ny anslutning**  >  **Azure Data Lake Storage Gen2**  >  **Fortsätt**.

8. På sidan **ange Azure Data Lake Storage anslutning** följer du de här stegen för att skapa en anslutning.

   ![Skapa en ADLS Gen2 länkad tjänst sida](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Välj ditt Data Lake Storage Gen2-kapabla konto i list rutan **namn** .
   2. Välj **Slutför** för att skapa anslutningen. Välj sedan **Nästa**.

9. På sidan **Välj utdatafil eller mapp** anger du **copyfromopenhub** som namn på mappen utdata. Välj sedan **Nästa**.

   ![Sidan Välj mapp för utdata](media/load-sap-bw-data/choose-output-folder.png)

10. På sidan **fil format inställning** väljer du **Nästa** för att använda standardinställningarna.

    ![Sidan Ange format för mottagare](media/load-sap-bw-data/specify-sink-format.png)

11. På sidan **Inställningar** expanderar du **prestanda inställningar**. Ange ett värde för **graden av kopierings parallellitet** , till exempel 5, som ska läsas in från SAP BW parallellt. Välj sedan **Nästa**.

    ![Konfigurera kopierings inställningar](media/load-sap-bw-data/configure-copy-settings.png)

12. Granska inställningarna på sidan **Sammanfattning** . Välj sedan **Nästa**.

13. På sidan **distribution** väljer du **övervakare** för att övervaka pipelinen.

    ![Distributionssida](media/load-sap-bw-data/deployment.png)

14. Observera att fliken **övervaka** till vänster på sidan väljs automatiskt. Kolumnen **åtgärder** innehåller länkar för att Visa aktivitets körnings information och köra pipelinen på nytt.

    ![Vyn pipeline-övervakning](media/load-sap-bw-data/pipeline-monitoring.png)

15. Om du vill visa aktivitets körningar som är associerade med pipeline-körningen väljer du **Visa aktivitets körningar** i kolumnen **åtgärder** . Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till pipelinen – körs väljer du länken **pipelines** överst. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).

    ![Skärmen för aktivitets övervakning](media/load-sap-bw-data/activity-monitoring.png)

16. Om du vill övervaka körnings informationen för varje kopierings aktivitet väljer du länken **information** , som är en glasögon-ikon under **åtgärder** i vyn aktivitets övervakning. Tillgänglig information inkluderar data volymen som kopierats från källan till mottagaren, data flöde, körnings steg och varaktighet samt konfigurationer som används.

    ![Information om aktivitets övervakning](media/load-sap-bw-data/activity-monitoring-details.png)

17. Om du vill visa det **maximala ID: t för begäran** går du tillbaka till vyn aktivitets övervakning och väljer **utdata** under **åtgärder**.

    ![Skärmen utdata för aktivitet](media/load-sap-bw-data/activity-output.png)

    ![Vyn information om aktivitets utdata](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Stegvis kopiering från SAP BW öppen hubb

> [!TIP]
> Se [SAP BWs extraherings flöde för öppen Hubbs koppling](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) för att lära dig hur SAP BW öppna Hub-anslutningen i Data Factory kopierar stegvisa Data från SAP BW. Den här artikeln kan också hjälpa dig att förstå grundläggande anslutnings konfiguration.

Nu ska vi fortsätta konfigurera stegvis kopiering från SAP BW öppna hubben.

I en avancerad kopia används en metod för "hög vatten märke" som baseras på **begäran-ID**. Detta ID genereras automatiskt i SAP BW Open Hub-destinationen av DTP. Följande diagram visar det här arbets flödet:

![Flödes schema för arbets flöde för stegvis kopiering](media/load-sap-bw-data/incremental-copy-workflow.png)

På sidan för att **komma igång** med Data Factory väljer du **skapa pipeline från mall** för att använda den inbyggda mallen.

1. Sök efter **SAP BW** för att hitta och välja den **stegvisa kopian från SAP BW till Azure Data Lake Storage Gen2** mall. Den här mallen kopierar data till Azure Data Lake Storage Gen2. Du kan använda ett liknande arbets flöde för att kopiera till andra typer av mottagare.

2. På mallens huvud sida väljer eller skapar du följande tre anslutningar och väljer sedan **Använd den här mallen** i det nedre högra hörnet i fönstret.

   - **Azure Blob Storage**: i den här genom gången använder vi Azure Blob Storage för att lagra den övre vatten märket, vilket är *Max-ID för kopierad förfrågan*.
   - **SAP BW öppen hubb**: det här är källan som data ska kopieras från. Läs den tidigare genom gången av fullständig kopiering för detaljerad konfiguration.
   - **Azure Data Lake Storage Gen2**: det här är den mottagare som data ska kopieras till. Läs den tidigare genom gången av fullständig kopiering för detaljerad konfiguration.

   ![Stegvis kopiering från SAP BW mall](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Den här mallen skapar en pipeline med följande tre aktiviteter och gör dem sammankopplade: *Lookup*, *Kopiera data* och *Web*.

   Gå till fliken pipeline- **parametrar** . Du ser alla konfigurationer som du måste ange.

   ![Stegvis kopiering från SAP BW konfiguration](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Ange namnet på den öppna Hub-tabellen som data ska kopieras från.

   - **Data_Destination_Container**: ange mål Azure Data Lake Storage Gen2 behållare för att kopiera data till. Om behållaren inte finns skapar Data Factory kopierings aktiviteten en under körningen.
  
   - **Data_Destination_Directory**: Ange mappsökvägen under den Azure Data Lake Storage Gen2 behållare för att kopiera data till. Om sökvägen inte finns skapar Data Factory kopierings aktiviteten en sökväg under körningen.
  
   - **HighWatermarkBlobContainer**: Ange den behållare där värdet för hög vatten märket ska lagras.

   - **HighWatermarkBlobDirectory**: Ange mappsökvägen under behållaren där du vill lagra det höga värdet för vatten märket.

   - **HighWatermarkBlobName**: Ange BLOB-namnet för att lagra det höga värdet för vatten märket, till exempel `requestIdCache.txt` . I Blob Storage går du till motsvarande sökväg för HighWatermarkBlobContainer + HighWatermarkBlobDirectory + HighWatermarkBlobName, till exempel *container/sökväg/requestIdCache.txt*. Skapa en blob med Content 0.

      ![Blobinnehåll](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: i den här mallen använder vi webactivity för att anropa Azure Logic Apps för att ange värdet för högsta vatten märket i Blob Storage. Du kan också använda Azure SQL Database för att lagra den. Använd en lagrad procedur aktivitet för att uppdatera värdet.

      Du måste först skapa en Logic-app, som följande bild visar. Klistra sedan in den i **http post-URL: en**.

      ![Konfiguration av Logic app](media/load-sap-bw-data/logic-app-config.png)

      1. Gå till Azure-portalen. Välj en ny **Logic Apps** tjänst. Välj **+ Tom Logic app** för att gå till **Logic Apps designer**.

      2. Skapa en utlösare av **när en HTTP-begäran tas emot**. Ange texten för HTTP-begäran enligt följande:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Lägg till en åtgärd för att **skapa BLOB** . För **mappsökväg** och **BLOB-namn** använder du samma värden som du konfigurerade tidigare i *HighWatermarkBlobContainer + HighWatermarkBlobDirectory* och *HighWatermarkBlobName*.

      4. Välj **Spara**. Kopiera sedan värdet för **http post-URL** som ska användas i Data Factory pipelinen.

4. När du har angett parametrarna för Data Factory pipeline väljer du **Felsök**  >  **Slutför** för att anropa en körning för att verifiera konfigurationen. Eller Välj **publicera** för att publicera alla ändringar och välj sedan **Lägg till utlösare** för att köra en körning.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW konfiguration av öppet nav mål

I det här avsnittet beskrivs konfiguration av SAP BW sidan som används för att använda SAP BW öppna hubben i Data Factory för att kopiera data.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurera delta extrahering i SAP BW

Om du behöver både historisk kopiering och stegvis kopiering eller endast stegvis kopiering konfigurerar du delta extrahering i SAP BW.

1. Skapa målet för den öppna hubben. Du kan skapa OHD i SAP Transaction RSA1, som automatiskt skapar den obligatoriska omvandlingen och data överförings processen. Använd följande inställningar:

   - **ObjectType**: du kan använda valfri objekt typ. Här använder vi **Infocube** som exempel.
   - **Måltyp**: Välj **databas tabell**.
   - **Nyckel för tabellen**: Välj **teknisk nyckel**.
   - **Extrahering**: Välj **Behåll data och infoga poster i tabellen**.

   ![Dialog rutan skapa SAP BW OHD delta extrahering](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Dialog rutan skapa SAP BW OHD delta2 extrahering](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Du kan öka antalet parallella SAP-arbetsprocesser för DTP:

   ![Skärm bilden visar inställningar för parallell bearbetning där du kan välja antalet parallella processer för D T P.](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Schemalägg DTP i process kedjor.

   En delta-DTP för en kub fungerar bara om nödvändiga rader inte har komprimerats. Se till att BW-kubens komprimering inte körs innan DTP till den öppna Hub-tabellen. Det enklaste sättet att göra detta är att integrera DTP i dina befintliga process kedjor. I följande exempel infogas DTP (till OHD) i process kedjan mellan stegen *Justera* (samlad sammanställning) och *Komprimera* (kub komprimering).

   ![Skapa SAP BW process kedje flödes diagram](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurera fullständig extrahering i SAP BW

Förutom delta extrahering kanske du vill ha en fullständig extrahering av samma SAP BW InfoProvider. Detta gäller vanligt vis om du vill göra en fullständig kopia men inte stegvis, eller om du vill [Synkronisera om delta extrahering](#resync-delta-extraction).

Du kan inte ha mer än en DTP för samma OHD. Så du måste skapa ytterligare en OHD innan delta extrahering.

![Skapa SAP BW OHD full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

För en fullständig belastnings OHD väljer du olika alternativ än för delta extrahering:

- I OHD: ange **extraherings** alternativet för att **ta bort data och infoga poster**. Annars extraheras data flera gånger när du upprepar DTP i en BW process-kedja.

- I DTP: Ställ in **extraherings läge** på **fullständig**. Du måste ändra den automatiskt skapade DTP från **delta** till **fullständig** omedelbart efter att OHD har skapats, eftersom den här bilden visar:

   ![Dialog rutan skapa SAP BW OHD som kon figurer ATS för "fullständig" extrahering](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- I BW för öppen hubb i Data Factory: inaktivera **utelämna senaste begäran**. Annars kommer inget att extraheras.

Du kör vanligt vis hela DTP manuellt. Du kan också skapa en process kedja för hela DTP. Det är vanligt vis en separat kedja som är oberoende av dina befintliga process kedjor. I båda fallen *kontrollerar du att DTP är färdig innan du startar extraheringen genom att använda Data Factory kopiera*. Annars kopieras bara ofullständiga data.

### <a name="run-delta-extraction-the-first-time"></a>Kör delta extrahering första gången

Den första delta extraktionen är tekniskt en *fullständig extrahering*. Som standard utesluter SAP BW öppna Hub Connector den senaste begäran när data kopieras. För den första delta extraheringen extraheras inga data av Data Factory kopierings aktiviteten förrän en efterföljande DTP genererar delta data i tabellen med ett separat ID för begäran. Det finns två sätt att undvika det här scenariot:

- Inaktivera alternativet för att **undanta senaste begäran** för den första delta extraheringen. Kontrol lera att den första delta-DTP är färdig innan du startar delta extraheringen första gången.
-  Använd proceduren för att synkronisera om delta extraheringen enligt beskrivningen i nästa avsnitt.

### <a name="resync-delta-extraction"></a>Omsynkronisering av delta extrahering

Följande scenarier ändrar data i SAP BW kuber men beaktas inte av delta-DTP:

- SAP BW selektiv borttagning (av rader med valfritt filter villkor)
- Borttagning av SAP BW begär Anden (av felaktiga begär Anden)

Ett SAP Open Hub-mål är inte ett data mart-kontrollerat data mål (i alla SAP BW support paket sedan 2015). Så du kan ta bort data från en kub utan att ändra data i OHD. Du måste sedan synkronisera om data i kuben med Data Factory:

1. Kör en fullständig extrahering i Data Factory (genom att använda en fullständig DTP i SAP).
2. Ta bort alla rader i den öppna Hub-tabellen för delta-DTP.
3. Ange statusen för delta-DTP som ska **hämtas**.

Därefter fungerar alla efterföljande delta-DTPs och Data Factory delta-extraheringar som förväntat.

Om du vill ange statusen för delta-DTP som **hämtas** kan du använda följande alternativ för att köra delta-DTP manuellt:

*Ingen Dataöverföring; Delta status i Källa: Hämtad*

## <a name="next-steps"></a>Nästa steg

Läs mer om SAP BW stöd för öppen hubb Connector:

> [!div class="nextstepaction"]
>[SAP Business Warehouse-anslutning med öppen hubb](connector-sap-business-warehouse-open-hub.md)
