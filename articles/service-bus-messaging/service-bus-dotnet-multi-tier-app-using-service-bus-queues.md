---
title: .NET-flernivåprogram med hjälp av Azure Service Bus | Microsoft Docs
description: En .NET-självstudiekurs som hjälper dig att utveckla en flernivåapp i Azure som använder Service Bus-köer för att kommunicera mellan nivåerna.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 40529df5195a29fbf2ff4887311932c2ffbf471d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96029903"
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>.NET-flernivåapp med hjälp av Azure Service Bus-köer

Att utveckla för Microsoft Azure är enkelt tack vare Visual Studio och det kostnadsfria utvecklingsverktyget Azure SDK för .NET. Den här självstudiekursen vägleder dig igenom stegen för att skapa en app som använder flera Azure-resurser som körs i din lokala miljö.

Du kommer att få lära dig följande:

* Hur du förbereder datorn för Azure-utveckling med en enda nedladdning och installation.
* Hur du använder Visual Studio för att utveckla för Azure.
* Hur du skapar en flernivåapp i Azure genom att använda webb- och arbetsroller.
* Hur du kan kommunicera mellan nivåer med hjälp av Service Bus-köer.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

I den här självstudiekursen kommer du att skapa och köra flernivåappen i en molntjänst för Azure. Klientdelen är en ASP.NET MVC-webbroll och serverdelen en arbetsroll som använder en Service Bus-kö. Du kan skapa samma flernivåapp med klientdelen som ett webbprojekt som distribueras till en Azure-webbplats i stället för till en molntjänst. Du kan också prova att gå igenom självstudiekursen [.NET-hybridapp lokalt/i molnet](../azure-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md).

Följande skärm bild visar det färdiga programmet.

![Skärm bild av programmets överförings sida.][0]

## <a name="scenario-overview-inter-role-communication"></a>Scenarioöversikt: kommunikation mellan roller
Om du vill skicka in en order för bearbetning måste klientdelens UI-komponent, som kör webbrollen, interagera med den mellannivålogik som kör arbetsrollen. I det här exemplet används en meddelandetjänst via Service Bus för kommunikation mellan nivåerna.

Genom att använda en meddelandetjänst mellan webben och mellannivåerna frikopplas de båda komponenterna. Till skillnad från direkt dataöverföring (d.v.s. TCP eller HTTP), ansluter webbnivån inte direkt till mellannivån. Istället skickar den arbetsenheter, som meddelanden, till Service Bus, som lagrar dem på ett tillförlitligt sätt tills mellannivån är klar att använda och bearbeta dem.

Service Bus innehåller två entiteter för att stödja den asynkrona meddelandetjänsten: köer och ämnen. När man använder köer förbrukas varje meddelande som skickas till kön av en enda mottagare. Ämnena stödjer det mönster för publicera/prenumerera där varje publicerat meddelande görs tillgängligt för en prenumeration som har registrerats med ämnet. Varje prenumeration underhåller logiskt nog sin egen meddelandekö. Prenumerationer kan också konfigureras med filterregler som begränsar den uppsättning av meddelanden som skickas vidare till prenumerationskön till dem som matchar filtret. I följande exempel används Service Bus-köer.

![Diagram över kommunikationen mellan webb rollen, Service Bus och arbets rollen.][1]

Denna kommunikationsmekanism har flera fördelar jämfört med funktioner för direkta meddelanden:

