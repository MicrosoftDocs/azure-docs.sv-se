---
title: 'ExpressRoute: Konfigurera anpassade aviseringar för annonserade vägar'
description: Den här artikeln visar hur du använder Azure Automation och Logic Apps för att övervaka antalet vägar som annonseras från ExpressRoute-gatewayen till lokala nätverk för att förhindra att gränsen på 200-vägar påträffas.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: fed7663e2342a708aee70b9a54e6e0a6b6f97e8c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504409"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Konfigurera anpassade aviseringar för att övervaka annonserade vägar

Den här artikeln hjälper dig att använda Azure Automation och Logic Apps för att ständigt övervaka antalet vägar som annonseras från ExpressRoute-gatewayen till lokala nätverk. Övervakning kan hjälpa till att förhindra att [gränsen på 200-vägar](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering)påträffas.

Med **Azure Automation** kan du automatisera körningen av anpassat PowerShell-skript som lagras i en *Runbook*. När du använder konfigurationen i den här artikeln innehåller runbooken ett PowerShell-skript som frågar en eller flera ExpressRoute-gatewayer. Den samlar in en data uppsättning som innehåller resurs gruppen, ExpressRoute Gateway-namn och antalet nätverks prefix som annonseras lokalt.

**Azure Logic Apps** schemalägger ett anpassat arbets flöde som anropar Azure Automation Runbook. Körningen av runbooken görs med ett jobb. När data insamlingen körs klassificerar Azure Logic Apps-arbets flödet data och, baserat på matchnings villkor för antalet nätverks prefix ovan eller under ett fördefinierat tröskelvärde, skickar information till en mål-e-postadress.

### <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Att konfigurera en anpassad avisering baseras på tre huvud steg:

1. Skapa ett Automation-konto med ett "kör som"-konto och-behörigheter.

2. Skapa och konfigurera Runbooks.

3. Skapa en logisk app som ska utlösa Automation-kontot och skicka ett e-postmeddelande om antalet är större än ett tröskelvärde (till exempel 160).

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har minst en ExpressRoute-gateway i distributionen.

* Du har grundläggande kunskaper om [Kör som-konton](../automation/manage-runas-account.md) i Azure Automation.

