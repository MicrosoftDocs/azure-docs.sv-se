---
title: Distribuera diagnostikverktyget för Windows Virtual Desktop (klassisk) – Azure
description: Distribuera verktyget Diagnostics UX för Windows Virtual Desktop (klassisk).
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bdba36e2ff4721bd024369788c7dc04f066e9a5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565588"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Distribuera diagnostikverktyget för Windows Virtual Desktop (klassiskt)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows.

Så här kan du använda diagnostikverktyget för virtuella Windows-datorer:

- Sök efter diagnostiska aktiviteter (hantering, anslutning eller feed) för en enskild användare under en period om en vecka.
- Samla in sessions värd information för anslutnings aktiviteter från din Log Analytics-arbetsyta.
- Granska prestanda information för virtuella datorer (VM) för en viss värd.
- Se vilka användare som är inloggade på sessions värden.
- Skicka meddelande till aktiva användare på en angiven värd för sessionen.
- Logga ut användare från en sessions värd.

## <a name="prerequisites"></a>Förutsättningar

Du måste skapa en Azure Active Directory app-registrering och en Log Analytics arbets yta innan du kan distribuera Azure Resource Manager-mallen för verktyget. Du eller administratören behöver dessa behörigheter för att göra det:

- Azure-Prenumerationens ägare
- Behörighet att skapa resurser i din Azure-prenumeration
- Behörighet att skapa en Azure AD-App
- RDS-ägare eller deltagar rättigheter

Du måste också installera dessa två PowerShell-moduler innan du börjar:

- [Azure PowerShell-modul](/powershell/azure/install-az-ps)
- [Azure AD-modul](/powershell/azure/active-directory/install-adv2)

Se till att du har ditt prenumerations-ID klart för när du loggar in.

När du har allt i ordning kan du skapa Azure AD-appens registrering.

## <a name="create-an-azure-active-directory-app-registration"></a>Skapa en Azure Active Directory app-registrering

I det här avsnittet visas hur du använder PowerShell för att skapa en Azure Active Directory-app med ett huvud namn för tjänsten och hämta API-behörigheter för den.

>[!NOTE]
>API-behörigheterna är Windows Virtual Desktop, Log Analytics och Microsoft Graph API-behörigheter läggs till i Azure Active Directory-programmet.

1. Öppna PowerShell som administratör.
2. Logga in på Azure med ett konto som har ägar-eller deltagar behörighet för den Azure-prenumeration som du vill använda för diagnostikverktyget:
   ```powershell
   Login-AzAccount
   ```
3. Logga in på Azure AD med samma konto:
   ```powershell
   Connect-AzureAD
   ```
4. Gå till [RDS-templates GitHub-lagrings platsen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) och kör **CreateADAppRegistrationforDiagnostics.ps1** -skriptet i PowerShell.
5.  När du uppmanas att namnge din app anger du ett unikt namn på appen.


När skriptet har körts ska det Visa följande saker i resultatet:

-  Ett meddelande som bekräftar att din app nu har en roll tilldelning för tjänstens huvud namn.
-  Ditt klient-ID och den hemliga klient nyckeln som du behöver för när du distribuerar diagnostikverktyget.

Nu när du har registrerat din app är det dags att konfigurera din Log Analytics-arbetsyta.

## <a name="configure-your-log-analytics-workspace"></a>Konfigurera din Log Analytics arbets yta