* **Tidsbestämd frikoppling.** Med mönstret för asynkrona meddelanden behöver producenter och konsumenter inte vara online samtidigt. Service Bus lagrar meddelanden på ett säkert sätt tills den konsumerande parten är redo att ta emot dem. Detta gör att komponenterna i den distribuerade appen kan frikopplas, antingen frivilligt, till exempel för underhåll, eller på grund av en komponentkrasch, utan att detta påverkar hela systemet. Dessutom behöver den konsumerande appen endast kopplas upp och vara online under vissa tider på dagen.
* **Belastningsutjämning.** I många program varierar systembelastningen beroende på tidpunkten, medan den bearbetningstid som krävs för varje arbetsenhet vanligtvis är konstant. Medlingen mellan meddelandeproducenter och -konsumenter med hjälp av en kö innebär att den konsumerande appen (arbetaren) endast måste konfigureras för att kunna hantera en genomsnittlig belastning i stället för mycket hög belastning vid vissa tider. Köns djup växer och dras samman allt eftersom den inkommande belastningen varierar. Detta sparar direkt pengar eftersom den mängd infrastruktur som krävs för att underhålla programbelastningen blir mindre.
* **Belastnings utjämning.** Allt eftersom belastningen ökar kan fler arbetsprocesser läggas till för att läsa från kön. Varje meddelande bearbetas bara av en av arbetsprocesserna. Dessutom gör den här pull-baserade belastningsbalanseringen att du får en optimal användning av arbetsdatorerna. Detta gäller även om arbetsdatorerna har olika processorkraft: de kommer att hämta meddelanden med den hastighet som var och en av dem klarar av. Det här mönstret kallas ofta för ett *konkurrerande konsument*-mönster.
  
  ![Diagram över kommunikationen mellan webb rollen, Service Bus och två arbets roller.][2]

I följande avsnitt pratar vi om den kod som implementerar denna arkitektur.

## <a name="create-a-namespace"></a>Skapa ett namnområde

Det första steget är att skapa ett *namn område* och hämta en nyckel för [signatur för delad åtkomst (SAS)](service-bus-sas.md) för denna namnrymd. Ett namnområde ger en appgräns för varje app som exponeras via Service Bus. SAS-nyckeln genereras av systemet när ett namnområde har skapats. Kombinationen av namnet på namnområdet och SAS-nyckeln tillhandahåller autentiseringsuppgifterna för Service Bus som används för att tillåta åtkomst till ett program.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Skapa en webbroll

I det här avsnittet ska du skapa klientdelen för din app. Först skapar du de sidor som din app visar.
Efter det lägger du till kod som skickar objekt till en Service Bus-kö och visar statusinformation om denna kö.

### <a name="create-the-project"></a>Skapa projektet

1. Du startar Visual Studio med administratörsbehörighet genom att högerklicka på programikonen för **Visual Studio** och sedan klicka på **Kör som administratör**. Azure Compute-emulatorn som beskrivs längre fram i den här artikeln kräver att Visual Studio startas med administratörs behörighet.
   
   I Visual Studio klickar du på **Nytt** på **Arkiv**-menyn och sedan på **Projekt**.
