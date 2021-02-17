---
title: Konfigurera en instans och autentisering (skript)
titleSuffix: Azure Digital Twins
description: Se hur du konfigurerar en instans av tjänsten Azure Digitals dubbla tjänster genom att köra ett skript för automatisk distribution
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 5ac41ecc068238cbb20c05b253dc071dfc4a8138
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560778"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Konfigurera en digital Azure-instans och autentisering (skript)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln slutför de här stegen genom att köra ett [ **skript exempel för automatiserad distribution**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) som effektiviserar processen. 
* Om du vill visa de manuella CLI-stegen som skriptet körs genom i bakgrunden, se CLI-versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (CLI)*](how-to-set-up-instance-cli.md).
* Om du vill visa de manuella stegen enligt Azure Portal, se Portal versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Krav: Hämta skriptet

Exempel skriptet skrivs i PowerShell. Den är en del av de [**digitala Azure-samlingarna från slut punkt till slut punkt**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), som du kan ladda ned till datorn genom att gå till exempel länken och välja knappen *Bläddra kod* under rubriken. Detta tar dig till GitHub-lagrings platsen för exemplen, som du kan hämta som en *. ZIP* genom att välja knappen *kod* och *Hämta zip*.

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="Vy över de digitala-och lagrings platsen-exemplen på GitHub. Knappen kod är markerad och skapar en liten dialog ruta där hämtnings ZIP-knappen är markerad." lightbox="media/includes/download-repo-zip.png":::

Då hämtas en *. ZIP* -mapp på din dator som **digital-twins-samples-master.zip**. Navigera till mappen på din dator och packa upp den för att extrahera filerna.

I den zippade mappen finns distributions skriptet på _digitala-och-exempel-master >-skript > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Kör distributionsskriptet

I den här artikeln används ett digitalt kod exempel för Azure för att distribuera en digital Digitals-instans i Azure och den nödvändiga autentiseringen är halv automatiskt. Det kan också användas som utgångs punkt för att skriva dina egna skriptbaserade interaktioner.

