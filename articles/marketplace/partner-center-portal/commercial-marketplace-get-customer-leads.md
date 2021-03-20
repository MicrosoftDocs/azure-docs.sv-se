---
title: Hantering av leads från Microsofts kommersiella marknads platser
description: Lär dig mer om att skapa och ta emot kund leads från din Microsoft AppSource och Azure Marketplace-erbjudanden
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489343"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Kundleads från ditt erbjudande på den kommersiella marknadsplatsen

Leads är kunder som är intresserade av eller distribuerar dina erbjudanden från [Microsoft AppSource](https://appsource.microsoft.com) och [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan ta emot kund leads när ditt erbjudande har publicerats till den kommersiella marknads platsen. I den här artikeln förklaras följande koncept för lead-hantering:

* Hur ditt kommersiella Marketplace-erbjudande genererar kund leads för att se till att du inte kan missa affärs möjligheter. 
* Så här ansluter du ditt CRM-system (Customer Relations hip Management) till ditt erbjudande så att du kan hantera dina leads på en central plats.
* Lead-data vi skickar dig så att du kan följa upp kunder som har nått dig.

## <a name="generate-customer-leads"></a>Generera kund leads

Här är platser där ett lead genereras:

- En kund samtycker till att dela sin information när de har valt **kontakta mig** från den kommersiella Marketplace. Det här leadet är ett *initialt intresse* lead. Vi delar information med dig om kunden som har visat intresse för att få din produkt. Leadet ligger överst i förvärvs tratten.

    ![Dynamics 365 kontakta mig](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- En kund väljer **Hämta nu** (eller väljer **skapa** i [Azure Portal](https://portal.azure.com/)) för att få ditt erbjudande. Det här leadet är ett *aktivt* lead. Vi delar information med dig om kunden som har börjat distribuera produkten.

    ![Knappen Hämta nu i SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Knappen Skapa i Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- En kund väljer **test enhet** eller **kostnads fri utvärderings version** för att testa ditt erbjudande. Test enheter eller kostnads fria utvärderings versioner kan påskyndas för att du ska kunna dela din verksamhet direkt med potentiella kunder utan några hinder i registreringen.

    ![Knappen Dynamics 365-testenhet](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Kostnads fri utvärderings knapp för Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Anslut till ditt CRM-system

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Förstå lead-data

Varje lead som du får under kund förvärvs processen har data i specifika fält. Det första fältet som ska inblickas för är `LeadSource` fältet, som följer detta format: **käll åtgärds**  |  **erbjudande**.

**Källor**: värdet för det här fältet fylls i baserat på marknads platsen som skapade leadet. Möjliga värden är `"AzureMarketplace"` , `"AzurePortal"` och `"AppSource (SPZA)"` .

**Åtgärder**: värdet för det här fältet fylls i baserat på den åtgärd som kunden vidtog i marknads platsen som skapade leadet.

Möjliga värden:

- **"Ins"**: står för *installation*. Den här åtgärden är i Azure Marketplace eller AppSource när en kund köper din produkt.
- **"PLT"**: står för en *utvärderings version av partner lampan*. Den här åtgärden är i AppSource när en kund väljer alternativet **kontakta mig** .
- **"DNC"**: står *inte för kontakt*. Den här åtgärden är i AppSource när en partner som fanns tvärs över på din app-sida uppmanas att kontaktas. Vi delar ett meddelande om att kunden fanns tvärt i listan i din app, men de behöver inte kontaktas.
- **"Skapa"**: den här åtgärden finns bara i Azure Portal och genereras när en kund köper ditt erbjudande till sitt konto.
- **"StartTestDrive"**: den här åtgärden gäller endast för alternativet **test enhet** och genereras när en kund startar sin test-enhet.

**Erbjudanden**: du kan ha flera erbjudanden i den kommersiella marknads platsen. Värdet för det här fältet fylls i baserat på det erbjudande som genererade leadet. Utgivar-ID och erbjudande-ID skickas både i det här fältet och är värden som du angav när du publicerade erbjudandet till Marketplace.

I följande exempel visas värden i förväntat format `publisherid.offerid` : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Kund information

Kundens information skickas via flera fält. I följande exempel visas kund information som finns i ett lead:

- FirstName: John
- LastName: Smith
- E-post: jsmith \@ Microsoft.com
- Telefon: 1234567890
- Land: US
- Företag: Microsoft
- Rubrik: CTO

>[!NOTE]
>Alla data i föregående exempel är alltid tillgängliga för varje lead. Eftersom du får leads från flera steg som nämns i avsnittet "Skapa kund leads" är det bästa sättet att hantera leads att ta bort posterna och anpassa uppföljningen. På så sätt får varje kund ett lämpligt meddelande och du skapar en unik relation.

## <a name="best-practices-for-lead-management"></a>Metod tips för ledar hantering

Här följer några rekommendationer för att driva leads genom din försäljnings cykel:

- **Process**: definiera en klar försäljnings process, med mil stolpar, analys och ta bort teamets ägarskap.
- **Kvalificering**: definiera förutsättningar, som anger om ett lead har varit fullständigt. Se till att Sälj-eller marknadsförings representanter kvalificerar leads noggrant innan du tar dem genom hela försäljnings processen.
- **Uppföljning**: Glöm inte att följa upp inom 24 timmar. Du får leadet i din CRM-valmöjlighet direkt efter att kunden har distribuerat en testenhet. skicka dem med e-post medan de fortfarande är i varma. Begär att schemalägga ett telefonsamtal för att bättre förstå om produkten är en bra lösning för deras problem. Vänta en typisk transaktion för att kräva ett antal uppföljnings anrop.
- **Nurture**: Nurture dina leads för att få dig på väg till en högre vinst marginal. Checka in, men Bombard inte. Vi rekommenderar att du skickar e-post minst några gånger innan du stänger ut dem. lämna inte upp efter det första försöket. Kom ihåg att dessa kunder direkt arbetar med din produkt och förbrukade tid i en kostnads fri utvärderings version. de är fantastiska utsikter.

När den tekniska konfigurationen är på plats kan du lägga till dessa leads i din aktuella strategi och drift process för försäljning och marknadsföring. Vi är intresserade av att bättre förstå den övergripande Sälj processen och vill arbeta nära dig med att tillhandahålla högkvalitativa leads och tillräckligt med data för att lyckas. Vi välkomnar din feedback om hur vi kan optimera och förbättra de leads som vi skickar till dig med ytterligare data för att hjälpa kunderna att lyckas. Berätta för oss om du är intresse rad av att [ge feedback](mailto:AzureMarketOnboard@microsoft.com) och förslag för att göra det möjligt för ditt säljteam att bli mer framgångs rik med leads på kommersiella platser.

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om hantering av leads](../lead-management-faq.md)