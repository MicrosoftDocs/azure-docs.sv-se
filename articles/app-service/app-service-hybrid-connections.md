---
title: Hybridanslutningar
description: Lär dig hur du skapar och använder hybrid anslutningar i Azure App Service för att få åtkomst till resurser i olika nätverk.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 02/05/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1b3fc4a254c1157f2c2336e6360ba7621f31364d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594239"
---
# <a name="azure-app-service-hybrid-connections"></a>Hybridanslutningar med Azure App Service

Hybridanslutningar är både en tjänst i Azure och en funktion i Azure App Service. Som tjänst har den användning och funktioner utöver de som används i App Service. Mer information om Hybridanslutningar och deras användning utanför App Service finns i [Azure Relay hybridanslutningar][HCService].

I App Service kan Hybridanslutningar användas för att få åtkomst till program resurser i ett nätverk som kan göra utgående anrop till Azure via port 443. Hybridanslutningar ger åtkomst från din app till en TCP-slutpunkt och aktiverar inte ett nytt sätt att komma åt din app. Som används i App Service motsvarar varje hybrid anslutning en enskild TCP-värd och port-kombination. Detta gör att dina appar kan komma åt resurser på ett operativ system, förutsatt att det är en TCP-slutpunkt. Hybridanslutningar funktionen känner inte igen eller bryr sig om vad applikations protokollet är, eller vad du får åtkomst till. Det ger bara nätverks åtkomst.  

## <a name="how-it-works"></a>Så här fungerar det ##
Hybridanslutningar kräver att en Relay Agent distribueras där den kan uppnå både den önskade slut punkten och Azure. Relay Agent, Hybridanslutningshanteraren (HCM), anropar Azure Relay över port 443. På webbappens webbplats ansluter App Service-infrastrukturen också till Azure Relay för ditt programs räkning. Via de anslutna anslutningarna kan din app komma åt önskad slut punkt. Anslutningen använder TLS 1,2 för säkerhets-och SAS-nycklar (signatur för delad åtkomst) för autentisering och auktorisering.    

![Diagram över hög nivå flöde för Hybrid anslutning][1]

När din app gör en DNS-begäran som matchar en konfigurerad hybrid anslutnings slut punkt omdirigeras utgående TCP-trafik via hybrid anslutningen.  

> [!NOTE]
> Det innebär att du alltid bör försöka använda ett DNS-namn för din hybrid anslutning. Vissa klient program gör ingen DNS-sökning om slut punkten använder en IP-adress i stället. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service hybrid anslutnings förmåner ###

Det finns ett antal fördelar med Hybridanslutningar-kapaciteten, inklusive:

- Appar kan komma åt lokala system och tjänster på ett säkert sätt.
- Funktionen kräver inte en tillgänglig slut punkt för Internet.
- Det går snabbt och enkelt att konfigurera. Inga gateways krävs
- Varje hybrid anslutning matchar en enda värd: port kombination, vilket är användbart för säkerhet.
- Det kräver normalt inte brand Väggs hål. Anslutningarna är alla utgående via standard webb portar.
- Eftersom funktionen är nätverks nivå, är den oberoende till det språk som används av din app och den teknik som används av slut punkten.
- Den kan användas för att ge åtkomst i flera nätverk från en enda app. 
- Den stöds i GA för Windows-appar och Linux-appar. Det finns inte stöd för Windows container-appar.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Saker som du inte kan göra med Hybridanslutningar ###

Saker som du inte kan göra med Hybridanslutningar inkluderar:

- Montera en enhet.
- Använd UDP.
- Få åtkomst till TCP-baserade tjänster som använder dynamiska portar, till exempel FTP passivt läge eller utökat passivt läge.
- Stöd för LDAP, eftersom det kan kräva UDP.
- Stöd Active Directory, eftersom du inte kan ansluta till en App Service Worker. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Lägga till och skapa Hybridanslutningar i din app ##

Om du vill skapa en hybrid anslutning går du till [Azure Portal][portal] och väljer din app. Välj **nätverk**  >  **Konfigurera hybrid anslutnings slut punkter**. Här kan du se de Hybridanslutningar som har kon figurer ATS för din app.  

![Skärm bild av hybrid anslutnings lista][2]

Om du vill lägga till en ny hybrid anslutning väljer du **[+] Lägg till hybrid anslutning**.  Du ser en lista över de Hybridanslutningar som du redan har skapat. Om du vill lägga till en eller flera av dem i din app väljer du de som du vill använda och väljer sedan **Lägg till vald hybrid anslutning**.  

![Skärm bild av hybrid anslutnings portalen][3]

Om du vill skapa en ny hybrid anslutning väljer du **Skapa ny hybrid anslutning**. Ange: 

- Namn på Hybrid anslutning.
- Slut punkts namn.
- Slut punkts port.
- Service Bus namn område som du vill använda.

![Skärm bild av dialog rutan skapa ny hybrid anslutning][4]

Varje hybrid anslutning är kopplad till ett Service Bus-namnområde och varje Service Bus-namnrymd finns i en Azure-region. Det är viktigt att du försöker använda ett Service Bus-namnområde i samma region som din app, för att undvika en fördröjning i nätverket.

