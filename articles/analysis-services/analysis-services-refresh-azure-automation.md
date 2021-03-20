---
title: Uppdatera Azure Analysis Services modeller med Azure Automation | Microsoft Docs
description: I den här artikeln beskrivs hur du kodar modell uppdateringar för Azure Analysis Services med hjälp av Azure Automation.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491255"
---
# <a name="refresh-with-azure-automation"></a>Uppdatera med Azure Automation

Med hjälp av Azure Automation-och PowerShell-Runbooks kan du utföra automatiserade data uppdaterings åtgärder på tabell modeller i Azure Analysis.  

Exemplet i den här artikeln använder [SQLServer PowerShell-modulen](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true). En PowerShell-PowerShell-Runbook som visar hur du uppdaterar en modell finns längre fram i den här artikeln.  

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig Azure Active Directory-token (OAuth 2).  Exemplet i den här artikeln använder ett huvud namn för tjänsten (SPN) för att autentisera till Azure Analysis Services. Läs mer i [skapa ett tjänst huvud namn med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> I följande exempel förutsätter vi att Azure Analysis Services brand väggen är inaktive rad. Om en brand vägg är aktive rad måste den offentliga IP-adressen för begär ande initieraren ingå i en brand Väggs regel.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installera SqlServer-moduler från PowerShell-galleriet.

1. I ditt Azure Automation konto klickar du på **moduler** och **bläddrar sedan till galleriet**.

2. Sök efter **SQLServer** i Sök fältet.

    ![Sök i moduler](./media/analysis-services-refresh-azure-automation/1.png)

3. Välj SqlServer och klicka sedan på **Importera**.
 
    ![Importera modul](./media/analysis-services-refresh-azure-automation/2.png)

4. Klicka på **OK**.
 
### <a name="create-a-service-principal-spn"></a>Skapa ett huvud namn för tjänsten (SPN)

Information om hur du skapar ett huvud namn för tjänsten finns i [skapa ett tjänst huvud namn med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurera behörigheter i Azure Analysis Services
 
Tjänstens huvud namn som du skapar måste ha Server administratörs behörighet på servern. Mer information finns i [lägga till ett huvud namn för tjänsten i Server administratörs rollen](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Utforma Azure Automation Runbook

1. I Automation-kontot skapar du en resurs för **autentiseringsuppgifter** som ska användas för att lagra tjänstens huvud namn på ett säkert sätt.

    ![Skärm bild som visar sidan "autentiseringsuppgifter" med åtgärden "Lägg till en autentiseringsuppgift" vald.](./media/analysis-services-refresh-azure-automation/6.png)

2. Ange information om autentiseringsuppgiften. I **användar namn** anger du program-ID för tjänstens huvud namn (AppID) och anger sedan tjänstens huvud namns hemlighet i **lösen ord**.

    ![Skapa autentiseringsuppgift](./media/analysis-services-refresh-azure-automation/7.png)

3. Importera Automation-runbooken.

    ![Skärm bild som visar sidan "Runbooks" med åtgärden "Importera en Runbook" vald.](./media/analysis-services-refresh-azure-automation/8.png)

4. Bläddra till [Refresh-Model.ps1](#sample-powershell-runbook) -filen, ange ett **namn** och en **Beskrivning** och klicka sedan på **skapa**.

    > [!NOTE]
    > Använd skript från [exemplet PowerShell-Runbook](#sample-powershell-runbook) längst ned i det här dokumentet för att skapa en fil med namnet Refresh-Model.ps1 och spara till en lokal dator som ska importeras till Runbook.

    ![Importera Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. När runbooken har skapats går den automatiskt till redigerings läget.  Välj **Publicera**.

    ![Publicera Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Den autentiseringsuppgifter-resurs som skapades tidigare hämtas av runbooken med hjälp av kommandot **Get-AutomationPSCredential** .  Kommandot skickas sedan till PowerShell-kommandot **Invoke-ProcessASADatabase** för att utföra autentiseringen till Azure Analysis Services.

6. Testa runbooken genom att klicka på **Starta**.

    ![Skärm bild som visar sidan "Översikt" med åtgärden "starta" vald.](./media/analysis-services-refresh-azure-automation/11.png)

7. Fyll i parametrarna **databasename**, **ANALYSISSERVER** och **REFRESHTYPE** och klicka sedan på **OK**. **WEBHOOKDATA** -parametern krävs inte när runbook körs manuellt.

    ![Starta runbooken](./media/analysis-services-refresh-azure-automation/12.png)

Om Runbook-flödet har körts får du ett resultat som liknar följande:

![Lyckad körning](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Använda en fristående Azure Automation Runbook

Runbooken kan konfigureras för att utlösa uppdatering av Azure Analysis Servicess modellen enligt schema.

Detta kan konfigureras på följande sätt:

1. I Automation Runbook klickar du på **scheman** och lägger sedan **till ett schema**.
 
    ![Skapa schema](./media/analysis-services-refresh-azure-automation/14.png)

2. Klicka på **Schemalägg**  >  **skapa ett nytt schema** och fyll sedan i informationen.

    ![Konfigurera schema](./media/analysis-services-refresh-azure-automation/15.png)

3. Klicka på **Skapa**.

4. Fyll i parametrarna för schemat. Dessa kommer att användas varje gången Runbook-flödet utlöses. Parametern **WEBHOOKDATA** måste vara tom när du kör via ett schema.

    ![Konfigurera parametrar](./media/analysis-services-refresh-azure-automation/16.png)

5. Klicka på **OK**.

## <a name="consume-with-data-factory"></a>Förbrukar med Data Factory

Om du vill använda runbooken genom att använda Azure Data Factory måste du först skapa en **webhook** för runbooken. **Webhooken** tillhandahåller en URL som kan anropas via en Azure Data Factory webb aktivitet.

> [!IMPORTANT]
> Om du vill skapa en **webhook** måste status för runbooken vara **publicerad**.

1. I din Automation-Runbook klickar du på **Webhooks** och sedan på **Lägg till webhook**.

   ![Lägg till webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Ge webhooken ett namn och ett förfallo datum.  Namnet identifierar bara webhooken i Automation-runbooken, den utgör inte en del av URL: en.

   >[!CAUTION]
   >Se till att du kopierar URL: en innan du stänger guiden eftersom du inte kan återställa den igen när den har stängts.
    
   ![Konfigurera webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Parametrarna för webhooken kan vara tomma.  När du konfigurerar Azure Data Factory webb aktivitet kan parametrarna skickas till webb anropets brödtext.

3. I Data Factory konfigurerar du en **webb aktivitet**

### <a name="example"></a>Exempel

   ![Exempel på webb aktivitet](./media/analysis-services-refresh-azure-automation/19.png)

**URL:** en är den URL som skapas från webhooken.

**Bröd texten** är ett JSON-dokument som innehåller följande egenskaper:


|Egenskap  |Värde  |
|---------|---------|
|**AnalysisServicesDatabase**     |Namnet på Azure Analysis Services databasen <br/> Exempel: AdventureWorksDB         |
|**AnalysisServicesServer**     |Namnet på Azure Analysis Services servern. <br/> Exempel: https: \/ /westus.asazure.Windows.net/servers/myserver/Models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ av uppdatering som ska utföras. <br/> Exempel: fullständig         |

Exempel på JSON-brödtext:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Dessa parametrar definieras i Runbook PowerShell-skriptet.  När webb aktiviteten körs är JSON-nyttolasten överförd WEBHOOKDATA.

Detta är deserialiserat och lagras som PowerShell-parametrar, som sedan används av Invoke-ProcesASDatabase PowerShell-kommandot.

![Avserialiserad webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Använda en Hybrid Worker med Azure Analysis Services

En virtuell Azure-dator med en statisk offentlig IP-adress kan användas som en Azure Automation Hybrid Worker.  Den här offentliga IP-adressen kan sedan läggas till i Azure Analysis Services brand väggen.

> [!IMPORTANT]
> Se till att den offentliga IP-adressen för den virtuella datorn är konfigurerad som statisk.
>
>Mer information om hur du konfigurerar Azure Automation hybrid Worker finns i [hybrid Runbook Worker installation](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation).

När en Hybrid Worker har kon figurer ATS skapar du en webhook enligt beskrivningen i avsnittet [förbruka med Data Factory](#consume-with-data-factory).  Den enda skillnaden är att välja alternativet **kör på**  >  **hybrid Worker** när du konfigurerar webhooken.

Exempel-webhook med Hybrid Worker:

![Exempel Hybrid Worker webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exempel på PowerShell-Runbook

Följande kodfragment är ett exempel på hur du utför uppdatering av Azure Analysis Servicess modellen med hjälp av en PowerShell-Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Nästa steg

[Exempel](analysis-services-samples.md)  
[REST-API](/rest/api/analysisservices/servers)