---
title: Skapa en grafisk Runbook i Azure Automation
description: I den här artikeln lär du dig att skapa, testa och publicera en enkel grafisk Runbook i Azure Automation.
keywords: runbook, runbook-mall, runbook-automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 1b6c02778b0ee790d81c713283e653058c29c153
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92899795"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Självstudie: skapa en grafisk Runbook

Den här självstudien beskriver steg för steg hur du skapar en [grafisk runbook](../automation-runbook-types.md#graphical-runbooks) i Azure Automation. Du kan skapa och redigera grafiska och grafiska PowerShell Workflow-Runbooks med hjälp av den grafiska redigeraren i Azure Portal. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en enkel grafisk Runbook
> * Testa och publicera runbooken
> * Kör och spåra statusen för Runbook-jobbet
> * Uppdatera runbooken för att starta en virtuell Azure-dator med Runbook-parametrar och villkorliga länkar

## <a name="prerequisites"></a>Förutsättningar

För att göra den här självstudien behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* Ett [Automation-konto för Azure](../index.yml) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör den inte vara en virtuell produktions dator.
* Om det behövs [importerar du Azure-moduler](../shared-resources/modules.md) eller [uppdaterar moduler](../automation-update-azure-modules.md) baserat på de cmdletar som du använder.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook

Börja med att skapa en enkel Runbook som matar ut texten `Hello World` .

1. Öppna ditt Automation-konto på Azure Portal.

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa till gångar är de moduler som automatiskt ingår i ett nytt Automation-konto. Du bör också ha den autentiseringsuppgifter som är kopplad till din prenumeration.

2. Öppna listan över runbooks genom att välja **Runbooks** under **process automatisering** .

3. Skapa en ny Runbook genom att välja **skapa en Runbook**.

4. Ge din runbook namnet **MyFirstRunbook-Graphical**.

5. I det här fallet ska du skapa en [grafisk Runbook](../automation-graphical-authoring-intro.md). Välj **grafisk** för **Runbook-typ**.

    ![Ny runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Klicka på **Skapa** för att skapa den nya runbooken och öppna den grafiska redigeraren.

## <a name="step-2---add-activities"></a>Steg 2 – lägga till aktiviteter

Du kan lägga till aktiviteter i din runbook med hjälp av bibliotekskontrollen till vänster i redigeraren. Du kommer att lägga till en `Write-Output` cmdlet för att mata ut text från runbooken.

1. I biblioteks kontrollen klickar du i Sök fältet och skriver `write-output` . Sök resultatet visas i följande bild.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Rulla längst ned i listan. Högerklicka på **Skriv ut** och välj **Lägg till på arbets ytan**. Alternativt kan du klicka på ellipsen (...) bredvid namnet på cmdleten och sedan välja **Lägg till på arbets ytan**.

3. Klicka på aktiviteten **Write-Output** på arbetsytan. Den här åtgärden öppnar sidan konfigurations kontroll där du kan konfigurera aktiviteten.

4. **Etikett** fältet är som standard namnet på cmdleten, men du kan ändra det till något mer användarvänligt. Ändra den till `Write Hello World to output`.

5. Klicka på **Parametrar** för att ange värden för cmdletens parametrar.

   En del cmdlets har flera parameter uppsättningar och du måste välja vilken som ska användas. I det här fallet `Write-Output` har bara en parameter uppsättning.

6. Välj `InputObject` parametern. Det här är den parameter som du använder för att ange den text som ska skickas till utdataströmmen.

7. List rutan **data källa** innehåller källor som du kan använda för att fylla i ett parameter värde. I den här menyn väljer du **PowerShell-uttryck**.

   Du kan använda utdata från sådana källor som en annan aktivitet, en Automation-till gång eller ett PowerShell-uttryck. I det här fallet är utdata bara `Hello World` . Du kan använda ett PowerShell-uttryck och ange en sträng.

8. I fältet **uttryck** skriver du `"Hello World"` och klickar sedan på **OK** två gånger för att återgå till arbets ytan.

9. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktion bör du testa den och kontrol lera att den fungerar som den ska. Om du testar en runbook körs dess utkast version och du kan visa dess utdata interaktivt.

1. Öppna test fönstret genom att välja **test fönster** .

2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.

3. Observera att ett [Runbook-jobb](../automation-runbook-execution.md) skapas och att dess status visas i fönstret.

   Jobbets status börjar som `Queued` , vilket anger att jobbet väntar på att en Runbook Worker i molnet ska bli tillgänglig. Statusen ändras till `Starting` när en arbets uppgift anspråkerar jobbet. Slutligen blir statusen `Running` när runbooken faktiskt börjar köras.

4. När Runbook-jobbet har slutförts visas utdata i test fönstret. I det här fallet visas `Hello World` .

    ![Hello World Runbook-utdata](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Den Runbook som du har skapat är fortfarande i utkast läge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Välj **publicera** för att publicera runbooken och sedan **Ja** när du uppmanas till det.

2. Rulla åt vänster för att Visa runbooken på sidan Runbooks och Observera att status värdet för **redigering** är inställt på **publicerat**.

3. Rulla tillbaka till höger för att visa sidan för **MyFirstRunbook-Graphic**.

   Med alternativen över överst kan du starta runbooken nu, schemalägga en framtida start tid eller skapa en [webhook](../automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

4. Välj **Start** och sedan **Ja** när du uppmanas att starta runbooken.

5. Ett jobb fönster öppnas för det Runbook-jobb som har skapats. Kontrol lera att fältet **jobb status** visar **slutfört**.

6. Klicka på **utdata** för att öppna sidan utdata där du kan se `Hello World` visning.

7. Stäng sidan utdata.

8. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se `Hello World` i utdataströmmen.

    Observera att fönstret strömmar kan visa andra strömmar för ett Runbook-jobb, till exempel utförliga data strömmar och fel strömmar, om Runbook skriver till dem.

9. Stäng fönstret strömmar och fönstret jobb för att återgå till MyFirstRunbook-Graphical sidan.

10. Om du vill visa alla jobb för runbooken väljer du **jobb** under **resurser**. På sidan jobb visas alla jobb som skapats av din Runbook. Du bör bara se ett jobb i listan eftersom du bara kör jobbet en gång.

11. Klicka på jobb namnet för att öppna samma jobb fönster som du visade när du startade runbooken. Använd det här fönstret om du vill visa information om alla jobb som har skapats för runbooken.

## <a name="step-5---create-variable-assets"></a>Steg 5 – Skapa variabler för tillgångar

Du har testat och publicerat din Runbook, men hittills gör den inte något användbart för att hantera Azure-resurser. Innan du konfigurerar runbooken att autentisera måste du skapa en variabel som ska innehålla prenumerations-ID, konfigurera en aktivitet för autentisering och sedan referera till variabeln. Genom att inkludera en referens till prenumerations kontexten kan du enkelt arbeta med flera prenumerationer.

1. Kopiera ditt prenumerations-ID från alternativet **prenumerationer** i navigerings fönstret.

2. På sidan Automation-konton väljer du **variabler** under **delade resurser**.

3. Välj **Lägg till en variabel**.

4. På sidan ny variabel gör du följande inställningar i de angivna fälten.

    * **Namn** --ange `AzureSubscriptionId` .
    * **Värde** – ange ditt PRENUMERATIONS-ID.
    * **Skriv** --Behåll sträng vald.
    * **Kryptering** – Använd standardvärdet.

5. Skapa variabeln genom att klicka på **Skapa**.

## <a name="step-6---add-authentication"></a>Steg 6 – Lägg till autentisering

Nu när du har en variabel som ska innehålla prenumerations-ID kan du konfigurera runbooken att autentisera med kör som-autentiseringsuppgifterna för din prenumeration. Gör detta genom att lägga till Azure kör som-anslutningen som en till gång. Du måste också lägga till cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) och cmdleten [set-AzContext](/powershell/module/az.accounts/Set-AzContext) på arbets ytan.

>[!NOTE]
>För PowerShell-Runbooks `Add-AzAccount` och `Add-AzureRMAccount` är alias för `Connect-AzAccount` . Observera att dessa alias inte är tillgängliga för dina grafiska runbooks. En grafisk Runbook kan bara använda `Connect-AzAccount` sig själv.

1. Navigera till din Runbook och välj **Redigera** på sidan MyFirstRunbook-Graphical.

2. Du behöver inte `Write Hello World to output` längre posten. Klicka bara på ellipsen och välj **ta bort**.

3. I biblioteks kontrollen expanderar du **till gångar** och sedan **anslutningar**. Lägg till på arbets `AzureRunAsConnection` ytan genom att välja **Lägg till på arbets ytan**.

4. Byt namn `AzureRunAsConnection` till `Get Run As Connection` .

5. I biblioteks kontrollen skriver `Connect-AzAccount` du i Sök fältet.

6. Lägg till `Connect-AzAccount` på arbets ytan.

7. Hovra över `Get Run As Connection` tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till den `Connect-AzAccount` för att skapa en länk. Runbooken börjar med `Get Run As Connection` och körs sedan `Connect-AzAccount` .

    ![Skapa länk mellan aktiviteter](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. På arbets ytan väljer du `Connect-AzAccount` . I rutan konfigurations kontroll skriver **du logga in på Azure** i fältet **etikett** .

9. Klicka på **parametrar** så visas sidan konfiguration av aktivitets parameter.

10. `Connect-AzAccount`Cmdleten har flera parameter uppsättningar och du måste välja en innan du tillhandahåller parameter värden. Klicka på **parameter uppsättning** och välj sedan **ServicePrincipalCertificateWithSubscriptionId**.

11. Parametrarna för den här parameter uppsättningen visas på sidan konfiguration av aktivitets parameter. Klicka på **APPLICATIONID**.

    ![Lägg till parametrar för Azure-konto](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. På sidan parameter värde gör du följande inställningar och klickar sedan på **OK**.

   * **Data källa** – Välj **aktivitets utdata**.
   * Lista över data källor – Välj **Hämta Automation-anslutning**.
   * **Fält Sök väg** --typ `ApplicationId` . Du anger namnet på egenskapen för fält Sök vägen eftersom aktiviteten matar ut ett objekt med flera egenskaper.

13. Klicka på **CERTIFICATETHUMBPRINT** och gör följande inställningar på sidan parameter värde och klicka sedan på **OK**.

    * **Data källa** – Välj **aktivitets utdata**.
    * Lista över data källor – Välj **Hämta Automation-anslutning**.
    * **Fält Sök väg** --typ `CertificateThumbprint` .

14. Klicka på **SERVICEPRINCIPAL** och på sidan parameter värde väljer du **ConstantValue** för fältet **data källa** . Klicka på alternativet **Sant** och klicka sedan på **OK**.

15. Klicka på **TENANTID** och gör följande inställningar på sidan parameter värde. När du är färdig klickar du på **OK** två gånger.

    * **Data källa** – Välj **aktivitets utdata**.
    * Lista över data källor – Välj **Hämta Automation-anslutning**.
    * **Fält Sök väg** --typ `TenantId` .

16. I biblioteks kontrollen skriver `Set-AzContext` du i Sök fältet.

17. Lägg till `Set-AzContext` på arbets ytan.

18. Välj `Set-AzContext` på arbets ytan. I rutan konfigurations kontroll anger `Specify Subscription Id` du i fältet **etikett** .

19. Klicka på **parametrar** och sidan konfiguration av aktivitets parameter visas.

20. `Set-AzContext`Cmdleten har flera parameter uppsättningar och du måste välja en innan du tillhandahåller parameter värden. Klicka på **parameter uppsättning** och välj sedan **SubscriptionId**.

21. Parametrarna för den här parameter uppsättningen visas på sidan konfiguration av aktivitets parameter. Klicka på **SubscriptionID**.

22. På sidan parameter värde väljer du **variabel till gång** för fältet **data källa** och väljer **AzureSubscriptionId** i listan källa. När du är färdig klickar du på **OK** två gånger.

23. Hovra över `Login to Azure` tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till `Specify Subscription Id` . Din Runbook bör se ut så här nu.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Skärm bild av runbooken när du har dragit pilen till &quot;ange prenumerations-ID&quot;.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Steg 7 – Lägga till aktivitet för att starta en virtuell dator

Nu måste du lägga till en `Start-AzVM` aktivitet för att starta en virtuell dator. Du kan välja vilken virtuell dator som helst i din Azure-prenumeration och för tillfället hårdkoda du dess namn i cmdleten [Start-AzVM](/powershell/module/az.compute/start-azvm) .

1. I biblioteks kontrollen skriver `Start-Az` du i Sök fältet.

2. Lägg till på `Start-AzVM` arbets ytan och klicka på och dra den under `Specify Subscription Id` .

3. Hovra över `Specify Subscription Id` tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till `Start-AzVM` .

4. Välj `Start-AzVM`. Klicka på **parametrar** och sedan på **parameter uppsättning** för att Visa uppsättningarna för aktiviteten.

5. Välj **ResourceGroupNameParameterSetName** för parameter uppsättningen. Fälten **ResourceGroupName** och **Name** har utrops tecken bredvid sig för att ange att de är obligatoriska parametrar. Observera att båda fälten förväntar sig sträng värden.

6. Välj **Name**. Välj **PowerShell-uttryck** för **data källans** fält. För den virtuella dator som du använder för att starta denna Runbook skriver du in dator namnet omgiven av dubbla citat tecken. Klicka på **OK**.

7. Välj **ResourceGroupName**. Använd värdet **PowerShell-uttrycket** för fältet **data källa** och skriv namnet på resurs gruppen omgiven av dubbla citat tecken. Klicka på **OK**.

8. Klicka på **test fönster** så att du kan testa runbooken.

9. Starta testet genom att klicka på **Starta** . När den är klar kontrollerar du att den virtuella datorn har startats. Din Runbook bör se ut så här nu.

    ![Runbook Start-AzVM utdata](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Steg 8 – lägga till ytterligare indataparametrar

Din Runbook startar för närvarande den virtuella datorn i den resurs grupp som du har angett för `Start-AzVM` cmdleten. Runbooken blir mer användbar om du anger både namn och resurs grupp när runbooken startas. Nu ska vi lägga till indataparametrar till Runbook för att tillhandahålla den funktionen.

1. Öppna den grafiska redigeraren genom att klicka på **Redigera** på sidan MyFirstRunbook-Graphical.

2. Välj **indata och utdata** och **Lägg sedan till indata** för att öppna fönstret inmatnings parameter för Runbook.

3. Gör följande inställningar i de angivna fälten och klicka sedan på **OK**.
   * **Namn** --ange `VMName` .
   * **Skriv** --Behåll sträng inställningen.
   * **Obligatoriskt** – ändra värdet till **Ja**.

4. Skapa en andra obligatorisk indataparameter med namnet `ResourceGroupName` och klicka sedan på **OK** för att stänga fönstret indata och utdata.

    ![Indataparametrar för runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Välj `Start-AzVM` aktiviteten och klicka sedan på **parametrar**.

6. Ändra fältet **data källa** för **namn** till **indata för Runbook**. Välj sedan **VMName**.

7. Ändra fältet **data källa** för **ResourceGroupName** till **indata för Runbook** och välj sedan **ResourceGroupName**.

    ![Start-AzVM parametrar](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som du använder i testet.

9. Stäng Testfönster.

10. Klicka på **Publicera** för att publicera den nya versionen av runbooken.

11. Stoppa den virtuella datorn som du startade tidigare.

12. Starta runbooken genom att klicka på **Starta**. Ange värdena för `VMName` och `ResourceGroupName` för den virtuella dator som du ska starta.

13. När Runbook-flödet har slutförts kontrollerar du att den virtuella datorn har startats.

## <a name="step-9---create-a-conditional-link"></a>Steg 9 – Skapa en villkorlig länk

Nu kan du ändra runbooken så att den bara försöker starta den virtuella datorn om den inte redan har startats. Det gör du genom att lägga till en [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) -cmdlet som hämtar den virtuella datorns status på instans nivå. Sedan kan du lägga till en PowerShell-modul för arbets flödes kod som anropas `Get Status` med ett fragment av PowerShell-kod för att avgöra om VM-statusen körs eller är stoppad. En villkorlig länk från `Get Status` modulen körs bara `Start-AzVM` om det aktuella körnings läget har stoppats. I slutet av den här proceduren använder din Runbook `Write-Output` cmdleten för att skicka ett meddelande för att meddela dig om den virtuella datorn har startats.

1. Öppna **MyFirstRunbook – grafiskt** i den grafiska redigeraren.

2. Ta bort länken mellan `Specify Subscription Id` och `Start-AzVM` genom att klicka på den och sedan trycka på **ta bort**.

3. I biblioteks kontrollen skriver `Get-Az` du i Sök fältet.

4. Lägg till `Get-AzVM` på arbets ytan.

5. Välj `Get-AzVM` och klicka sedan på **parameter uppsättning** för att Visa uppsättningarna för cmdleten.

6. Välj parameteruppsättningen **GetVirtualMachineInResourceGroupNameParamSet**. Fälten **ResourceGroupName** och **namn** innehåller utrops tecken bredvid dem, vilket indikerar att de anger obligatoriska parametrar. Observera att båda fälten förväntar sig sträng värden.

7. Under **data källa** för **namn** väljer du **indata för Runbook** och sedan **VMName**. Klicka på **OK**.

8. Under **data källa** för **ResourceGroupName**, väljer du **indata för Runbook** och sedan **ResourceGroupName**. Klicka på **OK**.

9. Under **data källa** för **status** väljer du **konstant värde** och sedan **Sant**. Klicka på **OK**.

10. Skapa en länk från `Specify Subscription Id` till `Get-AzVM` .

11. I biblioteks kontrollen expanderar du **Runbook-kontroll** och lägger till **kod** på arbets ytan.  

12. Skapa en länk från `Get-AzVM` till `Code` .  

13. Klicka på `Code` och ändra etiketten för att **Hämta status** i rutan konfiguration.

14. Välj `Code` och sidan kod redigerare visas.  

15. Klistra in följande kodfragment på sidan redigeraren.

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

17. Välj länken och ändra **tillämpa villkor** till **Ja** i rutan konfiguration. Observera att länken blir en streckad linje som anger att mål aktiviteten endast körs om villkoret matchar sant.  

18. För **villkors uttryck** skriver du `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` körs nu bara om den virtuella datorn har stoppats.

19. Expandera **Cmdlets** och sedan **Microsoft.PowerShell.Utility** i bibliotekskontrollen.

20. Lägg till `Write-Output` på arbets ytan två gånger.

21. För den första `Write-Output` kontrollen klickar du på **parametrar** och ändrar värdet för **etikett** för att **meddela att den virtuella datorn har startats**.

22. För **InputObject** ändrar du **data källa** till **PowerShell-uttryck** och skriver in uttrycket `$VMName successfully started.` .

23. På den andra `Write-Output` kontrollen klickar du på **parametrar** och ändrar **etikett** svärdet för att **meddela att den virtuella datorn startades**.

24. För **InputObject** ändrar du **data källa** till **PowerShell-uttryck** och skriver in uttrycket `$VMName could not start` .

25. Skapa länkar från `Start-AzVM` till `Notify VM Started` och `Notify VM Start Failed` .

26. Välj alternativet länka till `Notify VM Started` och ändra **tillämpa villkor** till sant.

27. Skriv i **villkors uttrycket** `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Den här `Write-Output` kontrollen körs nu bara om den virtuella datorn har startats.

28. Välj alternativet länka till `Notify VM Start Failed` och ändra **tillämpa villkor** till sant.

29. I fältet **villkors uttryck** skriver du `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Den här `Write-Output` kontrollen körs nu bara om den virtuella datorn inte har startats. Din Runbook bör se ut som på följande bild.

    ![Runbook med Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Spara runbooken och öppna Testfönster.

31. Starta runbooken med den virtuella datorn stoppad och starta sedan datorn.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering finns [i redigera en grafisk Runbook i Azure Automation](../automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-Runbooks finns i [skapa en PowerShell-Runbook](automation-tutorial-runbook-textual-powershell.md).
* Information om hur du kommer igång med PowerShell Workflow-Runbooks finns i [skapa en PowerShell Workflow-Runbook](automation-tutorial-runbook-textual.md).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
