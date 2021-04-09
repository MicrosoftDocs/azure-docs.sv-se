---
title: Så här schemalägger du Azure-SSIS Integration Runtime
description: I den här artikeln beskrivs hur du schemalägger start och stopp av Azure-SSIS Integration Runtime med hjälp av Azure Data Factory.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 40c3b8ab228c93fd4c681281d89d16f88ddf30f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384375"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Så startar och stoppar du Azure-SSIS Integration Runtime enligt ett schema

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs hur du schemalägger start och stopp av Azure-SSIS Integration Runtime (IR) med Azure Data Factory (ADF). Azure-SSIS IR är ADF Compute-resurs dedikerad för att köra SQL Server Integration Services-paket (SSIS). Att köra Azure-SSIS IR har en kostnad som är kopplad till den. Därför vill du normalt bara köra IR när du behöver köra SSIS-paket i Azure och stoppa din IR när du inte behöver den längre. Du kan använda ADF-/app (användar gränssnitt) eller Azure PowerShell för att [manuellt starta eller stoppa IR-nätverket](manage-azure-ssis-integration-runtime.md).

Alternativt kan du skapa webb aktiviteter i ADF-pipeliner för att starta/stoppa din IR enligt schema, t. ex. Starta den i morgon innan du kör dina dagliga ETL-arbetsbelastningar och stoppar den i eftermiddag när de är klara.  Du kan också kedja en kör SSIS-paket-aktivitet mellan två webb aktiviteter som startar och stoppar din IR, så att IR startar/stoppas på begäran, precis i tid före/efter ditt paket körning. Mer information om hur du kör SSIS-paket aktivitet finns i [köra ett SSIS-paket med aktiviteten kör SSIS-paket i ADF pipeline](how-to-invoke-ssis-package-ssis-activity.md) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar
Om du inte redan har etablerat ditt Azure-SSIS IR kan du etablera det genom att följa anvisningarna i [självstudien](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Skapa och schemalägga ADF-pipelines som startar och stoppar Azure-SSIS IR
I det här avsnittet visas hur du använder webb aktiviteter i ADF-pipelines för att starta/stoppa din Azure-SSIS IR enligt schema eller starta & stoppa den på begäran. Vi hjälper dig att skapa tre pipeliner: 

1. Den första pipelinen innehåller en webb aktivitet som startar Azure-SSIS IR. 
2. Den andra pipelinen innehåller en webb aktivitet som stoppar din Azure-SSIS IR.
3. Den tredje pipelinen innehåller en SSIS-paket-aktivitet som är kedjad mellan två webb aktiviteter som startar/stoppar din Azure-SSIS IR. 

När du har skapat och testat dessa pipeliner kan du skapa en schema utlösare och koppla den till en pipeline. Schema utlösaren definierar ett schema för att köra den associerade pipelinen. 

Du kan t. ex. skapa två utlösare, den första är schemalagd att köras varje dag kl. 6 och associeras med den första pipelinen, medan den andra är schemalagd att köras dagligen med 6 PM och som är associerad med den andra pipelinen.  På så sätt har du en period på mellan 6 och 6 timmar varje dag när IR körs, redo att köra dina dagliga ETL-arbetsbelastningar.  

Om du skapar en tredje utlösare som är schemalagd att köras varje dag vid midnatt och som är associerad med den tredje pipelinen, kommer den pipelinen att köras vid midnatt varje dag, med början på din IR precis innan paket körningen, därefter köra ditt paket och omedelbart stoppa din IR precis efter att paketet har körts, så din IR kommer inte att köra idly.

### <a name="create-your-adf"></a>Skapa din ADF

1. Logga in på [Azure Portal](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. På sidan **ny data fabrik** anger du **MyAzureSsisDataFactory** som **namn**. 
      
   ![Sidan Ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på din ADF måste vara globalt unikt. Om du får följande fel meddelande ändrar du namnet på din ADF (t. ex. Dittnamnmyazuressisdatafactory) och försöker skapa det igen. Se artikeln [Data Factory namngivnings regler](naming-rules.md) för att lära dig mer om namngivnings regler för ADF-artefakter.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Välj din Azure- **prenumeration** där du vill skapa din ADF. 
5. För **resurs grupp** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan. 
   - Välj **Skapa ny** och ange namnet på den nya resurs gruppen.   
         
   Mer information om resurs grupper finns i [använda resurs grupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md) .
   
6. För **version** väljer du **v2** .
7. För **plats** väljer du en av de platser som stöds för att skapa ADF i den nedrullningsbara listan.
8. Välj **fäst till instrumentpanelen**.     
9. Klicka på **Skapa**.
10. På Azure-instrumentpanelen visas följande panel med status: **distribuera Data Factory**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. När du har skapat den här sidan kan du se din ADF-sida på det sätt som visas nedan.
   
    ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Klicka på **redigera & övervakare** för att starta ADF-användargränssnitt/-app på en separat flik.

### <a name="create-your-pipelines"></a>Skapa dina pipelines

1. På sidan **Kom igång** väljer du **skapa pipeline**. 

   ![Sidan Kom igång](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. I verktygs lådan **aktiviteter** expanderar du menyn **allmänt** och drar & släpper en **webb** aktivitet på pipelinens design yta. På fliken **Allmänt** i fönstret aktivitets egenskaper ändrar du aktivitets namnet till **startMyIR**. Växla till fliken **Inställningar** och utför följande åtgärder.

    1. För **URL** anger du följande URL för REST API som börjar Azure-SSIS IR, ersätter, `{subscriptionId}` , `{resourceGroupName}` `{factoryName}` och `{integrationRuntimeName}` med de faktiska värdena för din IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` du kan också kopiera & klistra in resurs-ID: t för IR-filen från sidan övervakning på ADF UI/app för att ersätta följande del av URL: en: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![SSIS IR-resurs-ID](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. För **metod** väljer du **post**. 
    3. I **brödtext** anger du `{"message":"Start my IR"}` . 
    4. För **autentisering** väljer du **MSI** för att använda den hanterade identiteten för din ADF, se [hanterad identitet för Data Factory](./data-factory-service-identity.md) artikel för mer information.
    5. För **resurs** anger du `https://management.azure.com/` .
    
       ![ADF-webbaktivitets schema SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Klona den första pipelinen för att skapa en andra, ändra aktivitets namnet till **stopMyIR** och Ersätt följande egenskaper.

    1. För **URL** anger du följande URL för REST API som slutar Azure-SSIS IR, ersätter, `{subscriptionId}` , `{resourceGroupName}` `{factoryName}` och `{integrationRuntimeName}` med de faktiska värdena för din IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. I **brödtext** anger du `{"message":"Stop my IR"}` . 

4. Skapa en tredje pipeline, dra & släppa en **kör SSIS-paket** -aktivitet **från aktivitets verktygs lådan i** pipelinens designer-yta och konfigurera den enligt instruktionerna i [anropa ett SSIS-paket med aktiviteten kör SSIS-paket i ADF](how-to-invoke-ssis-package-ssis-activity.md) -artikeln.  Du kan också använda en **lagrad procedur** aktivitet i stället och konfigurera den enligt instruktionerna i [anropa ett SSIS-paket med hjälp av en lagrad procedur aktivitet i ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) -artikeln.  Sedan kopplar du aktiviteten kör SSIS-paket/lagrade procedurer mellan två webb aktiviteter som startar/stoppar din IR, som liknar dessa webb aktiviteter i de första/andra pipelinen.

   ![ADF-webbaktivitet på begäran SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Tilldela den hanterade identiteten för din ADF en **deltagar** roll till sig själv, så att webb aktiviteter i sina pipeliner kan anropa REST API för att starta/stoppa Azure-SSIS IRS som etablerades i den.  På sidan ADF i Azure Portal klickar du på **åtkomst kontroll (IAM)**, klickar på **+ Lägg till roll tilldelning** och gör sedan följande åtgärder på bladet **Lägg till roll tilldelning** .

    1. För **roll** väljer du **deltagare**. 
    2. För **tilldela åtkomst till** väljer du **Azure AD-användare, grupp eller tjänstens huvud namn**. 
    3. För **Välj** söker du efter ditt ADF-namn och väljer det. 
    4. Klicka på **Spara**.
    
   ![ADF-hanterad identitets roll tilldelning](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Verifiera dina ADF-och alla inställningar för pipelinen genom att klicka på **validera alla/verifiera** i verktygsfältet fabrik/pipeline. Stäng **resultatet av verifieringen av fabrik/pipeline** genom att klicka på **>>** knappen.  

   ![Verifiera pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Testa dina pipelines

1. Välj **testa körning** i verktygsfältet för varje pipeline och visa **utdatafönstret** i det nedre fönstret. 

   ![Kör test](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Starta SQL Server Management Studio (SSMS) för att testa den tredje pipelinen. Utför följande åtgärder i fönstret **Anslut till Server** . 

    1. För **Server namn** anger **&lt; du Server namnet &gt; . Database.Windows.net**.
    2. Välj **alternativ >>**.
    3. För **Anslut till databas** väljer du **SSISDB**.
    4. Välj **Anslut**. 
    5. Expandera **Integration Services-kataloger**  ->  **SSISDB** – > > **projekt** – > dina SSIS Project->- **paket**. 
    6. Högerklicka på det angivna SSIS-paketet för att köra och välj **rapporter**  ->  **standard rapporter**  ->  **alla körningar**. 
    7. Kontrol lera att den kördes. 

   ![Verifiera körning av SSIS-paket](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Schemalägg dina pipelines

Nu när dina pipelines fungerar som förväntat kan du skapa utlösare för att köra dem vid angiven cadences. Mer information om hur du kopplar utlösare med pipelines finns i [Utlös pipeline i en schema](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) artikel.

1. I pipeline-verktygsfältet väljer du **utlösare** och sedan **ny/redigera**. 

   ![Skärm bild som visar meny alternativet utlösare > nytt/redigera.](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. I fönstret **Lägg till utlösare** väljer du **+ ny**.

   ![Lägg till utlösare – ny](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Gör följande i fönstret **Ny utlösare** : 

    1. I **namn** anger du ett namn för utlösaren. I följande exempel är **Kör varje dag** Utlösarens namn. 
    2. I **typ** väljer du **schema**. 
    3. För **start datum (UTC)** anger du start datum och-tid i UTC. 
    4. För **upprepning** anger du en takt för utlösaren. I följande exempel är det **dagligen** en gång. 
    5. För **slut** väljer du **ingen end** eller anger slutdatum och-tid efter att ha valt **datum**. 
    6. Välj **aktive rad** för att aktivera utlösaren direkt efter att du har publicerat hela ADF-inställningarna. 
    7. Välj **Nästa**.

   ![Utlös > ny/redigera](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. På sidan **Utlös körnings parametrar** granskar du eventuella varningar och väljer **Slutför**. 
5. Publicera hela ADF-inställningarna genom att välja **publicera alla** i fabriks verktygsfältet. 

   ![Publicera alla](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Övervaka dina pipelines och utlösare i Azure Portal

1. Om du vill övervaka Utlös ande körningar och pipeline-körningar använder du fliken **övervaka** till vänster om ADF UI/app. Detaljerade anvisningar finns i [övervaka pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline) -artikeln.

   ![Pipelinekörningar](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Om du vill visa de aktivitets körningar som är associerade med en pipeline-körning väljer du den första länken (**Visa aktivitets körningar**) i kolumnen **åtgärder** . För den tredje pipelinen visas tre aktivitets körningar, en för varje kedjad aktivitet i pipelinen (webb aktivitet för att starta IR, lagrad procedur aktivitet för att köra ditt paket och webb aktivitet för att stoppa IR). Om du vill visa pipelinen körs igen väljer du länken **pipelines** överst.

   ![Aktivitetskörningar](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Om du vill visa utlösarens körningar väljer du **Utlös ande körningar** i list rutan under **pipeline-körningar** överst. 

   ![Utlösarkörning](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Övervaka dina pipelines och utlösare med PowerShell

Använd skript som följande exempel för att övervaka pipelines och utlösare.

1. Hämta status för en pipeline-körning.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Hämta information om en utlösare.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Hämta status för en Utlös ande körning.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Skapa och schemalägg Azure Automation-runbook som startar/stoppar Azure-SSIS IR

I det här avsnittet får du lära dig att skapa Azure Automation Runbook som kör PowerShell-skript, genom att starta/stoppa din Azure-SSIS IR enligt ett schema.  Detta är användbart när du vill köra ytterligare skript innan/efter att du startat/stoppat IR för för bearbetning.

### <a name="create-your-azure-automation-account"></a>Skapa ditt Azure Automation-konto

Om du inte redan har ett Azure Automation konto skapar du ett genom att följa anvisningarna i det här steget. Detaljerade anvisningar finns i artikeln [skapa ett Azure Automation konto](../automation/automation-quickstart-create-account.md) . Som en del av det här steget skapar du ett **Kör som** -konto i Azure (ett tjänst huvud namn i din Azure Active Directory) och tilldelar det till en **deltagar** roll i din Azure-prenumeration. Se till att det är samma prenumeration som innehåller din ADF med Azure SSIS IR. Azure Automation kommer att använda det här kontot för att autentisera till Azure Resource Manager och arbeta med dina resurser. 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. För närvarande stöds inte ADF-gränssnittet/-appen i Microsoft Edge-och Google Chrome-webbläsare.
2. Logga in på [Azure Portal](https://portal.azure.com/).    
3. Välj **nytt** på den vänstra menyn, Välj **övervakning och hantering** och välj **Automation**. 

   ![Skärm bild som visar alternativet Övervakning och hantering > Automation.](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. I fönstret **Lägg till Automation-konto** utför du följande åtgärder.

    1. I **namn** anger du ett namn för ditt Azure Automation-konto. 
    2. För **prenumeration** väljer du den prenumeration som har din ADF med Azure-SSIS IR. 
    3. För **resurs grupp** väljer du **Skapa ny** för att skapa en ny resurs grupp eller **Använd en befintlig** för att välja en befintlig. 
    4. För **plats** väljer du en plats för ditt Azure Automation-konto. 
    5. Bekräfta **skapa kör som-konto i Azure** som **Ja**. Ett huvud namn för tjänsten skapas i Azure Active Directory och tilldelas en **deltagar** roll i din Azure-prenumeration.
    6. Välj **Fäst på instrument panelen** för att visa den permanent på Azure-instrumentpanelen. 
    7. Välj **Skapa**. 

   ![New-> Övervakning och hantering-> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Du ser distributions status för ditt Azure Automation-konto på Azure-instrumentpanelen och-aviseringar. 
    
   ![Distribuera Automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Du kommer att se start sidan för ditt Azure Automation-konto när det har skapats. 

   ![Start sida för Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importera ADF-moduler

1. Välj **moduler** i avsnittet **delade resurser** på den vänstra menyn och kontrol lera om du har **AZ. DataFactory**  +  **AZ. Profile** i listan över moduler.

   ![Verifiera nödvändiga moduler](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Om du inte har **AZ. DataFactory** går du till modulen PowerShell-galleriet för [AZ. DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), väljer **distribuera till Azure Automation**, väljer ditt Azure Automation konto och väljer sedan **OK**. Gå tillbaka till Visa **moduler** i avsnittet **delade resurser** på den vänstra menyn och vänta tills du ser **status** för modulen **AZ. DataFactory** har ändrats till **tillgänglig**.

    ![Verifiera Data Factory-modulen](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Om du inte har **AZ. Profile** går du till PowerShell-galleriet för [AZ. Profile-modulen](https://www.powershellgallery.com/packages/Az.profile/), väljer **distribuera till Azure Automation**, väljer ditt Azure Automation-konto och väljer sedan **OK**. Gå tillbaka till Visa **moduler** i avsnittet **delade resurser** på den vänstra menyn och vänta tills du ser **status** för modulen **AZ. Profile** ändrad till **tillgänglig**.

    ![Verifiera modulen profil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Skapa din PowerShell-Runbook

I följande avsnitt beskrivs hur du skapar en PowerShell-Runbook. Skriptet som är associerat med din Runbook startar eller stoppar Azure-SSIS IR baserat på det kommando som du anger som **Åtgärds** parameter. Det här avsnittet innehåller ingen fullständig information om hur du skapar en Runbook. Mer information finns i [skapa en Runbook](../automation/automation-quickstart-create-runbook.md) -artikel.

1. Växla till fliken **Runbooks** och välj **+ Lägg till en Runbook** från verktygsfältet. 

   ![Skärm bild som visar knappen Lägg till en Runbook.](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Välj **skapa en ny Runbook** och utför följande åtgärder: 

    1. Som **namn** anger du **StartStopAzureSsisRuntime**.
    2. För **Runbook-typ** väljer du **PowerShell**.
    3. Välj **Skapa**.
    
   ![Knappen Lägg till en Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Kopiera & klistra in följande PowerShell-skript i fönstret för Runbook-skriptet. Spara och publicera din Runbook genom att använda knapparna **Spara** och **publicera** i verktygsfältet. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Redigera PowerShell-Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Testa din Runbook genom att välja **Start** -knappen i verktygsfältet. 

   ![Knappen starta Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. I fönstret **starta Runbook** utför du följande åtgärder: 

    1. För **resurs grupp namn** anger du namnet på den resurs grupp som innehåller din ADF med Azure-SSIS IR. 
    2. För **data fabriks namn** anger du namnet på din ADF med Azure-SSIS IR. 
    3. För **AZURESSISNAME** anger du namnet på Azure-SSIS IR. 
    4. Vid **åtgärd** anger du **Start**. 
    5. Välj **OK**.  

   ![Starta Runbook-fönstret](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Välj panelen **utdata** i fönstret jobb. I fönstret utdata väntar du tills meddelandet **# # # # har slutförts # # # # #** efter att du ser **# # # # # som börjar # #**# #. Det tar cirka 20 minuter att starta Azure-SSIS IR. Stäng **jobb** fönstret och gå tillbaka till **Runbook** -fönstret.

   ![Skärm bild som visar panelen utdata.](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Upprepa föregående två steg med **stopp** som värde för **åtgärden**. Starta din Runbook igen genom att välja **Start** -knappen i verktygsfältet. Ange resurs grupp, ADF och Azure-SSIS IR namn. Vid **åtgärd** anger du **stopp**. I fönstret utdata väntar du tills meddelandet **# # # # har slutförts # # # # #** när du ser **# # # # # Stop # # # #**. Att stoppa Azure-SSIS IR tar inte så lång tid att starta. Stäng **jobb** fönstret och gå tillbaka till **Runbook** -fönstret.

8. Du kan också utlösa din Runbook via en webhook som du kan skapa genom att välja meny alternativet **Webhooks** eller enligt ett schema som du kan skapa genom att välja meny alternativet **scheman** som anges nedan.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Skapa scheman för din Runbook för att starta/stoppa Azure-SSIS IR

I föregående avsnitt har du skapat din Azure Automation Runbook som antingen kan starta eller stoppa Azure-SSIS IR. I det här avsnittet ska du skapa två scheman för din Runbook. När du konfigurerar det första schemat anger du **Start** för **åtgärd**. På samma sätt anger du **åtgärden** **stoppa** för att konfigurera den andra. Detaljerade anvisningar om hur du skapar scheman finns i [skapa en schema](../automation/shared-resources/schedules.md#create-a-schedule) artikel.

1. I **Runbook** -fönstret väljer du **scheman** och sedan **+ Lägg till ett schema** i verktygsfältet. 

   ![Azure SSIS IR – Startad](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. I fönstret **Schemalägg Runbook** gör du följande: 

    1. Välj **Länka ett schema till din Runbook**. 
    2. Välj **skapa ett nytt schema**.
    3. I fönstret **nytt schema** anger du **Start-IR dagligen** för **namn**. 
    4. För **starter** anger du en tid som är några minuter efter den aktuella tiden. 
    5. För **upprepning** väljer du **återkommande**. 
    6. För **Upprepa varje** anger du **1** och väljer **dag**. 
    7. Välj **Skapa**. 

   ![Schema för Azure SSIS IR-start](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Växla till fliken **parametrar och kör inställningar** . Ange resurs grupp, ADF och Azure-SSIS IR namn. Vid **åtgärd** anger du **Start** och väljer **OK**. Välj **OK** igen för att Visa schema på sidan **scheman** i din Runbook. 

   ![Skärm bild som markerar fältet åtgärd.](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Upprepa föregående två steg för att skapa ett schema med namnet **stoppa IR dagligen**. Ange en tid på minst 30 minuter efter den tid som du har angett för **Start-IR daglig** schema. Vid **åtgärd** anger du **stopp** och väljer **OK**. Välj **OK** igen för att Visa schema på sidan **scheman** i din Runbook. 

5. I **Runbook** -fönstret väljer du **jobb** på den vänstra menyn. Du bör se de jobb som skapats av dina scheman vid de angivna tiderna och deras status. Du kan se jobb informationen, till exempel utdata, på samma sätt som du har sett när du har testat din Runbook. 

   ![Schema för att stjärna Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. När du är klar med testet inaktiverar du dina scheman genom att redigera dem. Välj **scheman** på den vänstra menyn, Välj **Start-IR daglig/stoppa IR dagligen** och välj **Nej** för **aktive rad**. 

## <a name="next-steps"></a>Nästa steg
Se följande blogg inlägg:
-   [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF-pipeline](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)