* Du är bekant med [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

* Du är van att använda Azure PowerShell. Azure PowerShell krävs för att samla in nätverks prefix i ExpressRoute-gatewayen. Mer information om Azure PowerShell i allmänhet finns i Azure PowerShell- [dokumentationen](/powershell/azure/).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Kommentarer och begränsningar

* Den anpassade aviseringen som beskrivs i den här artikeln är ett tillägg för att uppnå bättre drift och kontroll. Det är inte en ersättning för de interna aviseringarna i ExpressRoute.
* Data insamling för ExpressRoute-gatewayer körs i bakgrunden. Körningen kan vara längre än förväntat. För att undvika jobb köer måste upprepningen av arbets flödet konfigureras korrekt.
* Distributioner av skript eller ARM-mallar kan inträffa snabbare än den anpassade Alarm utlösaren. Detta kan leda till ökande antal nätverksprefix i ExpressRoute-Gateway över gränsen på 200 vägar.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Skapa och konfigurera konton

När du skapar ett Automation-konto i Azure Portal skapas ett [Kör som](../automation/automation-security-overview.md#run-as-accounts) -konto automatiskt. Det här kontot vidtar följande åtgärder:

* Skapar ett Azure Active Directory-program (Azure AD) med ett självsignerat certifikat. Själva kör som-kontot har ett certifikat som måste förnyas som standard varje år.

* Skapar ett tjänst objekts konto för programmet i Azure AD.

* Tilldelar sig själv deltagar rollen (Azure RBAC) på den Azure-prenumeration som används. Den här rollen hanterar Azure Resource Manager-resurser med hjälp av Runbooks.

För att kunna skapa ett Automation-konto måste du ha behörigheter och behörigheter. Mer information finns i [behörigheter som krävs för att skapa ett Automation-konto](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. skapa ett Automation-konto

Skapa ett Automation-konto med kör som-behörigheter. Instruktioner finns i [skapa ett Azure Automation-konto](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Lägg till Automation-konto" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. tilldela kör som-kontot en roll

Som standard tilldelas **deltagar** rollen till tjänstens huvud namn som används av ditt **Kör som** -konto. Du kan behålla standard rollen som tilldelats tjänstens huvud namn, eller så kan du begränsa behörigheterna genom att tilldela en [inbyggd roll](../role-based-access-control/built-in-roles.md) (till exempel läsare) eller en [anpassad roll](../active-directory/roles/custom-create.md).

 Använd följande steg för att fastställa vilken roll som ska tilldelas till tjänstens huvud namn som används av ditt kör som-konto:

1. Navigera till ditt Automation-konto. Gå till **konto inställningar** och välj **Kör som-konton**.

2. Välj **roller** för att visa den roll definition som används.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Tilldela rollen":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Skapa och konfigurera Runbooks

### <a name="1-install-modules"></a><a name="install-modules"></a>1. installera moduler

För att köra PowerShell-cmdlets i Azure Automation runbooks måste du installera några ytterligare Azure PowerShell AZ-moduler. Använd följande steg för att installera modulerna:

1. Öppna ditt Azure Automation-konto och navigera till **moduler**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Navigera till moduler":::

2. Sök i galleriet och importera följande moduler: **AZ. Accounts**, **AZ. Network**, **AZ. Automation** och **AZ. Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Sök och importera moduler" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. skapa en Runbook

1. Navigera till ditt Automation-konto om du vill skapa en PowerShell-Runbook. Under **process automatisering** väljer du panelen **Runbooks** och väljer sedan **skapa en Runbook**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Skapa Runbook.":::

2. Välj **skapa** för att skapa runbooken.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Välj Skapa.":::

3. Välj den nyligen skapade runbooken och välj sedan **Redigera**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Redigera runbooken":::

4. I **Redigera**, klistra in PowerShell-skriptet. [Exempel skriptet](#script) kan ändras och används för att övervaka ExpressRoute-gatewayer i en eller flera resurs grupper.

   Observera följande inställningar i exempel skriptet:

    * Mat ris **$rgList** innehåller listan över resurs grupper med ExpressRoute-gatewayer. Du kan anpassa List-baserade ExpressRoute-gatewayer.
    * Variabeln **$thresholdNumRoutes** definiera tröskelvärdet för antalet nätverks prefix som annonseras från en ExpressRoute-Gateway till lokala nätverk.

#### <a name="example-script"></a><a name="script"></a>Exempelskript

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. Spara och publicera runbooken

1. Välj **Spara** för att spara en utkast kopia av runbooken.
2. Välj **publicera** för att publicera runbooken som den officiella versionen av Runbook-flödet i Automation-kontot.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Spara och publicera runbooken.":::

När du kör PowerShell-skriptet samlas en lista med värden in:
 
* Resursgrupp

* ExpressRoute Gateway-namn

* IP-adress för den första BGP-peer (peer1)

* IP-adress för den andra BGP-peer (peer2)

* Antal nätverks prefix som har annonser ATS från ExpressRoute-gatewayen till den första BGP-peer-datorn (peer1)

* Antal nätverks prefix som annonseras från ExpressRoute-gatewayen till den andra BGP-peer (peer2)

* Timestamp

* Status, klassificerad som:

  * OK om antalet vägar är mindre än tröskelvärdet
  * "ALERT" om antalet vägar över ett tröskel värde
  * "Varning" om antalet nätverks prefix som annonseras till två BGP-peer är olika

* Aviserings meddelande för utförlig beskrivning av statusen (OK, AVISERING, varning)

PowerShell-skriptet konverterar insamlad information till JSON-utdata. I Runbook används PowerShell-cmdleten [Write-output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  som utdataström för att kommunicera information till klienten.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. validera runbooken

När runbooken har skapats måste den val IDE ras. Välj **Start** och kontrol lera utdata och fel för de olika jobb strömmarna.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Validera Runbook" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Skapa och konfigurera en Logic app

Azure Logic Apps är Orchestrator för alla processer för insamling och åtgärder. I följande avsnitt skapar du ett arbets flöde med hjälp av en Logic app.

### <a name="workflow"></a>Arbetsflöde

I det här arbets flödet skapar du en Logic-app som regelbundet övervakar ExpressRoute-gatewayer. Om det finns ett nytt objekt skickar logikappen ett e-postmeddelande för vart och ett. När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps arbets flöde":::

### <a name="1-create-a-logic-app"></a>1. skapa en logisk app

I **Logic App Designer** skapar du en Logic-app med hjälp av den **tomma Logic app** -mallen. Anvisningar finns i [skapa Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Tom mall":::

### <a name="2-add-a-trigger"></a>2. Lägg till en utlösare

Varje Logic app startas av en utlösare. En utlösare utlöses när en enskild händelse inträffar eller när ett angivet villkor uppfylls. Varje gång utlösaren utlöses skapar Azure Logic Apps-motorn en Logic App-instans som startar och kör ditt arbets flöde.

För att regelbundet köra en Logic-app som baseras på ett fördefinierat tids schema lägger du till det inbyggda **upprepnings programmet: schema** i arbets flödet. Skriv **schema** i rutan Sök. Välj **Utlösare**. Välj **upprepnings schema** i listan utlösare.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Upprepning: schema":::

I utlösaren för upprepnings schema kan du ange tids zonen och en upprepning för att upprepa det arbets flödet. Tillsammans definierar intervall och frekvens schemat för logikappens utlösare. Ta hänsyn till följande faktorer för att fastställa en rimlig lägsta upprepnings frekvens:

* PowerShell-skriptet i Automation-runbooken tar tid att slutföra. Körningen beror på antalet ExpressRoute-gatewayer som ska övervakas. En för kort upprepnings frekvens kommer att orsaka jobb köer.

* PowerShell-skriptet körs som ett jobb i bakgrunden. Den startar inte omedelbart. den körs efter en variabel fördröjning.

* En för kort upprepnings frekvens genererar en inaktive ras belastning på dina Azure ExpressRoute-gatewayer.

I slutet av arbets flödes konfigurationen kan du kontrol lera konsekvensen för upprepnings frekvensen genom att köra arbets flödet några gånger och sedan verifiera resultatet i **körnings historiken**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Skärm bild som visar upprepnings intervall och frekvens värden." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. skapa ett jobb

En Logic app får åtkomst till andra appar, tjänster och plattformen via anslutningar. Nästa steg i det här arbets flödet är att välja en anslutning för att komma åt Azure Automation kontot som definierades tidigare.

1. I **Logic Apps designer**, under **upprepning**, väljer du **nytt steg**. Under **Välj en åtgärd** och rutan Sök väljer du **alla**.
2. I rutan Sök skriver du **Azure Automation** och Sök. Välj **skapa jobb**. **Skapa jobb** kommer att användas för att utlösa den automatiserade Automation-Runbook som skapades tidigare.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Skapa jobb":::

3. Logga in med ett huvud namn för tjänsten. Du kan använda ett befintligt huvud namn för tjänsten, eller så kan du skapa ett nytt. Information om hur du skapar ett nytt huvud namn för tjänsten finns i [så här använder du portalen för att skapa ett tjänst huvud namn för Azure AD som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md). Välj **Anslut med tjänstens huvud namn**.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Skärm bild som visar avsnittet upprepning med åtgärden Anslut med tjänstens huvud namn markerat.":::

4. Skriv ett **anslutnings namn**, Lägg till ditt **klient-ID** (program-ID), **klient hemlighet** och klient **-ID**. Välj sedan **Skapa**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Anslut med tjänstens huvud namn":::

5. På sidan **skapa jobb** måste tjänstens huvud namn ha rollen "läsare" i **resurs gruppen** som är värd för Automation-kontot och "automatiserings jobb operatör" på **Automation-kontot**. Kontrol lera också att du har lagt till **Runbook-namnet** som en ny parameter.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Skärm bild som visar skapa jobb värden i upprepning, där du kan kontrol lera Runbook-namnet." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. Hämta jobbets utdata

1. Välj **Nytt steg**. Sök efter "Azure Automation". I listan **åtgärder** väljer du **Hämta utdata för jobb**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Jobbutdata":::

2. På sidan **Hämta jobbets utdata** anger du den information som krävs för att komma åt Automation-kontot. Välj den **prenumeration, resurs grupp** och det **Automation-konto** som du vill använda. Klicka i rutan **jobb-ID** . När listan med **dynamiskt innehåll** visas väljer du **jobb-ID**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Job-ID" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. parsa JSON

Informationen i utdata från Azure Automation skapa jobb åtgärd (föregående steg) genererar ett JSON-objekt. Logic Apps **parse JSON** är en inbyggd åtgärd för att skapa användarvänliga token från egenskaperna och deras värden i JSON-innehåll. Du kan sedan använda dessa egenskaper i ditt arbets flöde.

1. Lägg till en åtgärd. Under **åtgärden Hämta utdata för jobb – >** väljer du **nytt steg**.
2. Skriv "parsa JSON" i sökrutan **Välj en åtgärd** för att söka efter anslutningar som erbjuder den här åtgärden. Under listan **åtgärder** väljer du åtgärden **parsa JSON** för de data åtgärder som du vill använda.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Parsa JSON":::

3. Klicka i **innehålls** rutan. När listan med dynamiskt innehåll visas väljer du **innehåll**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Skärm bild som visar dialog rutan parsa JSON med innehåll valt." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Parsning av en JSON kräver ett schema. Schemat kan genereras med hjälp av utdata från Automation-runbooken. Öppna en ny webbläsarsession, kör Automation-runbooken och hämta utdata. Återgå till åtgärden **Logic Apps parsa JSON-dataåtgärder** . Längst ned på sidan väljer **du Använd exempel nytto last för att generera schemat**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Använd exempel nytto last för att skapa schema":::

5. Om du vill **Ange eller klistra in en exempel-JSON-nyttolast** klistrar du in utdata från Automation-runbooken och väljer **OK**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Klistra in exempel på nytto Last" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Ett schema genereras automatiskt genom att parsa JSON-indatakällans nytto Last.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Skapa schema" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. definiera och initiera en variabel

I det här steget i arbets flödet skapar vi ett villkor för att skicka ett larm via e-post. För en flexibel, anpassad formatering av ett e-postmeddelande med brödtext introduceras en hjälp variabel i arbets flödet.

1. Under **åtgärden Hämta utdata för jobb** väljer du **nytt steg**. Sök efter och välj **variabler** i sökrutan.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Skärm bild som visar dialog rutan Välj en åtgärd med variabel i sökrutan och markerade variabler.":::

2. Välj åtgärden **initiera variabel** i listan **åtgärder** .

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Initiera variabler":::

3. Ange namnet på variabeln. I **typ** väljer du **sträng**. Variabelns **värde** kommer att tilldelas senare i arbets flödet.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Skärm bild som visar parse JSON som är associerad med Initialize-variabeln, där du kan ange ett namn, en typ och ett värde." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. skapa en "för varje"-åtgärd

När JSON parsas lagrar åtgärden **parsa JSON-data åtgärder** innehållet i *bröd texten* . Du kan bearbeta utdata genom att skapa en upprepad "for each"-loop för varje objekt i matrisen.

1. Under **initiera variabel** väljer du **Lägg till en åtgärd**. Skriv "för varje" som filter i rutan Sök.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Skärm bild som visar dialog rutan Välj en åtgärd för varje i sökrutan och kontrollen vald.":::

2. I listan **åtgärder** väljer du åtgärden **för varje kontroll**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="För varje-kontroll":::

3. Klicka i text rutan **Välj utdata från föregående steg** . När listan med **dynamiskt innehåll** visas väljer du **texten**, som är utdata från den parsade JSON-filen.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Skärm bild som visar en initierad variabel som är associerad med för varje, som innehåller text rutan Välj utdata från föregående steg.":::

4. Vi vill ange ett villkor för varje element i JSON-brödtext. I åtgärds gruppen väljer du **kontroll**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Kontroll":::

5. I listan **åtgärder** väljer du **villkor-kontroll**. Condition-Control är en kontroll struktur som jämför data i arbets flödet med vissa värden eller fält. Du kan sedan ange olika åtgärder som körs baserat på om, data uppfyller villkoret.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Villkors kontroll":::

6. Ändra logik åtgärden till **eller** i roten av **villkors** åtgärd.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Eller" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Kontrol lera värdet för antalet nätverks prefix en ExpressRoute-Gateway annonserar till de två BGP-peer-datorerna. Antalet vägar är tillgängligt i "numRoutePeer1" och "numRoutePeer2" i **dynamiskt innehåll**. I rutan värde anger du värdet för **numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. Om du vill lägga till en annan rad i villkoret väljer du **Lägg till > Lägg till rad**. I den andra rutan, från **dynamiskt innehåll**, väljer du **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. Logik villkoret är sant när en av två dynamiska variabler, numRoute1 eller numRoute2, är större än tröskelvärdet. I det här exemplet är tröskelvärdet fast till 160 (80% av Max värdet för 200-vägar). Du kan ändra tröskelvärdet så att det passar dina behov. För konsekvens bör värdet vara samma värde som används i PowerShell-skriptet för Runbook.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Logik villkor":::

10. Under **om sant**, formatera och skapa åtgärder för att skicka aviseringen via e-post. I * * väljer du en åtgärd, söker och väljer **variabler**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Om sant":::

11. I variabler väljer du **Lägg till en åtgärd**. I listan **åtgärder** väljer du **ange variabel**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Skärm bild av avsnittet &quot;variabler&quot; med fliken &quot;Actions&quot; markerad och set Variable är markerad.":::

12. I **namn** väljer du variabeln med namnet **EmailBody** som du skapade tidigare. För **värde** klistrar du in det HTML-skript som krävs för att formatera e-postaviseringen. Använd det **dynamiska innehållet** för att inkludera värdena för JSON-brödtext. När du har konfigurerat de här inställningarna är resultatet att variabeln **Emailbody** innehåller all information som rör aviseringen i HTML-format.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Ange variabel":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. Lägg till e-postkopplingen

Logic Apps innehåller många e-postanslutningar. I det här exemplet lägger vi till en Outlook-koppling för att skicka aviseringen via e-post. Under **ange variabel** väljer du **Lägg till en åtgärd**. I **Välj en åtgärd** skriver du "skicka e-post" i sökrutan.

1. Välj **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Skicka e-post":::

2. I listan **åtgärder** väljer du **Skicka ett e-postmeddelande (v2)**.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Skicka ett e-postmeddelande (v2)":::

3. Logga in för att skapa en anslutning till Office 365 Outlook.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Logga in":::

4. I fältet **brödtext** klickar du på **Lägg till dynamiskt innehåll**. Lägg till variabeln **Emailbody** från panelen dynamiskt innehåll. Fyll i fälten **ämne** och **till** .

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Brödtext":::

5. Åtgärden **Skicka ett e-postmeddelande (v2)** Slutför arbets flödes konfigurationen.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="Skicka e-post v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. arbets flödes validering

Det sista steget är arbets flödes verifieringen. I **Logic Apps översikt** väljer du **Kör utlösare**. Välj **upprepning**. Arbets flödet kan övervakas och verifieras i **körnings historiken**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Kör utlösare":::

## <a name="next-steps"></a>Nästa steg

Mer information om hur du anpassar arbets flödet finns i [Azure Logic Apps](../logic-apps/logic-apps-overview.md).