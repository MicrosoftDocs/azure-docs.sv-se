---
title: Programmet Azure Certified Device – självstudie – lägga till enhets information
description: En steg-för-steg-guide om hur du lägger till enhets information i projektet på Azure-certifierad enhets Portal
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: c2a67e794932504150a1eacedd5418642623a20c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969772"
---
# <a name="tutorial-add-device-details"></a>Självstudie: Lägg till enhets information

Nu har du skapat projektet för din enhet och du är redo att börja certifierings processen! Först lägger vi till din enhets information. De innehåller tekniska specifikationer som dina kunder kan se i katalogen för Azure-certifierade enheter och marknadsförings information som de ska använda för att köpa när de har fattat ett beslut.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till enhets information med hjälp av funktionerna komponenter och beroenden
> * Ladda upp en kom igång-guide för din enhet
> * Ange marknadsförings information för kunder för att köpa din enhet
> * Du kan också identifiera eventuella bransch certifieringar

## <a name="prerequisites"></a>Förutsättningar

* Du måste vara inloggad och ha ett projekt för enheten som skapats på [Azure-certifierad enhets Portal](https://certify.azure.com). Mer information finns i [självstudien](tutorial-01-creating-your-project.md).
* Du bör ha en guide för att komma igång med din enhet i PDF-format. Vi tillhandahåller ett antal kom igång-mallar som du kan använda, beroende på både certifierings programmet och det språk du föredrar. Mallarna är tillgängliga på vår [Kom igång-mall](https://aka.ms/GSTemplate "Kom igång-mallar") GitHub-plats.

## <a name="adding-technical-device-details"></a>Lägga till teknisk enhets information

I det första avsnittet av projekt sidan, som kallas information om indata-enhet, kan du ange information om kärn funktionerna för maskin vara på enheten, till exempel enhets namn, beskrivning, processor, operativ system, anslutnings alternativ, maskin varu gränssnitt, bransch protokoll, fysiska dimensioner med mera. Även om många av fälten är valfria, kommer merparten av den här informationen att göras tillgänglig för potentiella kunder i Azure Certified Device-katalogen om du väljer att publicera enheten efter att den har certifierats.

1. Klicka `Add` i avsnittet information om indata-enhet på projekt sammanfattnings sidan för att öppna avsnittet enhets information. Du kommer att se fem avsnitt som du kan slutföra.

![Bild av sidan projekt information](./media/images/device-details-menu.png)

2. Granska informationen som du tidigare angav när du skapade projektet på `Basics` fliken.
1. Granska de certifieringar som du använder för med din enhet på `Certifications` fliken.
1. Öppna `Product details` fliken och välj minst ett operativ system.
1. Lägg till **minst** en diskret komponent som beskriver din enhet. Du kan visa ytterligare vägledning om komponent användning [här](how-to-using-the-components-feature.md).
1. Klicka på `Save`. Du kommer sedan att kunna redigera komponent enheten och lägga till mer avancerad information.
1. Visa en lista över ytterligare enhets information som inte har registrerats av komponent informationen under `Additional product details` .
1. Om du har markerat `Other` i något av komponent fälten eller har en särskild omständighet som du vill flagga med Azure-certifierings gruppen, lämnar du en klargörande kommentar i `Comments for reviewer` avsnittet.
1. Använd `Dependencies` fliken för att visa eventuella beroenden om enheten kräver ytterligare maskin vara eller tjänster för att skicka data till Azure. Du kan visa ytterligare vägledning om att Visa beroenden [här](how-to-indirectly-connected-devices.md).
1. När du är nöjd med den angivna informationen kan du använda `Review` fliken för en skrivskyddad översikt över den fullständiga uppsättningen enhets information som har angetts.
1. Klicka `Project summary` högst upp på sidan för att återgå till sidan Sammanfattning.

![Granska projekt informations Sidan](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Laddar upp en guide för att komma igång

Guiden kom igång är ett PDF-dokument för att förenkla installationen och konfigurationen och hanteringen av produkten. Syftet med detta är att göra det enkelt för kunder att ansluta och stödja enheter i Azure med hjälp av din enhet. Som en del av certifierings processen kräver vi våra partners för att tillhandahålla **en** guide för att komma igång för sitt mest relevanta certifierings program.

1. Kontrol lera att du har angett all information som du har gett i guiden för att komma igång med PDF enligt de angivna [mallarna](https://aka.ms/GSTemplate). Vilken mall du använder beror på vilken certifierings skylt som du använder för. (Till exempel kan en IoT Plug and Play-enhet använda IoT Plug and Play-mallen. Enheter som *endast* gäller för Azure-certifierad enhets bas linje certifikat kommer att använda Azure-certifierad enhets mall.)
1. Klicka `Add` i avsnittet "kom igång" på sidan projekt Sammanfattning.

![Bild av knappen GSG](./media/images/gsg-menu.png)

2. Klicka på "Välj fil" för att ladda upp PDF-filen.
1. Granska dokumentet i förhands granskningen för formatering.
1. Spara överföringen genom att klicka på knappen Spara.
1. Klicka `Project summary` högst upp på sidan för att återgå till sidan Sammanfattning.

## <a name="providing-marketing-details"></a>Tillhandahålla marknadsförings information

I det här avsnittet kommer du att tillhandahålla kund färdig marknadsförings information för din enhet. De här fälten visas i Azure Certified Device-katalogen om du väljer att publicera din certifierade enhet.

1. Klicka `Add` i avsnittet "Lägg till marknadsförings information" för att öppna sidan marknadsförings information.

![Bild av avsnittet marknadsförings information](./media/images/marketing-details.png)

1. Ladda upp ett produkt foto i JPEG-eller PNG-format som ska användas i katalogen.
1. Skriv en kort beskrivning av enheten som ska visas på sidan produkt beskrivning i katalogen.
1. Ange enhetens geografiska tillgänglighet.
1. Ange en länk till tillverkarens marknadsförings sida för den här enheten. Detta bör vara en länk till en plats som innehåller ytterligare information om enheten.
    > [!Note]
    > Se till att alla angivna URL: er är giltiga eller är aktiva vid tidpunkten för publiceringen efter godkännande. *)

1. Ange upp till 3 mål branscher som enheten är optimerad för.
1. Ange information för upp till 5 åter försäljare av enheten. Detta kan omfatta tillverkarens egen webbplats.

    > [!Note]
    > Om ingen URL för åter försäljare produkt sidan anges, `Shop` kommer knappen i katalogen att standardvärdet för den länk som anges för `Distributor page` , vilket inte är speciellt för enheten. Vi rekommenderar att åter försäljarens URL leder till en specifik sida där en kund kan köpa en enhet, men är inte obligatorisk. Om distributören är densamma som tillverkaren kan denna URL vara samma som tillverkarens marknadsförings sida. *)

1. `Save`Bekräfta informationen genom att klicka.
1. Klicka `Project summary` högst upp på sidan för att återgå till sidan Sammanfattning.

## <a name="declaring-additional-industry-certifications"></a>Att deklarera ytterligare bransch certifieringar

Du kan också befordra ytterligare bransch certifieringar som du kan ha tagit emot för din enhet. Dessa certifieringar kan bidra till ytterligare klarhet om den avsedda användningen av enheten och går att söka i Azure-certifierad enhets katalog.

1. Klicka `Add` i avsnittet "Tillhandahåll bransch certifieringar".
1. Klicka `Add a certification` om du vill välja från en lista över vanliga certifierings program för branschen. Om produkten har uppnått en certifiering som inte finns i listan kan du ange ett anpassat sträng värde genom att välja `Other (please specify)` .
1. Du kan också ange en beskrivning eller information om granskaren. Dessa anteckningar kommer dock inte att vara offentligt tillgängliga för visning i katalogen.
1. `Save`Bekräfta informationen genom att klicka.
1. Klicka `Project summary` högst upp på sidan för att återgå till sidan Sammanfattning.

## <a name="next-steps"></a>Nästa steg

Nu har du slutfört processen med att beskriva din enhet! På så sätt blir det lättare för Azure Certified Reports-teamet och kunden att förstå produkten bättre. När du är nöjd med den information som du har tillhandahållit är du nu redo att gå vidare till test fasen i certifierings processen.
> [!div class="nextstepaction"]
> [Självstudie: testa din enhet](tutorial-03-testing-your-device.md)
