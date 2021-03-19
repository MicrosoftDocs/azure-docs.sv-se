---
title: Installera Azure FarmBeats
description: Den här artikeln beskriver hur du installerar Azure-FarmBeats i din Azure-prenumeration
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 5863dcc20fb13f1bb203c68ad168655371130601
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674565"
---
# <a name="install-azure-farmbeats"></a>Installera Azure FarmBeats

Den här artikeln beskriver hur du installerar Azure-FarmBeats i din Azure-prenumeration.

Azure FarmBeats är ett affärs-till-affärs erbjudande som är tillgängligt på Azure Marketplace. Det möjliggör agg regering av data uppsättningar i jordbruket mellan leverantörer och generering av åtgärds bara insikter. Azure FarmBeats gör detta genom att göra det möjligt för dig att bygga artificiell intelligens (AI) eller Machine Learning-modeller (ML) baserat på säkrade data uppsättningar. De två huvud komponenterna i Azure FarmBeats är:

- **Datahub**: ett API-lager som möjliggör agg regerings-, normaliserings-och contextualization för olika jordbruks data uppsättningar mellan olika leverantörer.

- **Accelerator**: webb program som byggts ovanpå Datahub. Den hoppar – startar din modell utveckling och visualisering. Acceleratorn använder Azure FarmBeats-API: er för att demonstrera visualisering av inmatade sensor data som diagram och visualisering av modellens utdata som Maps.

## <a name="general-information"></a>Allmän information

### <a name="components-installed"></a>Installerade komponenter

När du installerar Azure-FarmBeats är följande resurser etablerade i din Azure-prenumeration:

| Azure-resurser har installerats  | Azure FarmBeats-komponent  |
|---------|---------|
| Application Insights   |      Datahub & Accelerator      |
| App Service     |     Datahub & Accelerator     |
| App Service-plan   | Datahub & Accelerator  |
| API-anslutning    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & Accelerator      |
| Azure Batch-konto    | Datahub   |
| Azure Key Vault |  Datahub & Accelerator        |
| Azure Maps konto       |     Gas    |
| Event Hub-namnområde    |     Datahub      |
| Logikapp      |  Datahub       |
| Lagringskonto      |     Datahub & Accelerator      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Kostnader som uppstår

Kostnaden för Azure FarmBeats är en mängd kostnader för de underliggande Azure-tjänsterna. Pris information för Azure-tjänster kan beräknas med hjälp av [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator). Den faktiska kostnaden för den totala installationen varierar beroende på användningen. Den stabila status kostnaden för de två komponenterna är:

- Datahub – mindre än $10 per dag
- Accelerator-mindre än $2 per dag

### <a name="regions-supported"></a>Regioner som stöds

Azure-FarmBeats stöds för närvarande i offentliga moln miljöer i följande regioner:

- Australien, östra
- Central US
- East US
- USA, östra 2
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra
- Asien, östra
- Asien, Sydostasien

### <a name="time-taken"></a>Åtgången tid

Hela installationen av Azure FarmBeats, inklusive förberedelser och installation tar mindre än en timme.

## <a name="prerequisites"></a>Förutsättningar

Du måste utföra följande steg innan du påbörjar den faktiska installationen av Azure FarmBeats:

### <a name="verify-permissions"></a>Verifiera behörigheter

Du behöver följande behörigheter i Azure-klienten för att installera Azure FarmBeats:

- Klient organisation – AAD-app Creator
- Prenumeration – ägare
- Resurs grupp där FarmBeats installeras – ägare

