---
title: Översikt över Service Health | Microsoft Docs
description: Lär dig hur Service Health ger dig en anpassningsbar instrument panel som spårar hälso tillståndet för dina Azure-tjänster i de regioner där du använder dem.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 8246b0ab93b95c13858e4ff96d0f24b255d05e55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90967794"
---
# <a name="service-health-overview"></a>Översikt över Service Health

Service Health ger dig en anpassningsbar instrument panel som spårar hälso tillståndet för dina Azure-tjänster i de regioner där du använder dem. På den här instrument panelen kan du spåra aktiva händelser som kontinuerliga tjänst problem, kommande planerat underhåll eller relevanta hälso rekommendationer. När händelser blir inaktiva placeras de i hälso tillstånds historiken i upp till 90 dagar. Slutligen kan du använda Service Health-instrumentpanelen för att skapa och hantera aviseringar om tjänst hälsa som proaktivt meddelar dig när tjänst problem påverkar dig.

## <a name="service-health-events"></a>Service Health händelser

Service Health spårar fyra typer av hälso händelser som kan påverka dina resurser:

1. **Tjänst problem** – problem i Azure-tjänster som påverkar dig just nu. 
2. **Planerat underhåll** – kommande underhåll som kan påverka tillgängligheten för dina tjänster i framtiden.  
3. **Hälso rekommendationer** – ändringar i Azure-tjänster som kräver din uppmärksamhet. Exempel innefattar utfasning av Azure-funktioner eller uppgraderings krav (till exempel uppgradering till ett PHP-ramverk som stöds).
4. **Säkerhets rekommendationer** – säkerhetsrelaterade aviseringar eller överträdelser som kan påverka tillgängligheten för dina Azure-tjänster.

> [!NOTE]
> Om du vill visa Service Health händelser måste användarna [beviljas rollen läsare](../role-based-access-control/role-assignments-portal.md) för en prenumeration.

## <a name="get-started-with-service-health"></a>Kom igång med Service Health

För att starta Service Health-instrumentpanelen väljer du panelen Service Health på instrument panelen för portalen. Om du tidigare har tagit bort panelen eller om du använder en anpassad instrument panel söker du efter Service Health tjänst i "fler tjänster" (längst ned till vänster på instrument panelen).

![Kom igång med Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Se aktuella problem som påverkar dina tjänster

I vyn **tjänst problem** visas eventuella pågående problem i Azure-tjänster som påverkar dina resurser. Du kan förstå när problemet började och vilka tjänster och regioner som påverkas. Du kan också läsa den senaste uppdateringen för att förstå vad Azure gör för att lösa problemet. 

[![Hantera tjänst problem](./media/service-health-overview/azure-service-health-overview-2.png)](./media/service-health-overview/azure-service-health-overview-2.png#lightbox)

Välj fliken **potentiell påverkan** för att se den specifika listan med resurser som du äger som kan påverkas av problemet. Du kan ladda ned en CSV-lista över dessa resurser för att dela med ditt team.

[![Hantera tjänst problem – påverkan](./media/service-health-overview/azure-service-health-overview-4.png)](./media/service-health-overview/azure-service-health-overview-4.png#lightbox)

## <a name="see-emerging-issues-which-may-impact-your-services"></a>Se nya problem som kan påverka dina tjänster

Det finns situationer när omfattande tjänst problem kan publiceras på [sidan Azure-status](https://status.azure.com) innan riktad kommunikation kan skickas till kunder som påverkas. För att säkerställa att Azure Service Health ger en omfattande vy över problem som kan påverka dig, finns det problem med Active Azures status sida i Service Health som *nya problem*. När en händelse är aktiv på sidan Azure-status finns en annons med nya problem i Service Health. Klicka på banderollen för att se all information om problemet.

![Problem med nya tjänster](./media/service-health-overview/azure-service-health-emerging-issue.png)

## <a name="get-links-and-downloadable-explanations"></a>Hämta länkar och nedladdnings bara förklaringar 

Du kan få en länk till problemet som du kan använda i problem hanterings systemet. Du kan ladda ned PDF och ibland CSV-filer för att dela med personer som inte har åtkomst till Azure Portal.   

[![Hantera tjänst problem – problem hantering](./media/service-health-overview/azure-service-health-overview-3.png)](./media/service-health-overview/azure-service-health-overview-3.png#lightbox)

## <a name="get-support-from-microsoft"></a>Få support från Microsoft

Kontakta supporten om resursen är i ett felaktigt tillstånd, även när problemet har lösts.  Använd support länkarna till höger på sidan.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Fästa en anpassad hälso karta på din instrument panel

Filtrera Service Health för att Visa affärs kritiska prenumerationer, regioner och resurs typer. Spara filtret och fäst en anpassad världskarta med hälsotillstånd på din portalinstrumentpanel. 

[![Filtrera personligt hälso tillstånds karta](./media/service-health-overview/azure-service-health-overview-6a.png)](./media/service-health-overview/azure-service-health-overview-6a.png#lightbox)

![Fästa en anpassad hälso karta](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurera aviseringar för tjänst hälsa

Service Health integreras med Azure Monitor för att varna dig via e-post, SMS och webhook-meddelanden när affärs kritiska resurser påverkas. Konfigurera en aktivitets logg avisering för lämplig tjänst hälso händelse. Dirigera aviseringen till lämpliga personer i din organisation med hjälp av åtgärds grupper. Mer information finns i [Konfigurera aviseringar för service Health](./alerts-activity-log-service-notifications-portal.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Nästa steg

Konfigurera aviseringar så att du får ett meddelande om hälso problem. Mer information finns i [metod tips för att konfigurera Azure Service Health aviseringar](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