Om du vill ta bort din hybrid anslutning från appen högerklickar du på den och väljer **Koppla från**.  

När du har lagt till en hybrid anslutning i din app kan du se information om den genom att välja den. 

![Skärm bild av information om hybrid anslutningar][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Skapa en hybrid anslutning i Azure Relay portalen ###

Förutom Portal upplevelsen från din app, kan du skapa Hybridanslutningar inifrån Azure Relay portalen. För att en hybrid anslutning ska kunna användas av App Service måste den:

* Kräv klient auktorisering.
* Ha ett metadataobjekt med namnet Endpoint som innehåller en värd: port kombination som värde.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridanslutningar och App Services planer ##

App Service Hybridanslutningar är bara tillgängliga i SKU: er för Basic, standard, Premium och isolerade priser. Det finns gränser som är knutna till pris planen.  

| Pris plan | Antal Hybridanslutningar användbara i planen |
|----|----|
| Grundläggande | 5 per plan |
| Standard | 25 per plan |
| PremiumV2 | 200 per app |
| Isolerad | 200 per app |

App Service plan användar gränssnitt visar hur många Hybridanslutningar som används och av vilka appar.  

![Skärm bild av App Service plan egenskaper][6]

Välj hybrid anslutning för att se information. Du kan se all information som du såg i vyn app. Du kan också se hur många andra appar i samma plan som använder hybrid anslutningen.

Det finns en gräns för antalet hybrid anslutnings slut punkter som kan användas i en App Service plan. Varje hybrid anslutning som används kan dock användas över valfritt antal appar i planen. Till exempel räknas en enskild hybrid anslutning som används i fem separata appar i ett App Service plan som en hybrid anslutning.

### <a name="pricing"></a>Prissättning ###

Förutom att det finns ett App Service plan SKU-krav finns det ytterligare kostnader att använda Hybridanslutningar. Det finns en avgift för varje lyssnare som används av en hybrid anslutning. Lyssnaren är Hybridanslutningshanteraren. Om du har fem Hybridanslutningar stöd för två hybrid anslutnings hanterare, är det 10 lyssnare. Mer information finns i [Service Bus prissättning][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybridanslutningshanteraren ##

Den Hybridanslutningar funktionen kräver en Relay-Agent i nätverket som är värd för Hybrid anslutnings slut punkten. Den Relay-agenten kallas för Hybridanslutningshanteraren (HCM). Om du vill ladda ned HCM från din app i [Azure Portal][portal]väljer du **nätverk**  >  **Konfigurera hybrid anslutnings slut punkter**.  

Verktyget körs på Windows Server 2012 och senare. HCM körs som en tjänst och ansluter utgående till Azure Relay på port 443.  

När du har installerat HCM kan du köra HybridConnectionManagerUi.exe för att använda användar gränssnittet för verktyget. Den här filen finns i installations katalogen för Hybridanslutningshanteraren. I Windows 10 kan du också bara söka efter *Hybridanslutningshanteraren användar gränssnitt* i sökrutan.  

![Skärm bild av Hybridanslutningshanteraren][7]

När du startar HCM-ANVÄNDARGRÄNSSNITTET är det första du ser en tabell med en lista över alla Hybridanslutningar som har kon figurer ATS med den här instansen av HCM. Om du vill göra några ändringar måste du först autentisera med Azure. 

Så här lägger du till en eller flera Hybridanslutningar till din HCM:

1. Starta HCM-ANVÄNDARGRÄNSSNITTET.
2. Välj **Konfigurera en annan hybrid anslutning**.
![Skärm bild av konfigurera nya Hybridanslutningar][8]

1. Logga in med ditt Azure-konto för att få din Hybridanslutningar tillgänglig med dina prenumerationer. HCM fortsätter inte att använda ditt Azure-konto utöver det. 
1. Välj en prenumeration.
1. Välj den Hybridanslutningar som du vill att HCM ska vidarebefordra.
![Skärm bild av Hybridanslutningar][9]

1. Välj **Spara**.

Nu kan du se Hybridanslutningar du har lagt till. Du kan också välja den konfigurerade hybrid anslutningen för att se information.

![Skärm bild av hybrid anslutnings information][10]

HCM kräver följande för att stödja den Hybridanslutningar den är konfigurerad med:

- TCP-åtkomst till Azure via port 443.
- TCP-åtkomst till hybrid anslutnings slut punkten.
- Möjligheten att göra DNS-sökning på slut punkts värden och Service Bus namnrymd.

> [!NOTE]
> Azure Relay är beroende av Web Sockets för anslutning. Den här funktionen är endast tillgänglig på Windows Server 2012 eller senare. Därför stöds inte HCM på något som är tidigare än Windows Server 2012.
>

### <a name="redundancy"></a>Redundans ###

Varje HCM kan stödja flera Hybridanslutningar. Dessutom kan en eventuell hybrid anslutning stödjas av flera HCMs. Standard beteendet är att dirigera trafik över de konfigurerade HCMs för alla angivna slut punkter. Om du vill ha hög tillgänglighet på din Hybridanslutningar från nätverket kan du köra flera HCMs på separata datorer. Algoritmen för belastnings fördelning som används av relä tjänsten för att distribuera trafik till HCMs är slumpmässig tilldelning. 

### <a name="manually-add-a-hybrid-connection"></a>Lägg till en hybrid anslutning manuellt ###

Om du vill att någon utanför din prenumeration ska vara värd för en HCM-instans för en specifik hybrid anslutning, delar du Gateway-anslutningssträngen för Hybrid anslutningen med dem. Du kan se Gateway-anslutningssträngen i egenskaperna för Hybrid anslutningen i [Azure Portal][portal]. Om du vill använda den strängen väljer du **ange manuellt** i HCM och klistrar in anslutnings strängen för gatewayen.

![Lägg till en hybrid anslutning manuellt][11]

### <a name="upgrade"></a>Uppgradera ###

Det finns periodiska uppdateringar av Hybridanslutningshanteraren för att åtgärda problem eller tillhandahålla förbättringar. När uppgraderingar publiceras visas en popup-meny i HCM-ANVÄNDARGRÄNSSNITTET. Om du använder uppgraderingen tillämpas ändringarna och du kan starta om HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Lägga till en hybrid anslutning till appen program mässigt ##

Det finns stöd för Azure CLI för Hybridanslutningar. De kommandon som tillhandahålls körs både i appen och på den App Service plan nivån.  Kommandona på App-nivå är:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

Med App Service plan-kommandon kan du ange vilken nyckel en angiven hybrid anslutning ska använda. Det finns två nycklar inställda på varje hybrid anslutning, en primär och en sekundär. Du kan välja att använda den primära eller sekundära nyckeln med nedanstående kommandon. På så sätt kan du växla nycklar för när du regelbundet vill återskapa nycklar. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Skydda din Hybridanslutningar ##

En befintlig hybrid anslutning kan läggas till i andra App Service Web Apps av alla användare som har tillräcklig behörighet för det underliggande Azure Service Bus reläet. Det innebär att om du måste hindra andra från att återanvända samma hybrid anslutning (till exempel när mål resursen är en tjänst som inte har några ytterligare säkerhets åtgärder på plats för att förhindra obehörig åtkomst) måste du låsa åtkomsten till Azure Service Bus reläet.

Alla som har `Reader` åtkomst till reläet kan _Se_ hybrid anslutningen vid försök att lägga till den i webbappen i Azure Portal, men de kommer inte att kunna _lägga till_ den eftersom de saknar behörighet att hämta anslutnings strängen som används för att upprätta relä anslutningen. För att kunna lägga till hybrid anslutningen måste de ha `listKeys` behörigheten ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ). `Contributor`Rollen eller någon annan roll som inkluderar den här behörigheten för reläet gör att användarna kan använda hybrid anslutningen och lägga till den i sina egna Web Apps.

## <a name="troubleshooting"></a>Felsökning ##

Statusen "ansluten" innebär att minst en HCM har kon figurer ATS med hybrid anslutningen och att den kan komma åt Azure. Om statusen för din hybrid anslutning inte är **ansluten** konfigureras inte din hybrid anslutning på någon HCM som har åtkomst till Azure. När din HCM visar **inte anslutna** finns det några saker att kontrol lera:

* Har värden utgående åtkomst till Azure på port 443? Du kan testa från din HCM-värd med PowerShell-kommandot *test-NetConnection destination-P-port* 
* Är din HCM potentiellt i ett dåligt tillstånd? Försök att starta om den lokala tjänsten Azure Hybridanslutningshanteraren service.

Om din status **är ansluten** men appen inte kan komma åt din slut punkt:

* kontrol lera att du använder ett DNS-namn i din hybrid anslutning. Om du använder en IP-adress kanske inte den begärda DNS-sökningen sker. Om klienten som kör i din webbapp inte gör en DNS-sökning kommer hybrid anslutningen inte att fungera
* kontrol lera att DNS-namnet som används i din hybrid anslutning kan matchas från HCM-värden. Kontrol lera lösning med hjälp av *nslookup EndpointDNSname* där EndpointDNSname är en exakt matchning av vad som används i din hybrid anslutnings definition.
* testa åtkomst från din HCM-värd till din slut punkt med PowerShell *-kommandot test-NetConnection EndpointDNSname-P-port*  om du inte kan nå slut punkten från HCM-värden kontrollerar du brand väggarna mellan de två värdarna, inklusive alla värdbaserade brand väggar på mål värden.

I App Service kan kommando rads verktyget **tcpping** anropas från konsolen Advanced tools (kudu). Det här verktyget kan meddela dig om du har åtkomst till en TCP-slutpunkt, men om du har åtkomst till en hybrid anslutnings slut punkt. När du använder verktyget i-konsolen mot en hybrid anslutnings slut punkt bekräftar du bara att den använder en värd: port kombination.  

Om du har en kommando rads klient för slut punkten kan du testa anslutningen från App-konsolen. Du kan till exempel testa åtkomst till webb server slut punkter med hjälp av sväng.


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
