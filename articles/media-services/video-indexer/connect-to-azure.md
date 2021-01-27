---
title: Skapa ett Video Indexer-konto som är anslutet till Azure
titleSuffix: Azure Media Services
description: Lär dig hur du skapar ett Video Indexer-konto som är anslutet till Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 8e110ba9818b48d66c5f17bb524bada567d808ab
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897164"
---
# <a name="create-a-video-indexer-account"></a>Skapa ett Video Indexer konto

När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med alternativet betald kan du skapa ett Video Indexer-konto som är anslutet till din Azure-prenumeration. Du betalar för minuter indexerat, mer information finns i [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/).

Den här artikeln visar hur du skapar ett Video Indexer-konto som är länkat till en Azure-prenumeration och ett Azure Media Services-konto. Avsnittet innehåller steg för att ansluta till Azure med hjälp av det automatiska (standard) flödet. Det visar också hur du ansluter till Azure manuellt (avancerat).

Om du flyttar från en *utvärdering* till *betald* video Indexer konto kan du välja att kopiera alla videor och modell anpassningar till det nya kontot, enligt beskrivningen i avsnittet [Importera ditt innehåll från utvärderings kontot](#import-your-content-from-the-trial-account) .

Artikeln beskriver också hur [du länkar ett video Indexer konto till Azure Government](#video-indexer-in-azure-government).

## <a name="prerequisites-for-connecting-to-azure"></a>Krav för att ansluta till Azure

* En Azure-prenumeration.

    Om du ännu inte har en Azure-prenumeration kan du registrera dig för en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/free/).
* En Azure Active Directory-domän (Azure AD).

    Om du inte har en Azure AD-domän skapar du den här domänen med din Azure-prenumeration. Mer information finns i [Hantera anpassade domän namn i Azure AD](../../active-directory/enterprise-users/domains-manage.md)
* En användare i din Azure AD-domän med rollen **program administratör** . Du använder den här medlemmen när du ansluter ditt Video Indexer-konto till Azure.

    Den här användaren bör vara en Azure AD-användare med ett arbets-eller skol konto. Använd inte ett personligt konto, till exempel outlook.com, live.com eller hotmail.com.

    ![alla Azure AD-användare](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Ytterligare krav för automatisk flöde

* En användare och medlem i din Azure AD-domän.

    Du använder den här medlemmen när du ansluter ditt Video Indexer-konto till Azure.

    Den här användaren bör vara medlem i din Azure-prenumeration med antingen en **ägar** roll eller både rollen **deltagare** och **administratör för användar åtkomst** . En användare kan läggas till två gånger, med två roller. En gång med deltagare och en gång med administratör för användar åtkomst. Mer information finns i [Visa en användares åtkomst till Azure-resurser](../../role-based-access-control/check-access.md).

    ![åtkomst kontroll](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Ytterligare krav för manuellt flöde

* Registrera EventGrid-resurs leverantören med hjälp av Azure Portal.

    I [Azure Portal](https://portal.azure.com/)går du till **prenumerationer**-> [prenumeration]->**ResourceProviders**.

    Sök efter **Microsoft. Media** och **Microsoft. EventGrid**. Om du inte har statusen "registrerad" klickar du på **Registrera**. Det tar några minuter att registrera sig.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Skapa ett nytt konto på Azure 

> [!NOTE]
> Om din Azure-prenumeration använder certifikatbaserad Multi-Factor Authentication är det viktigt att du utför följande steg på en enhet som har de certifikat som krävs installerade.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
1. Välj knappen **skapa obegränsad konto** :

    ![Skapa nytt Video Indexer konto](./media/create-account/create-unlimited-account.png)
1. När listan prenumerationer visas väljer du den prenumeration som du vill använda.

    ![Ansluta Video Indexer till Azure](./media/create-account/new-account-on-azure-subscription.png)
1. Välj en Azure-region från de platser som stöds: USA, västra 2, Nord Europa eller Asien, östra.
1. Under **Azure Media Services konto** väljer du något av följande alternativ:

    * Om du vill skapa ett nytt Media Services konto väljer du **Skapa ny resurs grupp**. Ange ett namn för resurs gruppen.

        Azure kommer att skapa ditt nya konto i din prenumeration, inklusive ett nytt Azure Storage konto.  
    * Om du vill använda ett befintligt Media Services-konto väljer du **Använd befintlig resurs**. Välj ditt konto i listan konton.

        Ditt Media Services-konto måste ha samma region som ditt Video Indexer-konto.

        > [!NOTE]
        > För att minimera Indexeringens varaktighet och låg genom strömning rekommenderar vi starkt att du justerar typen och antalet [reserverade enheter](../previous/media-services-scale-media-processing-overview.md ) i ditt Media Services-konto till **10 S3-reserverade enheter**. Se [använda Portal för att ändra reserverade enheter](../previous/media-services-portal-scale-media-processing.md). De reserverade enheterna debiteras ditt konto, Visa [pris information](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * Om du vill konfigurera anslutningen manuellt väljer du länken **Växla till manuell konfiguration** .

        Detaljerad information finns i avsnittet [ansluta till Azure manuellt](#connect-to-azure-manually-advanced-option) (avancerat alternativ) som följer.
1. När du är klar väljer du **Skapa**. Den här åtgärden kan ta upp till några minuter.

    När du har anslutit till Azure visas ditt nya Video Indexer-konto i konto listan:

    ![nytt konto](./media/create-account/new-account.png)
1. Kontrol lera att slut punkten för direkt uppspelning av Media Services kontot körs innan du kan spela upp videor i den Video Indexer webbappen (tryck på Start om det är i stoppat tillstånd).

> [!TIP]
> För att ge ett eget visnings värdnamn för ditt konto går du till **Inställningar**.

## <a name="connect-to-azure-manually-advanced-option"></a>Anslut till Azure manuellt (avancerat alternativ)

Om anslutningen till Azure misslyckades kan du försöka felsöka problemet genom att ansluta manuellt.

> [!NOTE]
> Vi rekommenderar starkt att ha följande tre konton i samma region: det Video Indexer konto som du ansluter till Media Services-kontot, samt det Azure Storage-konto som är anslutet till samma Media Services-konto.

### <a name="create-and-configure-a-media-services-account"></a>Skapa och konfigurera ett Media Services konto

1. Använd [Azure](https://portal.azure.com/) Portal för att skapa ett Azure Media Services konto, enligt beskrivningen i [skapa ett konto](../previous/media-services-portal-create-account.md).

     Kontrol lera att Media Servicess kontot har skapats med de klassiska API: erna. 
 
    ![Media Services klassiskt API](./media/create-account/enable-classic-api.png)


    När du skapar ett lagrings konto för ditt Media Services-konto väljer du **StorageV2** för konto Natura och **Geo-redundant** (GRS) för replikeringsalternativ.

    ![Nytt AMS-konto](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Se till att skriva ned Media Services resurs-och konto namn. Du behöver dem för stegen i nästa avsnitt.
1. Justera typen och antalet [reserverade enheter](../previous/media-services-scale-media-processing-overview.md ) till **10 S3-reserverade enheter** i det Media Services konto som du skapade. Se [använda Portal för att ändra reserverade enheter](../previous/media-services-portal-scale-media-processing.md).

    De reserverade enheterna debiteras ditt konto, Visa [pris information](https://azure.microsoft.com/pricing/details/media-services/#analytics). s
1. Innan du kan spela upp videor i Video Indexer-webbappen måste du starta standard **slut punkten för direkt uppspelning** av det nya Media Services-kontot.

    I det nya Media Services-kontot väljer du **slut punkter för direkt uppspelning**. Välj sedan slut punkten för direkt uppspelning och tryck på Starta.

    ![Slutpunkter för direktuppspelning](./media/create-account/create-ams-account-se.png)
4. För att Video Indexer ska kunna autentiseras med Media Services API måste en AD-App skapas. Följande steg vägleder dig genom processen för Azure AD-autentisering som beskrivs i [komma igång med Azure AD-autentisering med hjälp av Azure Portal](../previous/media-services-portal-get-started-with-aad.md):

    1. Välj **API-åtkomst** i det nya Media Services kontot.
    2. Välj [autentiseringsmetod för tjänstens huvud namn](../previous/media-services-portal-get-started-with-aad.md).
    3. Hämta klient-ID och klient hemlighet

        När du har valt **Inställningar** -> **nycklar**, Lägg till **Beskrivning**, tryck på **Spara** och nyckel värdet fylls i.

        Om nyckeln upphör att gälla måste konto ägaren kontakta Video Indexer support för att förnya nyckeln.

        > [!NOTE]
        > Se till att skriva ned nyckelvärdet och program-ID: t. Du behöver den för stegen i nästa avsnitt.

### <a name="connect-manually"></a>Anslut manuellt

I dialog rutan **skapa ett nytt konto i en Azure-prenumeration** på sidan [video Indexer](https://www.videoindexer.ai/) väljer du länken **Växla till manuell konfiguration** .

Ange följande information i dialog rutan:

|Inställning|Beskrivning|
|---|---|
|Video Indexer konto region|Namnet på Video Indexer konto region. För bättre prestanda och lägre kostnader rekommenderar vi starkt att du anger namnet på den region där Azure Media Services resursen och Azure Storage kontot finns. |
|Azure AD-klientorganisation|Namnet på Azure AD-klienten, till exempel "contoso.onmicrosoft.com". Klient informationen kan hämtas från Azure Portal. Placera markören över namnet på den inloggade användaren i det övre högra hörnet. Hitta namnet till höger om **domänen**.|
|Prenumerations-ID:t|Azure-prenumerationen som den här anslutningen ska skapas under. Prenumerations-ID kan hämtas från Azure Portal. Välj **alla tjänster** i den vänstra panelen och Sök efter "prenumerationer". Välj **prenumerationer** och välj önskat ID i listan med dina prenumerationer.|
|Namn på Azure Media Services resurs grupp|Namnet på resurs gruppen där du skapade Media Services-kontot.|
|Medie tjänst resurs namn|Namnet på det Azure Media Services konto som du skapade i föregående avsnitt.|
|Program-ID|Azure AD-programmets ID (med behörigheter för det angivna Media Services kontot) som du skapade i föregående avsnitt.|
|Program nyckel|Den Azure AD-programnyckel som du skapade i föregående avsnitt. |

### <a name="import-your-content-from-the-trial-account"></a>Importera ditt innehåll från *utvärderings* kontot

När du skapar ett nytt konto har du ett alternativ för att importera innehållet från *utvärderings* kontot till det nya kontot. Om du markerar alternativet *Importera* i dialog rutan **skapa ett nytt konto i en Azure-prenumeration** kopieras alla anpassningar av medie-och innehålls modeller från *utvärderings* kontot till det nya kontot.

Möjligheten att importera innehållet är giltig för både automatiserade och manuella metoder som beskrivs ovan.

> [!NOTE]
> Innehållet kan bara importeras en gång från varje konto.
>
> *Utvärderings* kontot är inte availagle på Azure Government molnet.

## <a name="azure-media-services-considerations"></a>Azure Media Services överväganden

Följande Azure Media Services relaterade överväganden gäller:

* Om du planerar att ansluta till ett befintligt Media Services konto kontrollerar du att Media Servicess kontot har skapats med de klassiska API: erna. 
 
    ![Media Services klassiskt API](./media/create-account/enable-classic-api.png)
* Om du ansluter till ett befintligt Media Services-konto kan Video Indexer inte ändra konfigurationen för den befintliga enheten för **reserverade enheter** .

   Du kan behöva justera typ och antal enheternas reserverade enheter enligt den planerade belastningen. Tänk på att om belastningen är hög och du inte har tillräckligt med enheter eller hastighet kan video bearbetningen leda till timeout-problem.
* Om du ansluter till ett nytt Media Services konto startar Video Indexer automatiskt standard **slut punkten för direkt uppspelning** i den:

    ![Slut punkt för Media Services strömning](./media/create-account/ams-streaming-endpoint.png)

    Slut punkter för direkt uppspelning har en betydande start tid. Det kan därför ta flera minuter från det att du anslöt ditt konto till Azure tills dina videor kan strömmas och bevakas i Video Indexer webbappen.
* Om du ansluter till ett befintligt Media Services konto kan Video Indexer inte ändra standard konfigurationen för strömnings slut punkten. Om det inte finns någon **slut punkt för direkt uppspelning** kan du inte titta på videor från det här Media Services kontot eller i video Indexer.
* Om du ansluter automatiskt, anger Video Indexer de medie **reserverade enheterna** till 10 S3-enheter:

    ![Media Services reserverade enheter](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>Automatisera skapandet av det Video Indexer kontot

Att automatisera skapandet av kontot är en process i två steg:
 
1. Använd Azure Resource Manager för att skapa ett Azure Media Services konto och Azure AD-program.

    Se ett exempel på mallen för att [skapa Media Services-konton](https://github.com/Azure-Samples/media-services-v3-arm-templates).
1. Anropa [create-Account med Media Services-och Azure AD-programmet](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account).

## <a name="video-indexer-in-azure-government"></a>Video Indexer i Azure Government

### <a name="prerequisites-for-connecting-to-azure-government"></a>Krav för att ansluta till Azure Government

-   En Azure-prenumeration i [Azure Government](https://docs.microsoft.com/azure/azure-government/).
- Ett Azure AD-konto i Azure Government.
- Alla före krav på behörigheter och resurser enligt beskrivningen ovan i [krav för att ansluta till Azure](#prerequisites-for-connecting-to-azure).

### <a name="create-new-account-via-the-azure-government-portal"></a>Skapa ett nytt konto via Azure Government portalen

> [!NOTE]
> Azure Government molnet innehåller ingen *utvärderings* upplevelse av video Indexer.

Så här skapar du ett betalt konto via Video Indexer-portalen:

1. Gå till https://videoindexer.ai.azure.us 
1. Logga in med ditt Azure Government Azure AD-konto.
1.  Om du inte har några Video Indexer konton i Azure Government att du är ägare eller deltagare i får du en tom upplevelse som du kan använda för att börja skapa ditt konto. 

    Resten av flödet är så som beskrivs i ovan. endast de regioner som du väljer från är myndigheter där video Indexer är tillgängliga 

    Om du redan är deltagare eller administratör för ett befintligt konto i en eller flera Video Indexer i Azure Government, kommer du att gå till det kontot och därifrån kan du börja följa stegen för att skapa ett ytterligare konto vid behov, enligt beskrivningen ovan.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Skapa ett nytt konto via API: et på Azure Government

Om du vill skapa ett betalt konto i Azure Government följer du instruktionerna i [skapa-betalt-Account](https://api-portal.videoindexer.ai.azure.us/docs/services/Operations/operations/Create-Paid-Account). Den här API-slutpunkten inkluderar endast myndigheter i molnet.

### <a name="limitations-of-video-indexer-on-azure-government"></a>Begränsningar för Video Indexer på Azure Government

*   Ingen manuell innehålls moderator är tillgänglig i myndighets molnet. 

    I det offentliga molnet när innehållet bedöms vara stötande baserat på en innehålls kontroll kan kunden be om en mänsklig att titta på innehållet och eventuellt återställa beslutet.  
*   Inga utvärderings konton. 
* Bing-beskrivning – i gov-molnet kommer vi inte att presentera en beskrivning av kändisar och namngivna entiteter som identifieras. Detta är endast en GRÄNSSNITTs funktion. 

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här självstudien tar du bort resurser som du inte planerar att använda.

### <a name="delete-a-video-indexer-account"></a>Ta bort ett Video Indexer konto

Om du vill ta bort ett Video Indexer konto kan du ta bort kontot från Video Indexer webbplats. Du måste vara ägare om du vill ta bort kontot.

Välj konto-> **Inställningar**  ->  **ta bort det här kontot**. 

Kontot tas bort permanent om 90 dagar.

## <a name="next-steps"></a>Nästa steg

Du kan interagera med ditt utvärderings konto och/eller med dina Video Indexer-konton som är anslutna till Azure genom att följa anvisningarna i: [använda API: er](video-indexer-use-apis.md).

Du bör använda samma Azure AD-användare som du använde när du anslöt till Azure.