De första två behörigheterna krävs för att [skapa AAD](#create-an-aad-application) -programsteget. Om det behövs kan du få någon med rätt behörighet för att skapa AAD-programmet.

Den person som kör FarmBeats-installationen från Marketplace måste vara ägare till resurs gruppen där FarmBeats installeras. För prenumerations ägare sker detta automatiskt när resurs gruppen skapas. För andra måste du i förväg skapa resurs gruppen och be prenumerations ägaren att bli ägare till resurs gruppen.

Du kan kontrol lera åtkomst behörigheterna i Azure Portal genom att följa anvisningarna i [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/check-access.md).

### <a name="decide-subscription-and-region"></a>Bestäm prenumeration och region

Du behöver ID för Azure-prenumeration och den region där du vill installera Azure-FarmBeats. Välj en av de regioner som anges i avsnittet [regioner som stöds](#regions-supported) .

Anteckna **ID för Azure-prenumerationen** och Azure- **regionen**.

### <a name="create-an-aad-application"></a>Skapa ett AAD-program

Azure FarmBeats kräver skapande och registrering av Azure Active Directory program. För att kunna köra skriptet för att skapa AAD, krävs följande behörigheter:

- Klient organisation – AAD-app Creator
- Prenumeration – ägare

Kör följande steg i en Cloud Shell-instans med hjälp av PowerShell-miljön. Första gången användaren uppmanas att välja en prenumeration och skapa ett lagrings konto. Slutför installationen enligt anvisningarna.

1. Hämta [skriptet för AAD app Generator](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Som standard hämtas filen till din arbets katalog. Navigera till katalogen.

    ```azurepowershell-interactive
        cd
    ```

3. Kör AAD-skriptet

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Skriptet frågar efter följande tre indata:

    - **FarmBeats webbplats namn**: det här är det unika URL-prefixet för ditt FarmBeats-webbprogram. Om prefixet redan används, kommer skriptet att fel. När din FarmBeats-distribution har installerats kan du komma åt den från https:// \<FarmBeats-website-name> . azurewebsites.net och Swagger-API: erna kommer att finnas på https:// \<FarmBeats-website-name> -API.azurewebsites.net

    - **Azure-inloggnings-ID**: Ange Azures inloggnings-ID för den användare som du vill lägga till som administratör för FarmBeats. Den här användaren kan sedan ge åtkomst till FarmBeats-webbprogram till andra användare. Inloggnings-ID: t är vanligt vis formuläret john.doe@domain.com . Azure UPN stöds också.

    - **Prenumerations-ID**: Detta är ID: t för den prenumeration som du vill installera Azure-FarmBeats i

5. AAD-skriptet tar cirka 2 minuter att köra och matar ut värden på skärmen samt till en JSON-fil i samma katalog. Om du har någon annan som kör skriptet ber du dem att dela dessa utdata med dig.

### <a name="create-sentinel-account"></a>Skapa Sentinel-konto

Med din Azure FarmBeats-installation kan du hämta satellit bilder från Europeiska rums Byråens [kontroll 2](https://scihub.copernicus.eu/) satellit uppdrag för din server grupp. Om du vill konfigurera den här installationen behöver du ett Sentinel-konto.

Följ stegen för att skapa ett kostnads fritt konto med Sentinel:

1. Gå till den officiella [registrerings](https://aka.ms/SentinelRegistration) sidan.
2. Ange nödvändig information (förnamn, efter namn, användar namn, lösen ord och e-post-ID) och fyll i formuläret.
3. En verifierings länk skickas till det registrerade e-post-ID: t. Välj länken som finns i e-postmeddelandet och slutför verifieringen.

Registrerings processen har slutförts. Anteckna ditt **Sentinel-användarnamn** och **kontroll lösen ord**, när verifieringen också har slutförts.

## <a name="install"></a>Installera

Du är nu redo att installera FarmBeats. Starta installationen genom att följa stegen nedan:

1. Logga in på Azure-portalen. Välj ditt konto i det övre högra hörnet och växla till den Azure AD-klient där du vill installera Azure-FarmBeats.

2. Gå till Azure Marketplace i portalen och Sök efter **Azure-FarmBeats** på Marketplace.

3. Ett nytt fönster med en översikt över Azure-FarmBeats visas. Välj **Skapa**.

4. Ett nytt fönster visas. Slutför registrerings processen genom att välja rätt prenumeration, resurs grupp och plats där du vill installera Azure-FarmBeats.

5. Ange den e-postadress som ska ta emot eventuella tjänst aviseringar som är relaterade till Azure FarmBeats i avsnittet **FarmBeats service Alerts** . Välj **Nästa** längst ned på sidan för att flytta till fliken **beroenden** .

    ![Fliken grunder](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Kopiera de enskilda posterna från utdata från [AAD-skript](#create-an-aad-application) till indata i avsnittet AAD-program.

7. Ange användar namn och lösen ord för [Sentinel-kontot](#create-sentinel-account) i avsnittet Sentinel-konto. Välj **Nästa** för att gå till fliken **Granska + skapa** .

    ![Fliken beroenden](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. När den angivna informationen har verifierats väljer du **OK**. Sidan Användningsvillkor visas. Granska villkoren och välj **skapa** för att starta installationen. Du kommer att omdirigeras till sidan där du kan följa installations förloppet.

När installationen är klar kan du verifiera installationen och börja använda FarmBeats-portalen genom att gå till namnet på webbplatsen som du angav under installationen: https:// \<FarmBeats-website-name> . azurewebsites.net. Du bör se FarmBeats-användargränssnittet med ett alternativ för att skapa Server grupper.

**Datahub** finns på https:// \<FarmBeats-website-name> -API.azurewebsites.net/Swagger. Här visas de olika FarmBeats API-objekten och utför REST-åtgärder på API: erna.

## <a name="upgrade"></a>Uppgradera

Om du vill uppgradera FarmBeats till den senaste versionen kör du följande steg i en Cloud Shell instans med hjälp av PowerShell-miljön. Användaren måste vara ägare till den prenumeration där FarmBeats är installerad.

Första gången användaren uppmanas att välja en prenumeration och skapa ett lagrings konto. Slutför installationen enligt anvisningarna.

1. Hämta [uppgraderings skriptet](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Som standard hämtas filen till din arbets katalog. Navigera till katalogen.

    ```azurepowershell-interactive
        cd
    ```

3. Kör uppgraderings skriptet

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Sökvägen till input.jsi filen är valfri. Om inget anges frågar skriptet efter alla nödvändiga indata. Uppgraderingen ska slutföras inom 30 minuter.

## <a name="uninstall"></a>Avinstallera

Slutför följande steg för att avinstallera Azure FarmBeats Datahub eller Accelerator:

1. Logga in på Azure Portal och **ta bort resurs grupperna** där dessa komponenter är installerade.

2. Gå till Azure Active Directory & **ta bort Azure AD-programmet** som är länkat till Azure FarmBeats-installationen.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du installerar Azure-FarmBeats i din Azure-prenumeration. Nu kan du lära dig hur du [lägger till användare](manage-users-in-azure-farmbeats.md#manage-users) till din Azure FarmBeats-instans.