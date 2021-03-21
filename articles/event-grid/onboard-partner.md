---
title: Publicera som en Azure Event Grid-partner med Azure Portal
description: Använd Azure Portal för att publicera en Azure Event Grid-partner.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98050960"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Publicera som en Azure Event Grid-partner med hjälp av Azure Portal
Den här artikeln beskriver hur SaaS-Providers från tredje part, även kallade händelse utgivare eller partner, registreras på Event Grid att kunna publicera händelser från sina tjänster och hur dessa händelser används av slutanvändarna.

> [!IMPORTANT]
> Om du inte är bekant med partner händelser kan du läsa [Översikt över partner händelser](partner-events-overview.md) för att få en detaljerad introduktion av viktiga begrepp som är viktiga att förstå och följa stegen i den här artikeln.

## <a name="onboarding-process-for-event-publishers-partners"></a>Onboarding-process för händelse utgivare (partner)
I en kortfattat så Jenkins innebär det att att tjänstens händelser ska konsumeras av användare vanligt vis följande process:

1. **Förmedla ditt intresse** av att bli en partner till Event Grid tjänst teamet innan du fortsätter med nästa steg.
1. Skapa en partner ämnes typ genom att skapa en **registrering**. 
1. Skapa ett **namn område**.
1. Skapa en **händelse kanal** och ett **partner ämne** (enskilt steg).
1. Testa funktionen partner händelser från början till slut.

För steg #4 bör du bestämma vilken typ av användar upplevelse som du vill tillhandahålla. Du kan välja mellan följande alternativ:
- Ange din egen lösning, vanligt vis en grafiskt användar gränssnitts upplevelse (GUI) som finns under din domän med hjälp av vår SDK och/eller REST API för att skapa en händelse kanal och dess motsvarande partner ämne. Med det här alternativet kan du be användaren om prenumerationen och resurs gruppen under vilken du skapar ett partner ämne.
- Använd Azure Portal eller CLI för att skapa händelse kanalen och den tillhör ande partner artikeln. Med det här alternativet måste du ha till gång till användarens Azure-prenumeration på ett sätt och en resurs grupp under vilken du skapar ett partner ämne. 

Den här artikeln visar hur du kan publicera som en Azure Event Grid-partner med hjälp av Azure Portal. 