Här följer stegen för att köra distributions skriptet i Cloud Shell.
1. Gå till ett [Azure Cloud Shell](https://shell.azure.com/) -fönster i webbläsaren. Logga in med det här kommandot:
    ```azurecli-interactive
    az login
    ```
    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida. Annars öppnar du en webb sida på *https://aka.ms/devicelogin* och anger den auktoriseringskod som visas i din terminal.
 
2. Kontrol lera att Cloud Shell har angetts för att köra PowerShell-versionen i Cloud Shell ikon fältet.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell fönster som visar valet av PowerShell-version":::

1. Välj ikonen "Ladda upp/ladda ned filer" och välj "Ladda upp".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell fönster som visar uppladdnings ikonen":::

    Navigera till _**deploy.ps1**_ -filen på din dator (i _digital-endrives-exempel-Master >-skript > **deploy.ps1**_) och tryck på "öppna". Detta kommer att överföra filen till Cloud Shell så att du kan köra den i fönstret Cloud Shell.

4. Kör skriptet genom `./deploy.ps1` att skicka kommandot i Cloud Shells fönstret. Du kan kopiera kommandot nedan (kom ihåg att klistra in i Cloud Shell, du kan använda **CTRL + SHIFT + v** på Windows och Linux eller **cmd + Shift + v** på MacOS. Du kan också använda snabb menyn.

    ```azurecli-interactive
    ./deploy.ps1
    ```

    Skriptet skapar en Azure Digitals-instans och tilldelar din Azure-användare *Azures digitala data ägar* roll på instansen.

    När skriptet körs genom de automatiserade konfigurations stegen uppmanas du att skicka in följande värden:
    * För instansen: *prenumerations-ID* för din Azure-prenumeration som ska användas
    * För-instansen: en *plats* där du vill distribuera instansen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * För instansen: ett *resurs grupp* namn. Du kan använda en befintlig resurs grupp eller ange ett nytt namn på en som ska skapas.
    * För-instansen: ett *namn* för din Azure Digital-instansen. Om din prenumeration har en annan Azure Digital-instans i den region som redan använder det angivna namnet uppmanas du att välja ett annat namn.

Här är ett utdrag från utmatnings loggen från skriptet:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell fönstret visar logg över indata och utdata genom körningen av distributions skriptet" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Om skriptet har slutförts kommer den slutliga utskriften att stå `Deployment completed successfully` . Annars kan du åtgärda fel meddelandet och köra skriptet igen. De steg som du redan har slutfört kommer att kringgås och börja begära inmatningar vid den punkt där du slutade.

> [!NOTE]
> Skriptet tilldelar för närvarande den nödvändiga hanterings rollen i Azure Digitals-data (*Azure Digitals data ägare*) till samma användare som kör skriptet från Cloud Shell. Om du behöver tilldela rollen till någon annan som ska hantera instansen kan du göra det nu via Azure Portal ([instruktioner](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) eller CLI ([instruktioner](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

>[!NOTE]
>Det finns för närvarande ett **känt problem** med skript konfiguration, där vissa användare (särskilt användare på personliga [Microsoft-konton (MSA: er)](https://account.microsoft.com/account)) kan hitta **roll tilldelningen till _Azure Digitals data ägare_ har inte skapats**.
>
>Du kan kontrol lera roll tilldelningen med avsnittet [*Verifiera tilldelning av användar roll*](#verify-user-role-assignment) senare i den här artikeln och, om det behövs, konfigurera roll tilldelningen manuellt med hjälp av [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) eller [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Mer information om det här problemet finns i [*fel sökning: kända problem i Azure Digitals*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Verifiera lyckade och samla in viktiga värden

Om du vill kontrol lera att dina resurser och behörigheter som skapats av skriptet är skapade kan du titta på dem i [Azure Portal](https://portal.azure.com) med hjälp av anvisningarna nedan. Om du inte kan verifiera att något steg lyckas kan du försöka med steget igen. Du kan utföra stegen individuellt med hjälp av [Azure Portal](how-to-set-up-instance-portal.md) -eller [CLI](how-to-set-up-instance-cli.md) -instruktionerna.

Det här avsnittet visar också hur du hittar viktiga värden från resurserna som har skapats av skriptet som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen. Du bör spara dessa värden på en säker plats eller gå tillbaka till det här avsnittet för att hitta dem senare när du behöver dem. Om andra användare kommer att program mera mot instansen bör du även dela dessa värden med dem.

### <a name="verify-instance"></a>Verifiera instans

För att kontrol lera att din instans har skapats går du till [sidan Azure Digitals dubbla](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) i Azure Portal. Du kan gå till den här sidan själv genom att söka efter *Azure Digital-dubbla* i portalens sökfält.

Den här sidan visar alla dina Azure Digitals dubbla instanser. Leta efter namnet på den nyligen skapade instansen i listan.

Om verifieringen misslyckades kan du försöka skapa en instans med hjälp av [portalen](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) eller [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="collect-instance-values"></a>Samla in instansvärden

Öppna instansens *översikts* sida genom att välja namnet på din instans från [sidan Azure Digitals dubbla](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) . Anteckna *namn*, *resurs grupp* och *värdnamn*. Du kan behöva dessa senare för att identifiera och ansluta till din instans.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markera viktiga värden från instansens översikts sida":::

### <a name="verify-user-role-assignment"></a>Verifiera tilldelning av användar roll

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Kom ihåg att skriptet för närvarande tilldelar den här nödvändiga rollen till samma användare som kör skriptet från Cloud Shell. Om du behöver tilldela rollen till någon annan som ska hantera instansen kan du göra det nu via Azure Portal ([instruktioner](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) eller CLI ([instruktioner](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Om verifieringen misslyckades kan du också göra om din egen roll tilldelning med hjälp av [portalen](how-to-set-up-instance-portal.md#set-up-user-access-permissions) eller [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

## <a name="next-steps"></a>Nästa steg

Testa enskilda REST API-anrop på din instans med hjälp av Azure Digitals flätat CLI-kommandon: 
* [AZ DT-referens](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Anvisningar: använda Azure Digitals flätat CLI*](how-to-use-cli.md)

Du kan också se hur du ansluter ett klient program till din instans med autentiserings kod:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)