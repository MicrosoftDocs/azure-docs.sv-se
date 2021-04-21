---
title: Planera Dynamics 365-erbjudanden för Microsoft AppSource
description: Planera Dynamics 365-erbjudanden för Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/16/2021
ms.openlocfilehash: c5746b70f84ff68ea578ba141cc5251cc5ce07ea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819426"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Planera ett Microsoft Dynamics 365-erbjudande

Den här artikeln beskriver de olika alternativen och funktionerna i ett Dynamics 365-erbjudande Microsoft AppSource på den kommersiella marknadsplatsen. AppSource innehåller erbjudanden som bygger på eller utökar Microsoft 365, Dynamics 365, PowerApps och Power BI. AppSource tillåter betalerbjudanden *(Get It Now),* list *(Kontakta* mig) och *utvärderingsversion (Try It Now).*

Innan du börjar skapar du ett konto för den kommersiella marknadsplatsen [i Partnercenter](./partner-center-portal/create-account.md) och ser till att det har registrerats i programmet för den kommersiella marknadsplatsen. Granska även [publiceringsprocessen och riktlinjerna.](/office/dev/store/submit-to-appsource-via-partner-center)

## <a name="licensing-options"></a>Licensieringsalternativ

När du förbereder publiceringen av ett nytt erbjudande måste du bestämma vilket licensieringsalternativ du ska välja. Detta avgör vilken ytterligare information du behöver ange senare när du skapar erbjudandet i Partnercenter.

Det här är de tillgängliga licensieringsalternativen för Dynamics 365-erbjudanden:

| Licensieringsalternativ | Transaktionsprocess |
| --- | --- |
| Hämta nu (kostnadsfritt) | Visa en lista med ditt erbjudande till kunder utan kostnad. |
| Kostnadsfri utvärderingsversion (lista) | Erbjuda dina kunder en kostnadsfri utvärderingsversion på en, tre eller sex månader. Erbjudande som visar en lista över kostnadsfria utvärderingsversioner skapas, hanteras och konfigureras av din tjänst och har inte prenumerationer som hanteras av Microsoft. |
| Kontakta mig | Samla in kundkontaktinformation genom att ansluta ditt CRM-system (Customer Relationship Management). Kunden uppmanas att ge tillstånd att dela sin information. Dessa kunduppgifter, tillsammans med erbjudandets namn, ID och marketplace-källa där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i **avsnittet Kund** leads på erbjudandetypens sida **för erbjudandekonfiguration.** |
|

## <a name="test-drive"></a>Provkörning

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Kundlyssnare

När du publicerar ett erbjudande på den kommersiella marknadsplatsen med Partnercenter bör du ansluta det till ditt CRM-system (Customer Relationship Management). På så sätt kan du ta emot kundkontaktinformation så snart någon uttrycker intresse för eller använder din produkt. Du måste ansluta till en CRM om du vill aktivera en provkörning. Annars är det valfritt att ansluta till en CRM. PartnerCenter stöder Azure-tabeller, Dynamics 365 Customer Engagement, HTTPS-slutpunkt, Marketo och Salesforce.

## <a name="legal"></a>Juridisk information

Skapa **dina allmänna villkor.** Kunderna måste godkänna dem innan de kan prova ditt erbjudande. Microsoft har ett standardavtal, men det gäller inte för Dynamics 365-erbjudanden.

## <a name="offer-listing-details"></a>Erbjudandeinformation

Här är ett exempel på hur erbjudandeinformation visas i Microsoft AppSource (angivna priser är endast exempel och återspeglar inte faktiska kostnader):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

> [!NOTE]
> Erbjudandelisteinnehåll måste inte vara på engelska om erbjudandebeskrivningen börjar med frasen "Det här programmet är endast tillgängligt på [icke-engelska språk]".

Förbered de här objekten i förväg för att skapa ditt erbjudande på ett enklare sätt. Alla krävs förutom där det anges.