För bästa möjliga upplevelse rekommenderar vi att du konfigurerar din Log Analytics arbets yta med följande prestanda räknare som gör att du kan härleda användar upplevelsen i en fjärrsession. En lista över rekommenderade räknare med föreslagna tröskelvärden finns i [tröskelvärden för Windows prestanda räknare](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Skapa en Azure Log Analytics-arbetsyta med PowerShell

Du kan köra ett PowerShell-skript för att skapa en Log Analytics arbets yta och konfigurera rekommenderade Windows-prestandaräknare för övervakning av användar upplevelse och app-prestanda.

>[!NOTE]
>Om du redan har en befintlig Log Analytics arbets yta som du har skapat utan det PowerShell-skript som du vill använda, kan du gå vidare till [validera skript resultatet i Azure Portal](#validate-the-script-results-in-the-azure-portal).

Köra PowerShell-skriptet:

1.  Öppna PowerShell som administratör.
2.  Gå till [RDS-templates GitHub-lagrings platsen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) och kör **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** -skriptet i PowerShell.
3. Ange följande värden för parametrarna:

    - För **ResourceGroupName** anger du namnet på resurs gruppen.
    - För **LogAnalyticsWorkspaceName** anger du ett unikt namn för din Log Analytics-arbetsyta.
    - För **plats** anger du den Azure-region som du använder.
    - Ange **ID för Azure-prenumerationen** som du hittar i Azure Portal under **prenumerationer**.

4. Ange autentiseringsuppgifterna för en användare med delegerad administratörs åtkomst.
5. Logga in på Azure Portal med samma användares autentiseringsuppgifter.
6. Skriv ner eller ange ett LogAnalyticsWorkspace-ID för senare.
7. Om du konfigurerar Log Analytics-arbetsytan med PowerShell-skriptet bör dina prestanda räknare redan vara konfigurerade och du kan gå vidare för att [validera skript resultatet i Azure Portal](#validate-the-script-results-in-the-azure-portal). Annars fortsätter du till nästa avsnitt.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Konfigurera Windows-prestandaräknare i din befintliga Log Analytics-arbetsyta

Det här avsnittet är för användare som vill använda en befintlig Azure Log Analytics-arbetsyta som skapats utan PowerShell-skriptet i föregående avsnitt. Om du inte har använt skriptet måste du konfigurera de rekommenderade Windows-prestandaräknare manuellt.

Så här konfigurerar du de rekommenderade prestanda räknarna manuellt:

1. Öppna din webbläsare och logga in på [Azure Portal](https://portal.azure.com/) med ditt administratörs konto.
2. Gå sedan till **Log Analytics arbets ytor** för att granska de konfigurerade Windows-prestandaräknare.
3. I avsnittet **Inställningar** väljer du  **Avancerade inställningar**.
4. Efter det navigerar du till **data**  >  **Windows prestanda räknare** och lägger till följande räknare:

    -   Logisk disk ( \* ) \\ % ledigt utrymme
    -   Logisk disk (C:) \\ Gnm. diskkölängd
    -   Tillgängligt minne ( \* ) \\ megabyte
    -   Processor information ( \* ) \\ processor tid
    -   Fördröjning för användarindata per session ( \* ) \\ Max fördröjning för indata

Läs mer om prestanda räknare [i prestanda data källor för Windows och Linux i Azure Monitor](../../azure-monitor/agents/data-sources-performance-counters.md).

>[!NOTE]
>Eventuella ytterligare räknare som du konfigurerar visas inte i själva diagnostikverktyget. Om du vill att det ska visas i diagnostikverktyget måste du konfigurera verktygets konfigurations fil. Instruktioner för hur du gör detta med avancerad administration blir tillgängligt i GitHub vid ett senare tillfälle.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Verifiera skript resultatet i Azure Portal

Innan du fortsätter distribuera diagnostikverktyget rekommenderar vi att du verifierar att ditt Azure Active Directory program har API-behörigheter och att Log Analytics arbets ytan har de förkonfigurerade Windows-prestandaräknare.

### <a name="review-your-app-registration"></a>Granska registreringen av appen

För att se till att din app-registrering har API-behörigheter:

1. Öppna en webbläsare och Anslut till [Azure Portal](https://portal.azure.com/) med ditt administratörs konto.
2. Gå till **Azure Active Directory**.
3. Gå till **Appregistreringar** och välj **alla program**.
4. Titta efter din Azure AD-App-registrering med samma app-namn som du angav i steg 5 i [skapa en Azure Active Directory app-registrering](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Granska din Log Analytics-arbetsyta

För att se till att din Log Analytics arbets yta har de förkonfigurerade Windows-prestandaräknare:

1. Gå till **Log Analytics arbets ytor** i [Azure Portal](https://portal.azure.com/)och granska de konfigurerade Windows-prestandaräknare.
2. Under **Inställningar** väljer du **Avancerade inställningar**.
3. Sedan går du till **data**  >  **Windows prestanda räknare**.
4. Kontrol lera att följande räknare är förkonfigurerade:

   - Logisk disk ( \* ) \\ % ledigt utrymme: visar mängden ledigt utrymme för det totala användbara utrymmet på disken som en procent andel.
   - Logisk disk (C:) \\ Gnm. diskkölängd: längden på disk överförings förfrågan för din C-enhet. Värdet får inte överstiga 2 under en kort tids period.
   - Tillgängligt minne ( \* ) \\ megabyte: tillgängligt minne för systemet i megabyte.
   - Processor information ( \* ) \\ processor tid: procent andelen tid som processorn ägnat åt att köra en icke-inaktiv tråd.
   - Fördröjning för användarindata per session ( \* ) \\ Max fördröjning för indata

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Ansluta till virtuella datorer i din Log Analytics-arbetsyta

För att kunna visa hälso tillståndet för virtuella datorer måste du aktivera Log Analytics anslutningen. Följ de här stegen för att ansluta dina virtuella datorer:

1. Öppna en webbläsare och logga in på [Azure Portal](https://portal.azure.com/) med ditt administratörs konto.
2. Gå till din Log Analytics-arbetsyta.
3. I den vänstra panelen, under arbets ytans data källor, väljer du **virtuella datorer**.
4. Välj namnet på den virtuella dator som du vill ansluta till.
5. Välj **Anslut**.

## <a name="deploy-the-diagnostics-tool"></a>Distribuera diagnostikverktyget

Distribuera Azures resurs hanterings mall för diagnostikverktyget:

1.  Gå till [sidan med GitHub Azure-RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Distribuera mallen till Azure och följ instruktionerna i mallen. Se till att du har följande tillgängliga information:

    -   Client-Id
    -   Client-Secret
    -   Log Analytics-arbetsytans ID

3.  När indataparametrarna har angetts accepterar du villkoren och väljer sedan **köp**.

Distributionen tar 2 – 3 minuter. När distributionen är klar går du till resurs gruppen och kontrollerar att resurserna för webbappen och App Service-planen finns där.

Därefter måste du ange omdirigerings-URI: n.

### <a name="set-the-redirect-uri"></a>Ange omdirigerings-URI

Ange omdirigerings-URI: n:

1.  I [Azure Portal](https://portal.azure.com/)går du till **app Services** och letar upp det program som du skapade.
2.  Gå till sidan Översikt och kopiera URL: en som du hittar där.
3.  Navigera till **app-registreringar** och välj den app som du vill distribuera.
4.  I den vänstra panelen under avsnittet hantera väljer du **autentisering**.
5.  Ange önskad omdirigerings-URI i text rutan **omdirigerings-URI** och välj sedan **Spara** i det övre vänstra hörnet på menyn.
6. Välj **webbplats** under typ på den nedrullningsbara menyn.
7. Ange URL: en från sidan Översikt över appen och Lägg till **/Security/signin-callback** i slutet av den. Exempel: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![Sidan omdirigerings-URI](../media/redirect-uri-page.png)

8. Gå nu till dina Azure-resurser, Välj resursen Azure App tjänster med det namn som du angav i mallen och navigera till den URL som är kopplad till den. (Till exempel om namnet på appen som du använde i mallen var `contosoapp45` , är din associerade URL <http://contoso.azurewebsites.net> ).
9. Logga in med lämpligt Azure Active Directory användar konto.
10.   Välj **Acceptera**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuera diagnostikverktyget

Innan du gör diagnostikverktyget tillgängligt för dina användare måste du kontrol lera att de har följande behörigheter:

- Användare behöver Läs behörighet för Log Analytics. Mer information finns i [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../../azure-monitor/roles-permissions-security.md).
-  Användare behöver också Läs behörighet för den virtuella Windows-klienten för fjärr skrivbord (RDS-läsar rollen). Mer information finns i [delegerad åtkomst i Windows Virtual Desktop](delegated-access-virtual-desktop-2019.md).

Du måste också ge användarna följande information:

- Appens URL
- Namnen på klient gruppen som de kan komma åt.

## <a name="use-the-diagnostics-tool"></a>Använda verktyget diagnostik

När du har loggat in på ditt konto med hjälp av den information som du har fått från din organisation, har du det UPN som är redo för den användare som du vill fråga aktiviteter för. Med en sökning får du alla aktiviteter under den angivna aktivitets typen som har inträffat under den senaste veckan.

### <a name="how-to-read-activity-search-results"></a>Så här läser du resultat från aktivitets sökning

Aktiviteter sorteras efter tidsstämpel, med den senaste aktiviteten först. Om resultatet returnerar ett fel, kontrol lera först om det är ett tjänst fel. För tjänst fel skapar du ett support ärende med aktivitets informationen som hjälper oss att felsöka problemet. Alla andra fel typer kan vanligt vis lösas av användaren eller administratören. En lista över de vanligaste fel scenarierna och hur du löser dem finns i [identifiera och diagnostisera problem](diagnostics-role-service-2019.md#common-error-scenarios).

>[!NOTE]
>Tjänst fel kallas "externa fel" i den länkade dokumentationen. Detta kommer att ändras när vi uppdaterar PowerShell-referensen.

Anslutnings aktiviteter kan ha fler än ett fel. Du kan expandera aktivitets typen för att se andra fel som användaren har kommit över. Välj namnet på felkoden för att öppna en dialog ruta för att se mer information om den.

### <a name="investigate-the-session-host"></a>Undersök sessionens värd

I Sök resultaten söker du efter och väljer den värd för sessionen som du vill ha information om.

Du kan analysera sessionens värd hälsa:

- Baserat på ett fördefinierat tröskelvärde kan du hämta hälso information om sessionens värd som Log Analytics frågor.
- När det inte finns någon aktivitet eller om sessions värden inte är ansluten till Log Analytics är informationen inte tillgänglig.

Du kan också interagera med användare på värd för sessionen:

- Du kan antingen logga ut eller skicka ett meddelande till inloggade användare.
- Användaren som du ursprungligen sökte efter är markerad som standard, men du kan också välja ytterligare användare för att skicka meddelanden eller logga ut flera användare på samma gång.

### <a name="windows-performance-counter-thresholds"></a>Tröskelvärden för Windows prestanda räknare

- Logisk disk ( \* ) \\ % ledigt utrymme:

    - Visar procent andelen av det totala användbara utrymmet på den logiska disken som är kostnads fri.
    - Tröskelvärde: mindre än 20% har marker ATS som ohälsosamt.

- Logisk disk (C:) \\ Gnm. Kölängd för disk:

    - Representerar lagrings systemets villkor.
    - Tröskelvärde: högre än 5 har marker ATS som ohälsosamt.

- Tillgängligt minne ( \* ) \\ megabyte:

    - Systemets tillgängliga minne.
    - Tröskelvärde: mindre än 500 megabyte har marker ATS som ohälsosamt.

- Processor information ( \* ) \\ processor tid:

    - Tröskel: högre än 80% har marker ATS som ohälsosam.

- [Fördröjning av användarindata per session ( \* ) \\ högsta fördröjning](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - Tröskel: högre än 2000 MS har marker ATS som ohälsosam.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du övervakar aktivitets loggar i [använda diagnostik med Log Analytics](diagnostics-log-analytics-2019.md).
- Läs om vanliga fel scenarier och hur du åtgärdar dem vid [identifiering och diagnostisering av problem](diagnostics-role-service-2019.md).