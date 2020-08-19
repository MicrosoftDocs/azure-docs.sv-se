---
title: Konfigurera en instans och autentisering (skript)
titleSuffix: Azure Digital Twins
description: Se hur du konfigurerar en instans av tjänsten Azure Digitals dubbla tjänster genom att köra ett skript för automatisk distribution
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 86597521f762237b5c4bc9a7a5268d7dae1303b4
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588011"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Konfigurera en digital Azure-instans och autentisering (skript)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln slutför de här stegen genom att köra ett [ **skript exempel för automatiserad distribution** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) som effektiviserar processen. 
* Om du vill visa de manuella CLI-stegen som skriptet körs genom i bakgrunden, se CLI-versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (CLI)*](how-to-set-up-instance-cli.md).
* Om du vill visa de manuella stegen enligt Azure Portal, se Portal versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Kör distributions skriptet

I den här artikeln används ett digitalt kod exempel för Azure för att distribuera en digital Digitals-instans i Azure och den nödvändiga autentiseringen är halv automatiskt. Det kan också användas som utgångs punkt för att skriva dina egna skriptbaserade interaktioner.

Exempel skriptet skrivs i PowerShell. Den är en del av [Azures digitala dubbla exempel](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/), som du kan ladda ned till datorn genom att gå till den exempel länken och välja hämtnings knappen för att *Ladda ned ZIP* under rubriken.

I den hämtade exempel mappen finns distributions skriptet på _Azure_Digital_Twins_samples.zip > skript > **deploy.ps1** _.

