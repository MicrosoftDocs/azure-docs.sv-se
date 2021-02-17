---
title: Skapa en python 3-Runbook (för hands version) i Azure Automation
description: I den här artikeln lär du dig att skapa, testa och publicera en enkel python 3-Runbook (för hands version).
services: automation
ms.subservice: process-automation
ms.date: 02/16/2021
ms.topic: tutorial
ms.openlocfilehash: c19f7e177d51a3de75e7d7ae2b83442e23efd243
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546150"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Självstudie: skapa en python 3-Runbook (för hands version)

Den här självstudien vägleder dig genom skapandet av en [python 3-Runbook](../automation-runbook-types.md#python-runbooks) (för hands version) i Azure Automation. Python-Runbooks kompileras under python 2 och 3. Du kan redigera koden för runbooken direkt med hjälp av text redigeraren i Azure Portal.

> [!div class="checklist"]
> * Skapa en enkel python-Runbook
> * Testa och publicera runbooken
> * Kör och spåra statusen för Runbook-jobbet
> * Uppdatera runbooken för att starta en virtuell Azure-dator med Runbook-parametrar

## <a name="prerequisites"></a>Förutsättningar

För att göra den här självstudien behöver du följande:

- En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Ett [Automation-konto för Azure](../automation-security-overview.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.

- En virtuell dator i Azure. Under den här självstudien kommer du att starta och stoppa den här datorn, så den bör inte vara en virtuell produktions dator.

- Du kan använda en Linux-eller Windows-Hybrid Runbook Worker för att köra python 3-Runbooks.Följ anvisningarna för att installera en [Linux](../automation-linux-hrw-install.md)-   eller [Windows](../automation-windows-hrw-install.md)-   hybrid Runbook Worker.Det finns inga särskilda krav för Linux-Hybrid Worker. Men om du vill använda en Windows Hybrid Worker för python 3-Runbooks, konfigurerar du att du är den dator som är värd för Runbook Worker baserat på om du bara stöder python 3, eller om python 2 och 3 behöver fungera.

   * Om du *bara* har python 2 eller python 3 installerade måste du lägga till sökvägen till mappen där python.exe finns i miljövariabeln **Path** . Om python.exe till exempel är i installations Sök vägen `C:\Python` måste sökvägen läggas till i miljövariabeln **Path** .

   * Om du har både python 2 och python 3 installerade och vill köra båda typerna av Runbooks måste du konfigurera följande miljövariabler:

     * Python 2 – Skapa en ny miljö variabel `PYTHON_2_PATH` som kallas och ange installationsmappen. Om installationsmappen till exempel är `C:\Python27` , måste den här sökvägen läggas till i variabeln.

     * Python 3 – skapa en ny miljö variabel `PYTHON_3_PATH` som kallas och ange installationsmappen. Om installationsmappen till exempel är `C:\Python3` , måste den här sökvägen läggas till i variabeln.

## <a name="create-a-new-runbook"></a>Skapa en ny Runbook

Du börjar med att skapa en enkel Runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).

2. Öppna listan över runbooks genom att välja **Runbooks** under **process automatisering** .

3. Välj **Lägg till en Runbook** för att skapa en ny Runbook.

4. Ge runbooken namnet **MyFirstRunbook-python**.

5. Välj **python 3** som **Runbook-typ**.

6. Välj **skapa** för att skapa runbooken och öppna text redigeraren.

## <a name="add-code-to-the-runbook"></a>Lägg till kod i Runbook

Nu ska du lägga till ett enkelt kommando för att skriva ut texten `Hello World` .

```python
print("Hello World!")
```

Välj **Spara** för att spara runbooken.

## <a name="test-the-runbook"></a>Testa runbooken

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en Runbook kör du dess utkast version och visar utdatan interaktivt.

1. Öppna **test** fönstret genom att välja **test fönster** .

2. Starta testet genom att välja **Starta** . Detta bör vara det enda aktiverade alternativet.

3. Ett [runbook-jobb](../automation-runbook-execution.md) skapas och dess status visas.
   Jobbets status börjar i **kö**, vilket anger att den väntar på att en Runbook Worker i molnet ska bli tillgänglig. Den ändras till att **Starta** när en arbets tagare anlitar jobbet och sedan **körs** när runbooken faktiskt börjar köras.

4. När runbook-jobbet är klart visas dess utdata. I så fall bör du se `Hello World` .

5. Stäng **test** fönstret för att återgå till arbets ytan.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbooken

Den Runbook som du har skapat är fortfarande i utkast läge. Du måste publicera den innan du kan köra den i produktion. När du publicerar en Runbook skriver du över den befintliga publicerade versionen med utkastet. I det här fallet har du inte någon publicerad version än eftersom du nyss skapade runbooken.

1. Välj **publicera** för att publicera runbooken och sedan **Ja** när du uppmanas till det.

2. Om du rullar åt vänster för att Visa runbooken på sidan **Runbooks** bör du se **redigerings statusen** **publicerad**.

3. Rulla tillbaka till höger för att visa fönstret för **MyFirstRunbook-python3**.

   Med alternativen över övre delen kan du starta runbooken, Visa runbooken eller schemalägga den så att den startar vid ett senare tillfälle.

4. Välj **Start** och välj sedan **OK** när fönstret **starta Runbook** öppnas.

5. Ett **jobb** fönster öppnas för det Runbook-jobb som du har skapat. Du kan stänga det här fönstret, men låt oss lämna det öppet så att du kan se jobbets förlopp.

6. Jobbets status visas i **jobb Sammanfattning** och matchar de status värden som du såg när du testade runbooken.

7. När Runbook-statusen är **slutförd** väljer du **utdata**. Fönstret **utdata** öppnas, där du kan se `Hello World` .

8. Stäng fönstret **utdata** .

9. Välj **alla loggar** för att öppna fönstret **strömmar** för Runbook-jobbet. Du bör bara se `Hello World` i utdataströmmen. Det här fönstret kan dock visa andra strömmar för ett Runbook-jobb, till exempel **verbose** och **Error**, om Runbook skriver till dem.

10. Stäng fönstret **strömmar** och fönstret **jobb** för att återgå till fönstret **MyFirstRunbook-python3** .

11. Välj **jobb** för att öppna sidan **jobb** för denna Runbook. Den här sidan visar alla jobb som har skapats av denna Runbook. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.

12. Du kan välja det här jobbet för att öppna samma **jobb** fönster som du visade när du startade runbooken. I det här fönstret kan du gå tillbaka i tiden och Visa information om alla jobb som har skapats för en viss Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Lägg till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser.
För att göra detta måste skriptet autentisera med hjälp av autentiseringsuppgifterna från ditt Automation-konto.

> [!NOTE]
> Automation-kontot måste ha skapats med tjänstens huvud namns funktion för att det ska finnas ett Kör som-certifikat.
> Om ditt Automation-konto inte har skapats med tjänstens huvud namn kan du autentisera enligt beskrivningen i [autentisera med Azures hanterings bibliotek för python](/azure/python/python-sdk-azure-authenticate).

1. Öppna text redigeraren genom att välja **Redigera** i fönstret **MyFirstRunbook-python3** .

2. Lägg till följande kod för att autentisera till Azure:

    ```python
    from OpenSSL import crypto 
    import binascii 
    from msrestazure import azure_active_directory 
    import adal 

    # Get the Azure Automation RunAs service principal certificate 
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate") 
    pks12_cert = crypto.load_pkcs12(cert) 
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey()) 
    
    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"] 
    thumbprint = runas_connection["CertificateThumbprint"] 
    tenant_id = runas_connection["TenantId"] 
    
    # Authenticate with service principal certificate 
    resource ="https://management.core.windows.net/" 
    authority_url = ("https://login.microsoftonline.com/"+tenant_id) 
    context = adal.AuthenticationContext(authority_url) 
    return azure_active_directory.AdalAuthentication( 
      lambda: context.acquire_token_with_client_certificate( 
          resource, 
          application_id, 
          pem_pkey, 
          thumbprint) 
    ) 
    ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Lägg till kod för att skapa python Compute Client och starta den virtuella datorn

Om du vill arbeta med virtuella Azure-datorer skapar du en instans av [Azure Compute-klienten för python](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Använd Compute-klienten för att starta den virtuella datorn. Lägg till följande kod i runbooken:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Där `MyResourceGroup` är namnet på den resurs grupp som innehåller den virtuella datorn och `TestVM` är namnet på den virtuella dator som du vill starta.

Testa och kör runbooken igen för att se att den startar den virtuella datorn.

## <a name="use-input-parameters"></a>Använda indataparametrar

Runbooken använder för närvarande hårdkodade värden för namnen på resurs gruppen och den virtuella datorn. Nu ska vi lägga till kod som hämtar dessa värden från indataparametrar.

Du kan använda `sys.argv` variabeln för att hämta parameter värden. Lägg till följande kod i Runbook omedelbart efter de andra- `import` satserna:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Detta importerar `sys` modulen och skapar två variabler för att lagra resurs gruppen och namn på virtuella datorer. Observera att elementet i argument listan, `sys.argv[0]` är namnet på skriptet och inte indata från användaren.

Nu kan du ändra de två sista raderna i runbooken så att de använder värdena för Indataparametern i stället för att använda hårdkodade värden:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

När du startar en python-Runbook, antingen från **test** -fönstret eller som en publicerad Runbook, kan du ange värden för parametrarna på sidan **starta Runbook** under **parametrar**.

När du har angett ett värde i den första rutan visas en sekund och så att du kan ange så många parameter värden som behövs.

Värdena är tillgängliga för skriptet i `sys.argv` matrisen som i den kod som du nyss lade till.

Ange namnet på din resurs grupp som värde för den första parametern, och namnet på den virtuella dator som ska starta som värdet för den andra parametern.

![Ange parameter värden](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Välj **OK** för att starta runbooken. Runbooken körs och startar den virtuella dator som du har angett.

## <a name="error-handling-in-python"></a>Fel hantering i python

Du kan också använda följande konventioner för att hämta olika strömmar från dina python-Runbooks, inklusive VARNINGs-, fel-och fel SÖKNINGs strömmar.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

I följande exempel visas den här konventionen som används i ett `try...except` block.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Runbook-typer, deras fördelar och begränsningar, se [Azure Automation Runbook-typer](../automation-runbook-types.md).

- Mer information om hur du utvecklar för Azure med python finns i [Azure för python-utvecklare](/azure/python/).

- För att se exempel på python 3-Runbooks, se [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
