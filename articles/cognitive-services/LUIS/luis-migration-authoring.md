---
title: Migrera till en Azure-resurs redigerings nyckel
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du migrerar Language Understanding (LUIS) som redigerar autentisering från ett e-postkonto till en Azure-resurs.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 086bc17938064571e8759ecda633fb5f87d1060f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616821"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrera till en Azure-resurs redigerings nyckel

> [!IMPORTANT]
>  Från och med december tredje, måste befintliga LUIS-användare slutföra migreringsprocessen för att kunna fortsätta redigera LUIS-program.

Language Understanding (LUIS) som redigerar autentisering har ändrats från ett e-postkonto till en Azure-resurs. Använd den här artikeln för att lära dig hur du migrerar ditt konto, om du inte har migrerat ännu.  


## <a name="what-is-migration"></a>Vad är migrering?

Migrering är en process för att ändra auktorisering av autentisering från ett e-postkonto till en Azure-resurs. Ditt konto kommer att länkas till en Azure-prenumeration och en Azure Authoring-resurs när du har migrerat.

Migreringen måste göras från Luis- [portalen](https://www.luis.ai). Om du skapar redigerings nycklarna med hjälp av LUIS CLI måste du till exempel slutföra migreringsprocessen i LUIS-portalen. Du kan fortfarande ha medförfattare i dina program efter migreringen, men de läggs till på Azure-resurs nivån i stället för på program nivån. Migrering av ditt konto kan inte ångras.

> [!Note]
> * Om du behöver skapa en förutsägelse körnings resurs finns det [en separat process](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) för att skapa den.
> * Se avsnittet [migrerings anteckningar](#migration-notes) nedan om du vill ha information om hur dina program och deltagare kommer att påverkas. 
> * Redigering av LUIS-appen är kostnads fri, som anges av F0-nivån. Läs [mer om pris nivåer](luis-limits.md#key-limits).

## <a name="migration-prerequisites"></a>Krav för migrering

* En giltig Azure-prenumeration. Be din klient administratör att lägga till dig på prenumerationen eller [Registrera dig för en kostnads fri](https://azure.microsoft.com/free/cognitive-services).
* En LUIS Azure Authoring-resurs från LUIS-portalen eller från [Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne). 
    * Att skapa en redigerings resurs från LUIS-portalen är en del av migreringsprocessen som beskrivs i nästa avsnitt.
* Om du är en medarbetare i program, migreras inte program automatiskt. Du uppmanas att exportera dessa appar när du går igenom migrations flödet. Du kan också använda [export-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Du kan importera appen tillbaka till LUIS efter migreringen. Import processen skapar en ny app med ett nytt app-ID som du är ägare till.        
* Om du är ägare till programmet behöver du inte exportera dina appar eftersom de migreras automatiskt. En e-postmall med en lista över alla medarbetare för varje program tillhandahålls, så att de kan meddelas om migreringsprocessen.

## <a name="migration-steps"></a>Migreringsanvisningar

1. När du loggar in på Luis- [portalen](https://www.luis.ai)öppnas ett Azure migration-fönster med stegen för migreringen. Om du avmarkerar det kan du inte fortsätta med att redigera dina LUIS-program och den enda åtgärden som visas är att fortsätta med migreringen.

    > [!div class="mx-imgBorder"]
    > ![Migrerings fönstret – Intro](./media/migrate-authoring-key/notify-azure-migration.png)

2. Om du har medarbetare på någon av dina appar visas en lista över program namn som ägs av dig, tillsammans med redigerings regionen och e-postmeddelandena för varje program. Vi rekommenderar att du skickar dina medarbetare ett e-postmeddelande som meddelar dem om migreringen genom att klicka på knappen **Skicka** symbol till vänster om programmets namn.
En `*` symbol visas bredvid programmets namn om en medarbetare har tilldelats en förutsägelse resurs som är tilldelad till ditt program. Efter migreringen kommer dessa appar fortfarande att ha de här förutsägelse resurserna tilldelade, även om medarbetarna inte har åtkomst till redigerings program. Den här tilldelningen bryts dock om ägaren av förutsägelse resursen [återskapade nycklarna](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) från Azure Portal.  

   > [!div class="mx-imgBorder"]
   > ![Meddela medarbetare](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   För varje medarbetare och app öppnas standard programmet för e-post med ett ljust formaterat e-postmeddelande. Du kan redigera e-postmeddelandet innan du skickar det. E-postmallen innehåller det exakta app-ID och app-namn.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > När du har migrerat ditt konto till Azure kommer dina appar inte längre att vara tillgängliga för medarbetare.

3. Om du är en medarbetare i alla appar visas en lista över program namn som delas med dig tillsammans med redigerings regionen och e-postmeddelanden för varje program. Vi rekommenderar att du exporterar en kopia av apparna genom att klicka på knappen Exportera till vänster om programmets namn. Du kan importera dessa appar tillbaka när du har migrerat, eftersom de inte automatiskt migreras med dig.
En `*` symbol visas bredvid programmets namn om du har en förutsägelse resurs som är tilldelad ett program. Efter migreringen kommer förutsägelse resursen fortfarande att tilldelas dessa program även om du inte längre kommer att ha åtkomst till att redigera dessa appar. Om du vill dela upp tilldelningen mellan förutsägelse resursen och programmet måste du gå till Azure Portal och [Återskapa nycklarna](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Exportera dina program.](./media/migrate-authoring-key/migration-export-apps.png)


4. I fönstret för att migrera regioner uppmanas du att migrera dina program till en Azure-resurs i samma region som de har skapats i. LUIS har tre redigerings regioner [och portaler](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions). I fönstret visas de regioner där dina ägda program har skapats. De områden för migrering som visas kan vara olika beroende på vilken region du använder och vilka appar du har skapat. 

   > [!div class="mx-imgBorder"]
   > ![Migrering mellan flera regioner.](./media/migrate-authoring-key/migration-regional-flow.png)

5. För varje region väljer du att skapa en ny LUIS Authoring-resurs, eller migrera till en befintlig med hjälp av knapparna.

   > [!div class="mx-imgBorder"]
   > ![Välj att skapa eller befintlig redigerings resurs](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Ange följande information:

   * **Klient** organisations namn: klienten som din Azure-prenumeration är associerad med. Som standard är detta inställt på den klient som du använder just nu. Du kan byta innehavare genom att stänga det här fönstret och välja avataren längst upp till höger på skärmen, som innehåller dina initialer. Klicka på **migrera till Azure** för att öppna fönstret igen.
   * **Namn på Azure-prenumeration**: den prenumeration som ska associeras med resursen. Om du har mer än en prenumeration som tillhör din klient väljer du den som du vill använda i list rutan.
   * **Skapar resurs namn**: ett eget namn som du väljer. Den används som en del av URL: en för din redigering och förutsägelse slut punkts frågor. Om du skapar en ny redigerings resurs, Observera att resurs namnet bara får innehålla alfanumeriska tecken, `-` och får inte börja eller sluta med `-` . Om det finns andra symboler i namnet kommer det inte att gå att skapa och migrera resurser.
   * **Namn på Azure-resurs grupp**: ett namn på en anpassad resurs grupp som du väljer i den nedrullningsbara listan. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering. Om du för närvarande inte har en resurs grupp i din prenumeration kommer du inte att kunna skapa en i LUIS-portalen. Gå till [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) för att skapa en och gå sedan till Luis för att fortsätta inloggnings processen.

6. När du har migrerat i alla regioner klickar du på Slutför. Du kommer nu att ha åtkomst till dina program. Du kan fortsätta att redigera och underhålla alla dina program i alla regioner i portalen.

## <a name="migration-notes"></a>Migrations anteckningar

* Innan migreringen kallas _medarbetares medarbetare_ på Luis app-nivå. Efter migreringen används Azure-rollen för _deltagare_ för samma funktion på Azure-resurs nivån.
* Om du har loggat in på fler än en [Luis regional Portal](./luis-reference-regions.md#luis-authoring-regions)blir du ombedd att migrera i flera regioner på samma gång.
* Program migreras automatiskt med dig om du är ägare till programmet. Program kommer inte att migreras med dig om du är en medarbetare i programmet. Medarbetare uppmanas dock att exportera appar som de behöver.
* Program ägare kan inte välja en delmängd av appar som ska migreras och det finns inget sätt för en ägare att veta om medarbetare har migrerats.
* Migreringen flyttar inte automatiskt eller lägger till medarbetare till Azure Authoring-resursen. Appens ägare är den som måste slutföra det här steget efter migreringen. Det här steget kräver [behörighet till Azure Authoring-resursen](./luis-how-to-collaborate.md).
* När deltagare har tilldelats till Azure-resursen måste de migrera innan de kan komma åt program. Annars har de inte åtkomst att redigera programmen.


## <a name="using-apps-after-migration"></a>Använda appar efter migrering

Efter migreringen kommer alla dina LUIS-appar för vilka du är ägaren nu att tilldelas en enda LUIS-Authoring-resurs.
I listan **Mina appar** visas appar som migrerats till den nya redigerings resursen. Innan du får åtkomst till dina appar väljer du **Välj en annan redigerings resurs** för att välja prenumerations-och redigerings resurs för att visa de appar som kan redige ras.

> [!div class="mx-imgBorder"]
> ![Välj prenumeration och redigering av resurs](./media/migrate-authoring-key/select-sub-and-resource.png)


Om du planerar att redigera dina appar program mässigt behöver du redigera nyckel värden. Dessa värden visas genom att klicka på **Hantera** överst på skärmen i Luis-portalen och sedan välja **Azure-resurser**. De är också tillgängliga i Azure Portal på sidan för resursens **nyckel och slut punkter** . Du kan också skapa fler redigerings resurser och tilldela dem från samma sida.

## <a name="adding-contributors-to-authoring-resources"></a>Lägga till deltagare i att skriva resurser

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Lär dig [hur du lägger till deltagare](luis-how-to-collaborate.md) på din redigerings resurs. Deltagare kommer att ha åtkomst till alla program i den resursen.

Du kan lägga till deltagare till redigerings resursen från Azure Portal på sidan **Access Control (IAM)** för resursen. Mer information finns i [lägga till deltagare i din app](luis-how-to-collaborate.md).

> [!Note]
> Om ägaren av LUIS-appen migrerat och lagt till medarbetaren som deltagare på Azure-resursen, kommer medarbetaren fortfarande inte att ha åtkomst till appen om de också migrerar.

## <a name="troubleshooting-the-migration-process"></a>Felsöka migreringsprocessen

Om du inte hittar din Azure-prenumeration i list rutan:
* Se till att du har en giltig Azure-prenumeration som har behörighet att skapa Cognitive Services-resurser. Gå till [Azure Portal](https://ms.portal.azure.com) och kontrol lera status för prenumerationen. Om du inte har ett kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/).
* Se till att du är i rätt klient som är associerad med din giltiga prenumeration. Du kan byta innehavare genom att välja avataren i det övre högra hörnet på skärmen, som innehåller dina initialer.

  > [!div class="mx-imgBorder"]
  > ![Sida för att växla kataloger](./media/migrate-authoring-key/switch-directories.png)

Om du har en befintlig redigerings resurs men inte kan hitta den när du väljer alternativet **Använd befintlig redigerings resurs** :
* Resursen skapades förmodligen i en annan region än den som du försöker migrera i.
* Skapa en ny resurs från LUIS-portalen i stället.

Om du väljer **resurs alternativet Skapa ny redigerare** och migreringen Miss lyckas med fel meddelandet "Det gick inte att hämta användarens Azure-information, försök igen senare":
* Din prenumeration kan ha 10 eller fler redigerings resurser per region, per prenumeration. I så fall kan du inte skapa en ny redigerings resurs.
* Migrera genom att välja alternativet **Använd befintlig redigerings resurs** och välja en av de befintliga resurserna under din prenumeration.

## <a name="create-new-support-request"></a>Skapa en ny supportbegäran

Om du har problem med migreringen som inte åtgärdas i fel söknings avsnittet [skapar du ett support avsnitt](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) och anger informationen nedan med följande fält:

   * **Typ av problem**: teknisk
   * **Prenumeration**: Välj en prenumeration i list rutan
   * **Tjänst**: Sök och välj "Cognitive Services"
   * **Resurs**: Välj en Luis-resurs om det finns en befintlig. Annars väljer du allmän fråga.

## <a name="next-steps"></a>Nästa steg

* Granska [begrepp om redigerings-och körnings nycklar](luis-how-to-azure-subscription.md)
* Granska hur du [tilldelar nycklar](luis-how-to-azure-subscription.md) och [lägger till deltagare](luis-how-to-collaborate.md)