> [!NOTE]
> Att registrera en partner ämnes typ är ett valfritt steg. För att hjälpa dig att avgöra om du ska skapa en partner ämnes typ, se [resurser som hanteras av händelse utgivare](partner-events-overview.md#resources-managed-by-event-publishers).

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Förmedla ditt intresse av att bli en partner
Fyll i [det här formuläret](https://aka.ms/gridpartnerform) och kontakta Event Grid-teamet på [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . Vi kommer att ha en konversation med information om partner evenemangs användnings fall, personer, onboarding-process, funktioner, priser och mycket annat.

## <a name="prerequisites"></a>Förutsättningar
Följ de återstående stegen genom att kontrol lera att du har:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- En Azure- [klient](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="register-a-partner-topic-type-optional"></a>Registrera en typ av partner ämne (valfritt)
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **alla tjänster** i det vänstra navigerings fönstret och skriv **Event Grid partner registreringar** i Sök fältet och markera det. 
1. På sidan **Event Grid partner registreringar** väljer du **+ Lägg till** i verktygsfältet. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Lägg till partner registrerings länk":::
1. På sidan **skapa partner ämnes typ registreringar – grundläggande** anger du följande information: 
    1. I avsnittet **projekt information** följer du de här stegen:
        1. Välj din Azure- **prenumeration**. 
        1. Välj en befintlig Azure- **resurs grupp** eller skapa en ny resurs grupp. 
    1. I avsnittet **registrerings information** följer du dessa steg:
        1. Ange ett namn för registreringen för **registrerings namn**. 
        1. Ange namnet på din organisation för **organisations namn**. 
    1. I avsnittet **partner resurs typ** anger du information om din resurs typ som ska visas på sidan för **partner avsnittet Skapa** : 
        1. För **partner resurs typ namn** anger du namnet på resurs typen. Detta är den typ av partner ämne som kommer att skapas i din Azure-prenumeration. 
        2. I **visnings namn** anger du ett användarvänligt visnings namn för partner ämnet (resurs typ). 
        3. Ange en **Beskrivning av resurs typen**. 
        4. Ange en **Beskrivning av scenariot**. Den bör förklara de metoder eller scenarier där partner ämnen för dina resurser kan användas.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Skapa partner registrering":::            
1. Välj **Nästa: anpassad tjänst** längst ned på sidan. På fliken **kund tjänst** på sidan **skapa partner registrering** anger du information som prenumeranter ska använda för att kontakta dig om det skulle uppstå problem med händelse källan:
    1. Ange **telefonnumret**.
    1. Ange **anknytning** för telefonnumret.
    1. Ange en webb **adress** till support webbplatsen. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Skapa partner registrering-kund tjänst":::        
1. Välj **Nästa: Taggar** längst ned på sidan. 
1. Konfigurera följande värden på sidan **taggar** . 
    1. Ange ett **namn** och ett **värde** för taggen som du vill lägga till. Det här steget är **valfritt**. 
    1. Välj **Granska + skapa** längst ned på sidan för att skapa registreringen (typ av partner ämne).

## <a name="create-a-partner-namespace"></a>Skapa ett namn område för partner

1. I Azure Portal väljer du **alla tjänster** i den vänstra navigerings menyn och skriver **Event Grid partners namn rymder** i Sök fältet och väljer sedan det i listan. 
1. På sidan **Event Grid partner namn områden** väljer du **+ Lägg till** i verktygsfältet. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Partner namn rymder – Lägg till länk":::
1. På sidan **skapa partner namn område – grundläggande** anger du följande information.
    1. I avsnittet **projekt information** utför du följande steg: 
        1. Välj en Azure- **prenumeration**.
        1. Välj en befintlig **resurs grupp** eller skapa en resurs grupp. 
    1. Utför följande steg i avsnittet **information om namn områdes information** :
        1. Ange ett **namn** för namn området. 
        1. Välj en **plats** för namnområdet. 
    1. I avsnittet **registrerings information** utför du följande steg för att associera namn rummet med en partner registrering. 
        1. Välj den **prenumeration** där partner registreringen finns. 
        1. Välj den **resurs grupp** som innehåller partner registreringen. 
        1. Välj **partner registreringen** i list rutan.
    1. Välj **Nästa: Taggar** längst ned på sidan.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Skapa partner namn område – sidan grundläggande information":::
1. Lägg till taggar (valfritt) på sidan **taggar** .
    1. Ange ett **namn** och ett **värde** för taggen som du vill lägga till. Det här steget är **valfritt**.
    1. Välj **Granska + skapa** längst ned på sidan.         
1. På sidan **Granska + skapa** granskar du informationen och väljer **skapa**. 

## <a name="create-an-event-channel"></a>Skapa en händelse kanal
> [!IMPORTANT]
> Du måste begära en Azure-prenumeration, resurs grupp, plats och partner ämnes namn för att kunna skapa ett partner ämne som din användare kommer att äga och hantera.

1. Gå till sidan **Översikt** i namn området som du skapade. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Partner namn område – översikts sida":::
    partner-namespace-overview.png
1. Välj **+ händelse kanal** i verktygsfältet. 
1. På sidan **Skapa händelse kanal – grunder** anger du följande information. 
    1. Gör följande i avsnittet **kanal information** :
        1. För **händelse kanal namn** anger du ett namn för händelse kanalen. 
        1. Ange **källan**. Se [Cloud events 1,0-specifikationer](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) för att få en uppfattning om ett lämpligt värde för källan. Se även [det här exempel på Cloud Events-schemat](cloud-event-schema.md#sample-event-using-cloudevents-schema).
    1. I avsnittet **mål information** anger du information om mål partner ämnet som ska skapas för den här händelse kanalen. 
        1. Ange **ID: t för den prenumeration** där partner ämnet ska skapas. 
        1. Ange **namnet på resurs gruppen** där partner ämnes resursen kommer att skapas. 
        1. Ange ett **namn för partner ämnet**. 
    1. Välj **Nästa: filter** längst ned på sidan. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Sidan Skapa händelse kanal – grunder":::
1. Lägg till filter på sidan **filter** . utför du följande steg:
    1. Filtrera efter attribut för varje händelse. Endast händelser som matchar alla filter får levereras. Du kan ange upp till 25 filter. Jämförelser är inte Skift läges känsliga. Giltiga nycklar som används för filter varierar beroende på händelse schemat. I följande exempel kan, `eventid` , `source` `eventtype` , och `eventtypeversioin` användas för nycklar. Du kan också använda anpassade egenskaper inuti data nytto lasten med `.` som kapslings operator. Exempel: `data` , `data.key` , `data.key1.key2` .
    1. Välj **Nästa: ytterligare funktioner** längst ned på sidan. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Sidan Skapa händelse kanal – filter":::
        create-event-channel-filters-page.png
1. På sidan **ytterligare funktioner** kan du ange en **förfallo tid** och en **Beskrivning för partner ämnet**. 
    1. **Förfallo tiden** är den tid då ämnet och dess associerade händelse kanal tas bort automatiskt om de inte aktive ras av kunden. En standard på sju dagar används om ingen tid anges. Markera kryss rutan för att ange din egen förfallo tid. 
    1. Eftersom det här avsnittet är en resurs som inte har skapats av användaren, kan en **Beskrivning** hjälpa användaren att förstå vad det här avsnittet innehåller. En allmän beskrivning anges om ingen anges. Markera kryss rutan för att ange en egen partner ämne beskrivning. 
    1. Välj **Nästa: Granska + skapa**. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Sidan Skapa händelse kanal – ytterligare funktioner":::
1. Granska inställningarna på sidan **Granska och skapa** och skapa händelse kanalen genom att välja **skapa** . 

## <a name="next-steps"></a>Nästa steg
- [Översikt över partner ämnen](./partner-events-overview.md)
- [Formulär för partner ämnen onboarding](https://aka.ms/gridpartnerform)
- [Auth0-partner ämne](auth0-overview.md)
- [Så här använder du Auth0-partner ämnet](auth0-how-to.md)