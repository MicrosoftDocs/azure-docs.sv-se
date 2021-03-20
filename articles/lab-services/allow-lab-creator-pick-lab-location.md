---
title: Tillåt labb skapare att välja plats i Azure Lab Services
description: Den här artikeln beskriver hur en labb konto administratör kan låta labb skapare välja platser för sina labb.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444207"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Tillåt labb skapare att välja plats för labbet i Azure Lab Services
I Azure Lab Services kan en labb konto ägare tillåta labb skapare (lärare) att välja en plats för de labb som de skapar. Den här platsen kan skilja sig från labb kontots plats. En plats är en grupp med Azure-regioner. Till exempel är USA plats en grupp med regioner som USA, västra, USA, västra och så vidare. 

Som labb konto ägare kan du välja alternativet **Tillåt labb skapare att välja labb plats** när du skapar ett labb konto och när du har skapat labb kontot (eller ett befintligt labb konto). 

## <a name="at-the-time-of-lab-account-creation"></a>När labb kontot skapas
När du skapar ett labb konto ser du det här alternativet på den första skärmen (fliken **grundläggande** ). 

![Aktivera alternativet vid tidpunkten för att skapa labbet](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Det här alternativet är inaktiverat om du väljer ett virtuellt dator nätverk för ditt labb konto på fliken **Avancerat** .  

![När peer Virtual Network är aktiverat](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>När labb kontot har skapats
När du har skapat labb kontot kan du aktivera eller inaktivera det här alternativet genom att följa dessa steg: 

1. På sidan **labb konto** väljer du **Lab-inställningar** på den vänstra menyn.
2. Välj alternativet **Tillåt labb skapare att välja labb plats** om du vill att Labbets skapare ska kunna välja en plats för labbet. Om den är inaktive rad skapas labben automatiskt på samma plats som labb kontot finns i. 
    
    Det här fältet är inaktiverat när du väljer ett virtuellt nätverk för fältet **peer Virtual Network** . Det beror på att labb i labb kontot måste finnas i samma region som labb kontot för att få åtkomst till resurser i det virtuella peer-nätverket. 
1. Välj **Spara** i verktygsfältet. 

    ![Labb inställningar](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Inget virtuellt nätverk och val av plats tillåts inte
I det här scenariot har du inte aktiverat alternativet **Tillåt labb skapare för att välja labb plats** . 

![Ingen labb plats](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Sedan ser labb skapare (lärare) inte något alternativ för att välja en plats för labbet. De kommer att se priset per timme för varje storleks alternativ som är tillgängligt för dem. När de skapar ett labb kommer det att skapas i en Azure-region som finns på samma plats som den Azure-region där deras labb konto finns. Om labb kontot till exempel är i **västra USA**, kan labbet skapas i **södra centrala USA** , men det skulle inte skapas i **Kanada, öst**. Vi garanterar inte något om den region som vi väljer undan från den platsen. Om en storlek för närvarande är begränsad, ser labb skaparen en kryss ruta där de kan se de storlekar som vi normalt stöder men som inte är tillgängliga för tillfället. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>I virtuellt nätverk och val av plats tillåts inte
I det här scenariot är alternativet **Tillåt labb skapare att välja labb plats** inaktiverat eftersom du har valt ett virtuellt dator nätverk för labb kontot. Sedan ser labb skapare samma skärm som med föregående alternativ. Eftersom alla virtuella datorer måste finnas i samma Azure-region som det virtuella nätverket kommer labbet att skapas i samma Azure-region som det virtuella nätverket finns i. Om den aktuella regionen är begränsad till en storlek visas storleken som inte tillgänglig. 

## <a name="location-selection-is-enabled"></a>Val av plats har Aktiver ATS
När du väljer **Tillåt att labb skapare väljer att välja labb plats**, ser labb skapare (lärare) ett alternativ för att välja en plats när du skapar ett labb. 

![Välj en labb plats](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Labb skapare ser pris intervallet för alla platser som storleken är i och kan välja en plats. Labbet skapas i alla Azure-regioner som mappar till den platsen.

Om en plats är begränsad visas den inte i listan som standard. Expandera List rutan och välj **Visa otillgängliga platser för den här storleken**. 

![Visa otillgängliga platser](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Cost
Tidigare var priset baserat på den VM-storlek som du valde för labbet. Priset baseras nu på kombinationen av operativ system (OS), storlek och plats. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Anslut ditt labb nätverk till ett peer-virtuellt nätverk](how-to-connect-peer-virtual-network.md)
- [Koppla ett delat avbildnings galleri till ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägg till en användare som labb ägare](how-to-add-user-lab-owner.md)
- [Visa brand Väggs inställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)