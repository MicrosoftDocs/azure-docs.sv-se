---
title: Skapa en grafisk runbook i Azure Automation
description: I den här artikeln får du lära dig att skapa, testa och publicera en enkel grafisk runbook i Azure Automation.
keywords: runbook, runbook-mall, runbook-automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 668f661d2c8f349f4dac3f850800d83890d2f4d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831446"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Självstudie: Skapa en grafisk runbook

Den här självstudien beskriver steg för steg hur du skapar en [grafisk runbook](../automation-runbook-types.md#graphical-runbooks) i Azure Automation. Du kan skapa och redigera grafiska PowerShell Workflow-runbooks med hjälp av den grafiska redigeraren i Azure Portal. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en enkel grafisk runbook
> * Testa och publicera runbooken
> * Köra och spåra status för runbook-jobbet
> * Uppdatera runbooken för att starta en virtuell Azure-dator med runbook-parametrar och villkorsstyrda länkar

## <a name="prerequisites"></a>Förutsättningar

För att göra den här självstudien behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* Ett [Automation-konto för Azure](../index.yml) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör det inte vara en virtuell produktionsdator.
* Om det behövs [importerar du Azure-moduler](../shared-resources/modules.md) [eller uppdateringsmoduler](../automation-update-azure-modules.md) baserat på de cmdlets som du använder.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook

Börja med att skapa en enkel runbook som matar ut texten `Hello World` .

1. Öppna ditt Automation-konto på Azure Portal.

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du bör också ha den autentiseringstillgång som är associerad med din prenumeration.

2. Välj **Runbooks** under **Processautomatisering** för att öppna listan över runbooks.

3. Skapa en ny runbook genom att välja **Skapa en runbook**.

4. Ge din runbook namnet **MyFirstRunbook-Graphical**.

5. I det här fallet ska du skapa en grafisk [runbook](../automation-graphical-authoring-intro.md). Välj **Grafisk** för **Runbook-typ**.

    ![Ny runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Klicka på **Skapa** för att skapa den nya runbooken och öppna den grafiska redigeraren.

## <a name="step-2---add-activities"></a>Steg 2 – Lägg till aktiviteter

Du kan lägga till aktiviteter i din runbook med hjälp av bibliotekskontrollen till vänster i redigeraren. Du ska lägga till en `Write-Output` cmdlet för att mata ut text från runbooken.

1. I bibliotekskontrollen klickar du i sökfältet och skriver `write-output` . Sökresultaten visas i följande bild.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Rulla längst ned i listan. Högerklicka på **Write-Output och välj** Lägg till på **arbetsytan.** Du kan också klicka på ellipsen (...) bredvid cmdlet-namnet och sedan välja Lägg **till på arbetsytan.**

3. Klicka på aktiviteten **Write-Output** på arbetsytan. Den här åtgärden öppnar sidan Konfigurationskontroll där du kan konfigurera aktiviteten.

4. Fältet **Etikett** får som standard namnet på cmdleten, men du kan ändra det till något mer användarvänligt. Ändra den till `Write Hello World to output`.

5. Klicka på **Parametrar** för att ange värden för cmdletens parametrar.

   Vissa cmdlets har flera parameteruppsättningar och du måste välja vilken som ska användas. I det här fallet `Write-Output` har bara en parameter angetts.

6. Välj `InputObject` parametern . Det här är den parameter som du använder för att ange vilken text som ska skickas till utdataströmmen.

7. Listrutan **Datakälla** innehåller källor som du kan använda för att fylla i ett parametervärde. I den här menyn väljer du **PowerShell-uttrycket**.

   Du kan använda utdata från källor som en annan aktivitet, en Automation-tillgång eller ett PowerShell-uttryck. I det här fallet är utdata bara `Hello World` . Du kan använda ett PowerShell-uttryck och ange en sträng.

8. I fältet **Uttryck** skriver du och `"Hello World"` klickar sedan på OK två **gånger** för att återgå till arbetsytan.

9. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktion bör du testa den för att se till att den fungerar korrekt. När du testar en runbook körs utkastversionen och du kan visa dess utdata interaktivt.

1. Välj **Testfönster** för att öppna testfönstret.

2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.

3. Observera att ett [runbook-jobb](../automation-runbook-execution.md) skapas och dess status visas i fönstret.

   Jobbets status startar som `Queued` , vilket anger att jobbet väntar på att en Runbook Worker i molnet ska bli tillgänglig. Statusen ändras till `Starting` när en arbetsroll gör anspråk på jobbet. Slutligen blir statusen `Running` när runbooken faktiskt börjar köras.

4. När runbook-jobbet har slutförts visar fönstret Test dess utdata. I det här fallet visas `Hello World` .

    ![Hello World runbook-utdata](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Runbooken som du har skapat är fortfarande i utkastläge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Välj **Publicera** för att publicera runbooken och sedan **Ja när** du uppmanas att göra det.

2. Rulla åt vänster för att visa runbooken på sidan Runbooks och observera att **värdet Redigeringsstatus** är inställt på **Publicerad.**

3. Rulla tillbaka till höger för att visa sidan för **MyFirstRunbook-Graphical**.

   Med alternativen längst upp kan du starta runbooken nu, schemalägga en framtida starttid eller skapa en [webhook](../automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

4. Välj **Start** och sedan **Ja när** du uppmanas att starta runbooken.

5. Ett jobbfönster öppnas för runbook-jobbet som har skapats. Kontrollera att fältet **Jobbstatus** visar **Slutfört.**

6. Klicka **på Utdata** för att öppna sidan Utdata, där du kan `Hello World` se den.

7. Stäng sidan Utdata.

8. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se `Hello World` i utdataströmmen.

    Observera att fönstret Strömmar kan visa andra strömmar för ett runbook-jobb, till exempel utförliga dataströmmar och felströmmar, om runbooken skriver till dem.

9. Stäng fönstret Strömmar och fönstret Jobb för att återgå till MyFirstRunbook-Graphical sidan.

10. Om du vill visa alla jobb för runbooken väljer du **Jobb** under **Resurser**. Sidan Jobb visar en lista över alla jobb som skapats av din runbook. Du bör bara se ett jobb i listan eftersom du bara har kört jobbet en gång.

11. Klicka på jobbnamnet för att öppna samma jobbfönster som du visade när du startade runbooken. Använd det här fönstret för att visa information om alla jobb som skapats för runbooken.

## <a name="step-5---create-variable-assets"></a>Steg 5 – Skapa variabler för tillgångar

Du har testat och publicerat din runbook, men hittills gör den inte något användbart för att hantera Azure-resurser. Innan du konfigurerar runbooken för autentisering måste du skapa en variabel som ska innehålla prenumerations-ID:t, konfigurera en aktivitet för autentisering och sedan referera till variabeln. Genom att inkludera en referens till prenumerationskontexten kan du enkelt arbeta med flera prenumerationer.

1. Kopiera ditt prenumerations-ID **från alternativet** Prenumerationer i navigeringsfönstret.

2. På sidan Automation-konton väljer du **Variabler** under **Delade resurser.**

3. Välj **Lägg till en variabel**.

4. På sidan Ny variabel gör du följande inställningar i de angivna fälten.

    * **Namn** – ange `AzureSubscriptionId` .
    * **Värde** – ange ditt prenumerations-ID.
    * **Typ** – behåll strängen markerad.
    * **Kryptering** – använd standardvärdet.

5. Skapa variabeln genom att klicka på **Skapa**.

## <a name="step-6---add-authentication"></a>Steg 6 – Lägg till autentisering

Nu när du har en variabel som ska innehålla prenumerations-ID:t kan du konfigurera runbooken så att den autentiserar med Kör som-autentiseringsuppgifterna för din prenumeration. Gör detta genom att lägga till Azure Kör som-anslutningen som en tillgång. Du måste också lägga till [cmdleten Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) och cmdleten [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) på arbetsytan.

>[!NOTE]
>För PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` `Connect-AzAccount` . Observera att dessa alias inte är tillgängliga för dina grafiska runbooks. En grafisk runbook kan bara använda sig `Connect-AzAccount` själv.

1. Gå till din runbook och **välj Redigera** på MyFirstRunbook-Graphical sidan.

2. Du behöver inte `Write Hello World to output` posten längre. Klicka bara på ellipsen och välj Ta **bort.**

3. I bibliotekskontrollen expanderar **du TILLGÅNGAR** och sedan **Anslutningar**. Lägg `AzureRunAsConnection` till på arbetsytan genom att välja Lägg till på **arbetsytan**.

4. Byt `AzureRunAsConnection` namn till `Get Run As Connection` .

5. I bibliotekskontrollen skriver `Connect-AzAccount` du i sökfältet.

6. Lägg `Connect-AzAccount` till på arbetsytan.

7. Hovra `Get Run As Connection` över tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till för `Connect-AzAccount` att skapa en länk. Runbooken börjar med `Get Run As Connection` och kör sedan `Connect-AzAccount` .

    ![Skapa länk mellan aktiviteter](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. På arbetsytan väljer du `Connect-AzAccount` . I fönstret Konfigurationskontroll skriver du **Logga in på Azure** i **fältet** Etikett.

9. Klicka **på** Parametrar så visas sidan Konfiguration av aktivitetsparameter.

10. `Connect-AzAccount`Cmdleten har flera parameteruppsättningar och du måste välja en innan du anger parametervärden. Klicka **på Parameteruppsättning** och välj **sedan ServicePrincipalCertificateWithSubscriptionId**.

11. Parametrarna för den här parameteruppsättningen visas på sidan Konfiguration av aktivitetsparameter. Klicka på **APPLICATIONID**.

    ![Lägga till Azure-kontoparametrar](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. På sidan Parametervärde gör du följande inställningar och klickar sedan på **OK.**

   * **Datakälla** – välj **Aktivitetsutdata**.
   * Datakällslista – välj **Hämta Automation-anslutning.**
   * **Fältsökväg** – skriv `ApplicationId` . Du anger namnet på egenskapen för fältsökvägen eftersom aktiviteten matar ut ett objekt med flera egenskaper.

13. Klicka **på CERTIFICATETHUMBPRINT.** På sidan Parametervärde gör du följande inställningar och klickar sedan på **OK.**

    * **Datakälla** – välj **Aktivitetsutdata**.
    * Datakällslista – välj **Hämta Automation-anslutning.**
    * **Fältsökväg** – skriv `CertificateThumbprint` .

14. Klicka **på SERVICEPRINCIPAL** och välj **ConstantValue** för fältet Datakälla på **sidan Parametervärde.** Klicka på alternativet **Sant** och klicka sedan på **OK.**

15. Klicka **på TENANTID** och gör följande inställningar på sidan Parametervärde. När du är klar klickar du **på OK två** gånger.

    * **Datakälla** – välj **Aktivitetsutdata**.
    * Datakällslista – välj **Hämta Automation-anslutning.**
    * **Fältsökväg** – skriv `TenantId` .

16. Skriv i sökfältet `Set-AzContext` i bibliotekskontrollen.

17. Lägg `Set-AzContext` till på arbetsytan.

18. Välj `Set-AzContext` på arbetsytan. I fönstret Konfigurationskontroll anger du `Specify Subscription Id` i **fältet** Etikett.

19. Klicka **på Parametrar** så visas sidan Konfiguration av aktivitetsparameter.

20. `Set-AzContext`Cmdleten har flera parameteruppsättningar och du måste välja en innan du anger parametervärden. Klicka **på Parameteruppsättning** och välj sedan **SubscriptionId**.

21. Parametrarna för den här parameteruppsättningen visas på sidan Konfiguration av aktivitetsparameter. Klicka **på SubscriptionID**.

22. På sidan Parametervärde väljer du **Variabeltillgång** för **fältet Datakälla** och **väljer AzureSubscriptionId** i källlistan. När du är klar klickar du **på OK två** gånger.

23. Hovra `Login to Azure` över tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till `Specify Subscription Id` . Din runbook bör se ut så här i det här läget.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Skärmbild av runbooken när du har dragit pilen till Ange prenumerations-ID.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Steg 7 – Lägga till aktivitet för att starta en virtuell dator

Nu måste du lägga till en `Start-AzVM` aktivitet för att starta en virtuell dator. Du kan välja valfri virtuell dator i din Azure-prenumeration och för tillfället hårdkodar du dess namn i cmdleten [Start-AzVM.](/powershell/module/az.compute/start-azvm)

1. I bibliotekskontrollen skriver `Start-Az` du i sökfältet.

2. Lägg `Start-AzVM` till på arbetsytan och klicka och dra det under `Specify Subscription Id` .

3. Hovra `Specify Subscription Id` över tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till `Start-AzVM` .

4. Välj `Start-AzVM`. Klicka **på** Parametrar och **sedan på Parameteruppsättning** för att visa uppsättningar för aktiviteten.

5. Välj **ResourceGroupNameParameterSetName** som parameteruppsättning. Fälten **ResourceGroupName och** **Name har** utropstecken bredvid sig för att ange att de är obligatoriska parametrar. Observera att båda fälten förväntar sig strängvärden.

6. Välj **Name**. Välj **PowerShell-uttryck** för **fältet Datakälla.** För den virtuella dator som du använder för att starta den här runbooken anger du datornamnet omgivet av dubbla citattecken. Klicka på **OK**.

7. Välj **ResourceGroupName**. Använd värdet **PowerShell-uttryck** för **fältet Datakälla** och skriv namnet på resursgruppen omgivet av dubbla citattecken. Klicka på **OK**.

8. Klicka **på Testfönster** så att du kan testa runbooken.

9. Klicka **på Starta** för att starta testet. När den är klar kontrollerar du att den virtuella datorn har startats. Din runbook bör se ut så här i det här läget.

    ![Runbook-Start-AzVM utdata](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Steg 8 – Lägg till ytterligare indataparametrar

Din runbook startar för närvarande den virtuella datorn i resursgruppen som du angav för `Start-AzVM` cmdleten . Runbooken är mer användbar om du anger både namn och resursgrupp när runbooken startas. Nu ska vi lägga till indataparametrar i runbooken för att tillhandahålla den funktionen.

1. Öppna den grafiska redigeraren genom **att** klicka på Redigera MyFirstRunbook-Graphical sidan.

2. Välj **Indata och utdata** och sedan **Lägg till indata** för att öppna fönstret Indataparameter för Runbook.

3. Gör följande inställningar i de angivna fälten och klicka sedan på **OK.**
   * **Namn** – ange `VMName` .
   * **Typ** – behåll stränginställningen.
   * **Obligatoriskt** – ändra värdet till **Ja.**

4. Skapa en andra obligatorisk indataparameter med `ResourceGroupName` namnet och klicka sedan på **OK** för att stänga fönstret Indata och Utdata.

    ![Indataparametrar för runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Välj aktiviteten `Start-AzVM` och klicka sedan på **Parametrar.**

6. Ändra fältet **Datakälla för** Namn **till** **Runbook-indata**. Välj sedan **VMName**.

7. Ändra fältet **Datakälla** för **ResourceGroupName till** **Runbook-indata** och välj **sedan ResourceGroupName.**

    ![Start-AzVM parametrar](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som du använder i testet.

9. Stäng Testfönster.

10. Klicka på **Publicera** för att publicera den nya versionen av runbooken.

11. Stoppa den virtuella dator som du startade tidigare.

12. Starta runbooken genom att klicka på **Starta**. Ange värdena för `VMName` och för den virtuella dator som du ska `ResourceGroupName` starta.

13. När runbooken är klar ser du till att den virtuella datorn har startats.

## <a name="step-9---create-a-conditional-link"></a>Steg 9 – Skapa en villkorlig länk

Nu kan du ändra runbooken så att den bara försöker starta den virtuella datorn om den inte redan har startats. Gör detta genom att lägga [till en Get-AzVM-cmdlet](/powershell/module/Az.Compute/Get-AzVM) som hämtar den virtuella datorns status på instansnivå. Sedan kan du lägga till en PowerShell Workflow-kodmodul med namnet med ett kodfragment med PowerShell-kod för att avgöra om `Get Status` VM-tillståndet körs eller stoppas. En villkorlig länk från modulen `Get Status` körs bara om det aktuella `Start-AzVM` körningstillståndet har stoppats. I slutet av den här proceduren använder runbooken cmdleten för att mata ut ett meddelande för att informera dig om att den `Write-Output` virtuella datorn har startats.

1. Öppna **MyFirstRunbook-Graphical i** den grafiska redigeraren.

2. Ta bort länken mellan och `Specify Subscription Id` genom att klicka på den och sedan trycka på Ta `Start-AzVM` **bort.**

3. I bibliotekskontrollen skriver `Get-Az` du i sökfältet.

4. Lägg `Get-AzVM` till på arbetsytan.

5. Välj `Get-AzVM` och klicka sedan på **Parameteruppsättning** för att visa uppsättningarna för cmdleten.

6. Välj parameteruppsättningen **GetVirtualMachineInResourceGroupNameParamSet**. Fälten **ResourceGroupName** **och Name** har utropstecken bredvid sig, vilket anger att de anger obligatoriska parametrar. Observera att båda fälten förväntar sig strängvärden.

7. Under **Datakälla** för **Namn** väljer du **Runbook-indata** och sedan **VMName**. Klicka på **OK**.

8. Under **Datakälla** för **ResourceGroupName** väljer du **Runbook input** och sedan **ResourceGroupName.** Klicka på **OK**.

9. Under **Datakälla** för **Status** väljer du **Konstant värde** och sedan **True**. Klicka på **OK**.

10. Skapa en länk från `Specify Subscription Id` till `Get-AzVM` .

11. I bibliotekskontrollen expanderar du **Runbook-kontroll** och lägger **till Kod** på arbetsytan.  

12. Skapa en länk från `Get-AzVM` till `Code` .  

13. Klicka `Code` på och ändra etiketten till Hämta status i fönstret **Konfiguration.**

14. Välj `Code` så visas sidan Kodredigerare.  

15. Klistra in följande kodfragment på redigeringssidan.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Skapa en länk från `Get Status` till `Start-AzVM` .

    ![Runbook med kodmodul](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Välj länken och ändra Tillämpa villkor till Ja **i** fönstret **Konfiguration.** Observera att länken blir en streckad linje som anger att målaktiviteten bara körs om villkoret matchas till sant.  

18. I **Villkorsuttryck** skriver du `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` körs nu bara om den virtuella datorn har stoppats.

19. Expandera **Cmdlets** och sedan **Microsoft.PowerShell.Utility** i bibliotekskontrollen.

20. Lägg `Write-Output` till på arbetsytan två gånger.

21. För den första `Write-Output` kontrollen klickar du på Parametrar **och** ändrar värdet **etikett till** Meddela att den virtuella datorn **har startat.**

22. För **InputObject** ändrar du **Datakälla** till **PowerShell-uttryck** och skriver i uttrycket `$VMName successfully started.` .

23. I den andra `Write-Output` kontrollen klickar du på Parametrar **och** ändrar värdet etikett **till Meddela** att det inte gick att starta den **virtuella datorn.**

24. För **InputObject** ändrar du **Datakälla** till **PowerShell-uttryck** och skriver i uttrycket `$VMName could not start` .

25. Skapa länkar från `Start-AzVM` till `Notify VM Started` och `Notify VM Start Failed` .

26. Välj länken till `Notify VM Started` och ändra Tillämpa villkor **till** sant.

27. Som **Villkorsuttryck** skriver du `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Den `Write-Output` här kontrollen körs nu bara om den virtuella datorn startar korrekt.

28. Välj länken till `Notify VM Start Failed` och ändra Tillämpa **villkor** till sant.

29. I fältet **Villkorsuttryck** skriver du `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Den `Write-Output` här kontrollen körs nu bara om den virtuella datorn inte har startats. Din runbook bör se ut som på följande bild.

    ![Runbook med Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Spara runbooken och öppna Testfönster.

31. Starta runbooken med den virtuella datorn stoppad, så bör datorn starta.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering finns i Author [a graphical runbook in Azure Automation](../automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-runbooks finns [i Skapa en PowerShell-runbook.](automation-tutorial-runbook-textual-powershell.md)
* Information om hur du kommer igång med PowerShell Workflow-runbooks finns i [Skapa en PowerShell-arbetsflödes-runbook.](automation-tutorial-runbook-textual.md)
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