- **Namn** – Namnet visas som titeln på din erbjudandelista på den kommersiella marknadsplatsen. Namnet kan vara varumärkesskyddat. Den får inte innehålla emojis (såvida de inte är varumärken och upphovsrättssymboler) och är begränsade till 50 tecken.
- **Sammanfattning av sökresultat** – Syftet med eller funktionen för ditt erbjudande som en enskild mening utan radbrytningar på 100 tecken eller mindre. Detta används i sökresultat för den eller de som visar en lista på den kommersiella marknadsplatsen.
- **Beskrivning** – Den här beskrivningen visas i översikten över den eller de kommersiella marknadsplatserna. Överväg att inkludera ett mervärde, viktiga fördelar, avsedd användarbas, alla kategorier eller branschassociationer, köpmöjligheter i appen, eventuella nödvändiga avslöjanden och en länk för att lära dig mer. Den här textrutan har kontroller för RTF-redigerare för att göra beskrivningen mer engagerande. Du kan också använda HTML-taggar för formatering.
- **Sök nyckelord** (valfritt) – Upp till tre söknyckelord som kunder kan använda för att hitta ditt erbjudande. Inkludera inte erbjudandets namn **och** **beskrivning;** texten inkluderas automatiskt i sökningen.
- **Produkt som din app fungerar med** (valfritt) – Namnen på upp till tre produkter som ditt erbjudande fungerar med.
- **Hjälp- och sekretesspolicylänkar** – WEBBADRESSerna för ditt erbjudandes hjälp och företagets sekretesspolicy. Du är ansvarig för att se till att din app följer sekretesslagar och -föreskrifter.
- **Kontaktinformation**
  - **Supportkontakt** – Namn, telefon och e-post som Microsoft-partner ska använda när dina kunder öppnar biljetter. Inkludera URL:en för din supportwebbplats.
  - **Teknisk kontakt** – Namn, telefon och e-postadress som Microsoft kan använda direkt när det uppstår problem med ditt erbjudande. Den här kontaktinformationen visas inte på den kommersiella marknadsplatsen.
- **Stöddokument** – Upp till tre kundriktade dokument, till exempel whitepapers, checklistor eller PowerPoint-presentationer, i PDF-format.
- **Media**
    - **Logotyper –** En PNG-fil för den **stora** logotypen. Partnercenter använder detta för att skapa andra nödvändiga logotypstorlekar. Du kan också ersätta dem med olika bilder senare.
    - **Skärmbilder** – Minst en och upp till fem skärmbilder som visar hur ditt erbjudande fungerar. Bilderna måste vara 1 280 x 720 bildpunkter, i PNG-format och innehålla en bildtext.
    - **Videor** (valfritt) – Upp till fyra videor som visar ditt erbjudande. Inkludera ett namn, en URL för YouTube eller Vimeo och en PNG-miniatyr på 1 280 x 720 pixlar.

> [!Note]
> Ditt erbjudande måste uppfylla de allmänna [certifieringsprinciperna för den kommersiella marknadsplatsen](/legal/marketplace/certification-policies#100-general) för att publiceras på den kommersiella marknadsplatsen.

## <a name="additional-sales-opportunities"></a>Ytterligare affärsmöjligheter

Du kan välja att använda marknadsförings- och försäljningskanaler som stöds av Microsoft. När du skapar ditt erbjudande i Partnercenter visas två en flik i slutet av processen **för säljpartner med Microsoft**. Med det här alternativet kan Microsofts säljteam överväga din berättigade IP-säljlösning när de utvärderar sina kunders behov. Mer [information om hur du förbereder ditt erbjudande för utvärdering](commercial-marketplace-co-sell.md) finns i alternativet för säljpartner i Partnercenter.

## <a name="next-steps"></a>Nästa steg

När du har övervägt planeringsobjekten som beskrivs ovan väljer du något av följande (finns även i innehållsförteckningen till vänster) för att börja skapa ditt erbjudande.

| Publiceringsguide    | Kommentarer  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) | När du skapar för Enterprise Edition bör du först granska dessa ytterligare [publiceringsprocesser och riktlinjer.](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source) |
| [Dynamics 365 för Business Central](partner-center-portal/create-new-business-central-offer.md) |   |
| [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) | Granska först dessa ytterligare [publiceringsprocesser och riktlinjer.](/dynamics365/customer-engagement/developer/publish-app-appsource) |
| [Power BI](/partner-center-portal/create-power-bi-app-offer.md) | Granska först dessa ytterligare [publiceringsprocesser och riktlinjer.](/power-bi/developer/office-store) |
|