Här följer stegen för att köra distributions skriptet i Cloud Shell.
1. Gå till ett [Azure Cloud Shell](https://shell.azure.com/) -fönster i webbläsaren. Logga in med det här kommandot:
    ```azurecli-interactive
    az login
    ```
    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida. Annars öppnar du en webb sida på *https://aka.ms/devicelogin* och anger den auktoriseringskod som visas i din terminal.
 
2. När du har loggat in tittar du på det Cloud Shell fönstrets ikon fält. Välj ikonen "Ladda upp/ladda ned filer" och välj "Ladda upp".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell fönster som visar val av överförings alternativ":::

    Navigera till _**deploy.ps1**_ -filen på din dator och tryck på "öppna". Detta kommer att överföra filen till Cloud Shell så att du kan köra den i fönstret Cloud Shell.

3. Kör skriptet genom `./deploy.ps1` att skicka kommandot i Cloud Shells fönstret. När skriptet körs genom de automatiserade konfigurations stegen uppmanas du att skicka in följande värden:
    * För instansen: *prenumerations-ID* för din Azure-prenumeration som ska användas
    * För-instansen: en *plats* där du vill distribuera instansen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * För instansen: ett *resurs grupp* namn. Du kan använda en befintlig resurs grupp eller ange ett nytt namn på en som ska skapas.
    * För-instansen: ett *namn* för din Azure Digital-instansen. Namnet på den nya instansen måste vara unikt inom regionen för din prenumeration (vilket innebär att om din prenumeration har en annan Azure Digital-instans i den region som redan använder det namn du väljer, blir du ombedd att välja ett annat namn).
    * För app-registreringen: ett *visnings namn för Azure AD-program* som ska associeras med registreringen. Den här appens registrering är den plats där du konfigurerar åtkomst behörigheter till [Azure Digitals dubbla API: er](how-to-use-apis-sdks.md). Senare kommer klient programmet att autentiseras mot appens registrering och därför beviljas de konfigurerade åtkomst behörigheterna till API: erna.
    * För app-registreringen: en *Azure AD Application svars-URL* för Azure AD-programmet. Du kan använda `http://localhost` .

Skriptet skapar en Azure Digital-instansen, tilldelar din Azure-användare rollen *Azure Digitals-ägare (för hands version)* på instansen och konfigurerar en Azure AD-App-registrering för din klient app som ska användas.

Här är ett utdrag från utmatnings loggen från skriptet:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell fönstret visar logg över indata och utdata genom körningen av distributions skriptet" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Om skriptet har slutförts kommer den slutliga utskriften att stå `Deployment completed successfully` . Annars kan du åtgärda fel meddelandet och köra skriptet igen. De steg som du redan har slutfört kommer att kringgås och börja begära inmatningar vid den punkt där du slutade.

När skriptet har slutförts har du nu en Azure Digital-instans som är redo att användas med behörigheter för att hantera den och har angett behörighet för en klient app för att få åtkomst till den.

> [!NOTE]
> Skriptet tilldelar för närvarande den nödvändiga hanterings rollen i Azure Digitals (för hands version) av Azure Digitals (för*hands version)* till samma användare som kör skriptet från Cloud Shell. Om du behöver tilldela rollen till någon annan som ska hantera instansen kan du göra det nu via Azure Portal ([instruktioner](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) eller CLI ([instruktioner](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

## <a name="collect-important-values"></a>Samla in viktiga värden

Det finns flera viktiga värden från de resurser som konfigureras av skriptet som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen. I det här avsnittet ska du använda [Azure Portal](https://portal.azure.com) för att samla in dessa värden. Du bör spara dem på en säker plats eller gå tillbaka till det här avsnittet för att hitta dem senare när du behöver dem.

Om andra användare kommer att program mera mot instansen bör du även dela dessa värden med dem.

### <a name="collect-instance-values"></a>Samla in instans värden

I [Azure Portal](https://portal.azure.com)kan du hitta din Azure Digital-instansen genom att söka efter namnet på din instans i portalens sökfält.

Om du markerar den öppnas sidan med instansens *Översikt* . Anteckna *namn*, *resurs grupp*och *värdnamn*. Du kan behöva dessa senare för att identifiera och ansluta till din instans.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markera viktiga värden från instansens översikts sida":::

### <a name="collect-app-registration-values"></a>Samla in registrerings värden för appar 

Det finns två viktiga värden från App-registreringen som kommer att behövas senare för att [skriva kod för klientautentisering för Azure Digitals dubbla API: er](how-to-authenticate-client.md). 

Du hittar dem genom att följa [den här länken](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) för att gå till översikts sidan för Azure AD-appen registrering i Azure Portal. Den här sidan visar alla app-registreringar som har skapats i din prenumeration.

Du bör se den app-registrering som du nyss skapade i den här listan. Välj den för att öppna informationen:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Portal visning av viktiga värden för appens registrering":::

Anteckna *program* -ID och *katalog (klient)-ID: t* som **visas på sidan** . Om du inte är den person som ska skriva kod för klient program måste du dela dessa värden med den person som ska vara.

## <a name="verify-success"></a>Verifieringen lyckades

Om du vill kontrol lera skapandet av dina resurser och behörigheter som har skapats av skriptet kan du titta på dem i [Azure Portal](https://portal.azure.com).

### <a name="verify-instance"></a>Verifiera instans

För att kontrol lera att din instans har skapats går du till [sidan Azure Digitals dubbla](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) i Azure Portal. Den här sidan visar alla dina Azure Digitals dubbla instanser. Leta efter namnet på den nyligen skapade instansen i listan.

### <a name="verify-user-role-assignment"></a>Verifiera tilldelning av användar roll

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Kom ihåg att skriptet för närvarande tilldelar den här nödvändiga rollen till samma användare som kör skriptet från Cloud Shell. Om du behöver tilldela rollen till någon annan som ska hantera instansen kan du göra det nu via Azure Portal ([instruktioner](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) eller CLI ([instruktioner](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).
>
> Du kan också använda portalen eller CLI för att göra om din egen roll tilldelning om det fanns några problem med skript konfigurationen.

### <a name="verify-app-registration"></a>Verifiera registrering av appar

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Kontrol lera först att inställningarna för Azure Digitals-inställningar för digital har kon figurer ATS korrekt i registreringen. Det gör du genom att välja *manifest* från meny raden för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter dessa fält under `requiredResourceAccess` . Värdena ska matcha dem i skärm bilden nedan:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>Andra möjliga steg för din organisation

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Nästa steg

Se hur du ansluter klient programmet till din instans genom att skriva klient appens autentiseringsnyckel:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)