2. Från **Installerade mallar**, under **Visual C#**, klickar du på **Moln** och sedan på **Azure Cloud Service**. Ge projektet följande namn: **MultiTierApp**. Klicka sedan på **OK**.
   
   ![Skärm bild av dialog rutan nytt projekt med moln valt och Azure Cloud Service Visual C# markerat och beskrivs i rött.][9]
3. Dubbelklicka på **ASP.NET Web Role** från fönstret **Roller**.
   
   ![Skärm bild av dialog rutan ny Microsoft Azure moln tjänst med ASP.NET-webbroll vald och WebRole1 också vald.][10]
4. Hovra över **WebRole1** under **Azure Cloud Service Solution** och klicka på pennikonen. Byt sedan namn på webbrollen till **FrontendWebRole**. Klicka sedan på **OK**. (Kontrollera att du anger ”Frontend” med ett litet ”e”, det vill säga, inte ”FrontEnd”).
   
   ![Skärm bild av dialog rutan ny Microsoft Azure moln tjänst med lösningen ändrat namn till FrontendWebRole.][11]
5. Från dialogrutan **Nytt ASP.NET-projekt** klickar du på **MVC** i listan **Välj en mall**.
   
   ![Screenshotof dialog rutan nytt ASP.NET-projekt med MVC markerad och beskrivs i rött och alternativet ändra autentisering beskrivs i rött.][12]
6. Medan du fortfarande är kvar i dialogrutan **Nytt ASP.NET-projekt**, klickar du på knappen **Ändra autentisering**. I dialogrutan **Ändra autentisering** kontrollerar du att **Ingen autentisering** har markerats och klickar sedan på **OK**. För den här självstudiekursen distribuerar du en app som inte kräver någon användarinloggning.
   
    ![Skärm bild av dialog rutan Ändra autentisering med alternativet Ingen autentisering markerat och anges i rött.][16]
7. Väl tillbaka i dialogrutan **Nytt ASP.NET-projekt** klickar du på **OK** för att skapa projektet.
8. I projektet **FrontendWebRole** i **Solution Explorer** högerklickar du på **Referenser** och sedan klickar du på **Hantera NuGet-paket**.
9. Klicka på fliken **Bläddra** och sök sedan efter **WindowsAzure.ServiceBus**. Välj paketet **WindowsAzure.ServiceBus** genom att klicka på **Installera** och godkänn användarvillkoren.
   
   ![Skärm bild av dialog rutan hantera NuGet-paket med WindowsAzure. Service Bus markerat och alternativet Installera som beskrivs i rött.][13]
   
   Observera att de obligatoriska klientsammansättningarna nu refereras och vissa nya kodfiler har lagts till.
10. I **Solution Explorer** högerklickar du på **Modeller**. Klicka sedan på **Lägg till** och på **Klass**. I rutan **Namn** anger du namnet **OnlineOrder.cs**. Klicka sedan på **Lägg till**.

### <a name="write-the-code-for-your-web-role"></a>Skriva koden för webbrollen
I detta avsnitt ska du skapa de olika sidor som din app visar.

1. Ersätt den befintliga definitionen för namnområdet med följande kod i filen OnlineOrder.cs i Visual Studio:
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. I **Solution Explorer** dubbelklickar du på **Controllers\HomeController.cs**. Lägg till följande **using**-uttryck högst upp i filen för att inkludera namnområdena för den modell som du precis har skapat, samt Service Bus.
   
   ```csharp
   using FrontendWebRole.Models;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   ```
3. Ersätt även den befintliga definitionen för namnområdet med följande kod i filen HomeController.cs i Visual Studio. Den här koden innehåller metoder för att hantera överföring av objekt till kön.
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. I menyn **Skapa** klickar du på **Skapa lösning** för att kontrollera att det arbete du utfört hittills är korrekt.
5. Nu ska du skapa vyn för den `Submit()`-metod som du skapade tidigare. Högerklicka inne i `Submit()`-metoden (överlagringen av `Submit()` som inte tar några parametrar) och välj sedan **Lägg till vy**.
   
   ![Skärm bild av koden med fokus på sändnings metoden och en nedrullningsbar listruta med alternativet Lägg till vy markerat.][14]
6. En dialogruta för att skapa vyn visas. Välj **Skapa** i listan **Mall**. Välj klassen **OnlineOrder** i listan **Modellklass**.
   
   ![En skärm bild av dialog rutan Lägg till vy med list rutorna mall och modell klass som beskrivs i rött.][15]
7. Klicka på **Lägg till**.
8. Nu ska du ändra visningsnamnet för din app. Dubbelklicka på filen **Views\Shared\\_Layout.cshtml** i **Solution Explorer** för att öppna den i Visual Studio-redigeraren.
9. Ersätt alla förekomster av **My ASP.NET Application** med **Northwind Traders Products**.
10. Ta bort länkarna för **Start**, **Om** och **Kontakt**. Ta bort den markerade koden:
    
    ![Skärm bild av koden med tre rader av H T M L-åtgärd länk kod markerad.][28]
11. Slutligen ändrar du överföringssidan för att inkludera information om kön. Dubbelklicka på filen **Views\Home\Submit.cshtml** i **Solution Explorer** för att öppna den i Visual Studio-redigeraren. Lägg till följande rad efter `<h2>Submit</h2>`. Just nu är `ViewBag.MessageCount` tom. Du kommer att fylla i denna senare.
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. Du har nu implementerat ditt användargränssnitt (UI). Du kan trycka på **F5** för att köra programmet och bekräfta att det ser ut som du förväntar dig att det ska göra.
    
    ![Skärm bild av programmets överförings sida.][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Skriva koden för att skicka objekt till en Service Bus-kö
Nu ska du lägga till kod för att skicka objekt till en kö. Först måste du skapa en klass som innehåller anslutningsinformation för Service Bus-kön. Sedan initierar du anslutningen från Global.aspx.cs. Slutligen uppdaterar du den överföringskod som du skapade tidigare i HomeController.cs för att den faktiskt ska skicka objekt till en Service Bus-kö.

1. Högerklicka på **FrontendWebRole** (högerklicka på projektet, inte på rollen) i **Solution Explorer**. Klicka på **Lägg till** och sedan på **Klass**.
2. Ge klassen namnet **QueueConnector.cs**. Klicka på **Lägg till** för att skapa klassen.
3. Lägg nu till kod som innehåller anslutningsinformationen och initierar anslutningen till en Service Bus-kö. Ersätt hela innehållet i QueueConnector.cs med följande kod och ange värden för `your Service Bus namespace` (namnet på ditt namnområde) och `yourKey`, som är den **primärnyckel** som du tidigare hämtade från Azure Portal.
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Web;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   
   namespace FrontendWebRole
   {
       public static class QueueConnector
       {
           // Thread-safe. Recommended that you cache rather than recreating it
           // on every request.
           public static QueueClient OrdersQueueClient;
   
           // Obtain these values from the portal.
           public const string Namespace = "your Service Bus namespace";
   
           // The name of your queue.
           public const string QueueName = "OrdersQueue";
   
           public static NamespaceManager CreateNamespaceManager()
           {
               // Create the namespace manager which gives you access to
               // management operations.
               var uri = ServiceBusEnvironment.CreateServiceUri(
                   "sb", Namespace, String.Empty);
               var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                   "RootManageSharedAccessKey", "yourKey");
               return new NamespaceManager(uri, tP);
           }
   
           public static void Initialize()
           {
               // Using Http to be friendly with outbound firewalls.
               ServiceBusEnvironment.SystemConnectivity.Mode =
                   ConnectivityMode.Http;
   
               // Create the namespace manager which gives you access to
               // management operations.
               var namespaceManager = CreateNamespaceManager();
   
               // Create the queue if it does not exist already.
               if (!namespaceManager.QueueExists(QueueName))
               {
                   namespaceManager.CreateQueue(QueueName);
               }
   
               // Get a client to the queue.
               var messagingFactory = MessagingFactory.Create(
                   namespaceManager.Address,
                   namespaceManager.Settings.TokenProvider);
               OrdersQueueClient = messagingFactory.CreateQueueClient(
                   "OrdersQueue");
           }
       }
   }
   ```
4. Se nu till att din **initiera**-metod anropas. Dubbelklicka på **Global.asax\Global.asax.cs** i **Solution Explorer**.
5. Lägg till följande kodrad i slutet av metoden **Application_Start**.
   
   ```csharp
   FrontendWebRole.QueueConnector.Initialize();
   ```
6. Slutligen uppdaterar du den webbkod som du skapade tidigare, för att på så sätt skicka objekt till kön. I **Solution Explorer** dubbelklickar du på **Controllers\HomeController.cs**.
7. Uppdatera metoden `Submit()` (överlagring som inte tar några parametrar) enligt följande anvisningar för att hämta meddelanderäknaren för kön.
   
   ```csharp
   public ActionResult Submit()
   {
       // Get a NamespaceManager which allows you to perform management and
       // diagnostic operations on your Service Bus queues.
       var namespaceManager = QueueConnector.CreateNamespaceManager();
   
       // Get the queue, and obtain the message count.
       var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
       ViewBag.MessageCount = queue.MessageCount;
   
       return View();
   }
   ```
8. Uppdatera metoden `Submit(OnlineOrder order)` (överlagring som inte tar några parametrar) enligt följande anvisningar för att skicka orderinformation till kön.
   
   ```csharp
   public ActionResult Submit(OnlineOrder order)
   {
       if (ModelState.IsValid)
       {
           // Create a message from the order.
           var message = new BrokeredMessage(order);
   
           // Submit the order.
           QueueConnector.OrdersQueueClient.Send(message);
           return RedirectToAction("Submit");
       }
       else
       {
           return View(order); 
       }
   }
   ```
9. Du kan nu köra appen igen. Varje gång du skickar en order, ökar antalet meddelanden.
   
   ![Skärm bild av programmets sändnings sida med antalet meddelanden som ökas till 1.][18]

## <a name="create-the-worker-role"></a>Skapa arbetsrollen
Du ska nu skapa den arbetsroll som behandlar orderöverföringen. I det här exemplet använder vi den projektmall för Visual Studio som heter **Arbetsroll med Service Bus-kö**. Du har redan fått de autentiseringsuppgifter som krävs från portalen.

1. Kontrollera att du har anslutit Visual Studio till ditt Azure-konto.
2. Högerklicka på **Roller** i Visual Studio, i **Solution Explorer**, under projektet **MultiTierApp**.
3. Klicka på **Lägg till** och sedan på **Nytt arbetsrollprojekt**. Dialogrutan **Lägg till nytt rollprojekt** visas.
   
   ![Skärm bild av fönstret lösning Explorer med det nya arbets roll projekt alternativet och Lägg till alternativet markerat.][26]
4. Klicka på **Arbetsroll med Service Bus-kö** i dialogrutan **Lägg till nytt rollprojekt**.
   
   ![Skärm bild av dialog rutan nytt roll projekt för AD med arbets rollen med Service Bus Queue-alternativet markerat och visas i rött.][23]
5. Ge projektet namnet **OrderProcessingRole** i rutan **Namn**. Klicka sedan på **Lägg till**.
6. Kopiera den anslutningssträng som du fick i steg 9 av avsnittet ”Skapa ett namnområde för Service Bus” till Urklipp.
7. I **Solution Explorer** högerklickar du på **OrderProcessingRole** som du skapade i steg 5 (se till att du högerklickar på **OrderProcessingRole** under **Roller** och inte på klassen). Klicka sedan på **Egenskaper**.
8. På fliken **Inställningar** i dialogrutan **Egenskaper** klickar du inuti rutan **Värde** för **Microsoft.ServiceBus.ConnectionString** och sedan klistrar du in slutpunktsvärdet som du kopierade i steg 6.
   
   ![Skärm bild av dialog rutan egenskaper med fliken Inställningar markerad och tabell raden Microsoft. Service Bus. ConnectionString som anges i rött.][25]
9. Skapa en **OnlineOrder**-klass för att representera ordrarna allt eftersom du behandlar dem från kön. Du kan återanvända en klass som du redan har skapat. Högerklicka på klassen **OrderProcessingRole** (högerklicka på klassikonen, inte på rollen) i **Solution Explorer**. Klicka på **Lägg till** och sedan på **Befintligt objekt**.
10. Bläddra till undermappen för **FrontendWebRole\Models** och dubbelklicka sedan på **OnlineOrder.cs** för att lägga till den i projektet.
11. I **WorkerRole.cs** ändrar du värdet för variabeln **QueueName** från `"ProcessingQueue"` till `"OrdersQueue"`, som visas i följande kod.
    
    ```csharp
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```
12. Lägg till följande using-uttryck högst upp i filen WorkerRole.cs.
    
    ```csharp
    using FrontendWebRole.Models;
    ```
13. I funktionen `Run()`, inuti anropet `OnMessage()`, ersätter du innehållet i satsen `try` med följande kod.
    
    ```csharp
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```
14. Du har nu gjort klart appen! Du kan testa den kompletta appen genom att högerklicka på MultiTierApp-projektet i Solution Explorer och välja **Ställ in som startprojekt**. Slutligen trycker du på F5. Observera att antalet meddelanden inte ökar. Detta eftersom arbetsrollen behandlar objekt från kön och sedan markeras dessa som slutförda. Du kan se spårad utdata från arbetsrollen genom att titta i användargränssnittet för Azure Compute Emulator. Du kan göra detta genom att högerklicka på emulatorikonen i aktivitetsfältets meddelandefält och välja **Visa Compute Emulator UI**.
    
    ![Skärm bild av vad som visas när du klickar på emulator-ikonen. Visa användar gränssnittet för beräknings-emulatorn finns i listan med alternativ.][19]
    
    ![Skärm bild av dialog rutan Microsoft Azure Compute mula (Express).][20]

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om Service Bus kan du använda följande resurser:  

* [Komma igång med Service Bus-köer][sbacomqhowto]
* [Tjänstesida för Service Bus][sbacom]  

Mer information om flernivåscenarier finns i:  

* [.NET-flernivåapp med hjälp av lagringstabeller, köer och blob-objekt][mutitierstorage]  

[0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
[2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
[9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
[10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
[11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
[12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
[13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
[14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
[15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
[16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
[17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png

[19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
[20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
[23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
[25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
[26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
[28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
[mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
