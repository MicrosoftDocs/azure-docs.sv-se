---
title: Så här använder du komponenten komponenter i Azure-certifierad enhets Portal
description: En guide om hur du bäst använder komponent funktionen i avsnittet enhets information för att beskriva din enhet korrekt
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 091a666026853d677f1e9ed7e5e1736307cecbe8
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969454"
---
# <a name="add-components-on-the-portal"></a>Lägg till komponenter på portalen

När du har slutfört [självstudien för att lägga till enhets information](tutorial-02-adding-device-details.md) i certifierings projektet förväntas du beskriva maskin varu specifikationerna för enheten. För att göra det kan användarna markera flera separata maskin varu produkter (kallas **komponenter**) som utgör enheten. På så sätt kan du bättre befordra enheter som medföljer ytterligare maskin vara och gör det möjligt för kunder att hitta rätt produkt genom att söka i katalogen baserat på dessa funktioner.

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara inloggad och ha ett projekt för enheten som skapats på [Azure-certifierad enhets Portal](https://certify.azure.com). Mer information finns i [självstudien](tutorial-01-creating-your-project.md).

## <a name="how-to-add-components"></a>Så här lägger du till komponenter

Varje projekt som skickas för certifiering innehåller en **kund färdig produkt** komponent (som i många fall kommer att representera den holistiska produkten). För att bättre förstå distinktionen av en kund färdig produkt komponent typ, se vår [certifierings ord lista](./resources-glossary.md). Du kan ta med alla ytterligare komponenter om du vill ta med när du ska avbilda din enhet korrekt.

1. Välj `Add a component` fliken produkt information.

    ![Lägg till en komponent länk](./media/images/add-a-component-link.png)

1. Fyll i relevanta formulär fält för komponenten.

    ![Avsnittet komponent information](./media/images/component-details-section.png)

1. Spara informationen med hjälp av `Save Product Details` knappen längst ned på sidan:  

    ![Knappen Spara produkt information](./media/images/save-product-details-button.png)

1. När du har sparat komponenten kan du ytterligare skräddarsy de maskin varu funktioner som stöds. Välj `Edit` länken efter komponent namn.  

    ![Knappen Redigera komponent](./media/images/component-edit.png)

1. Ange relevant information om maskin varu kapacitet där det är lämpligt.  

    ![Bild av avsnitt om redigerbara komponenter](./media/images/component-selection-area.png)  

    De redigerbara komponent fälten (visas ovan) inkluderar:

    - **Allmänt**: maskin varu information, till exempel processorer och säker maskin vara
    - **Anslutning**: anslutnings alternativ, protokoll och gränssnitt, till exempel radio (s) och GPIO
    - **Acceleratorer**: Ange maskin varu acceleration, till exempel GPU och VPU
    - **Sensorer**: Ange tillgängliga sensorer som GPS och vibrationer
    - **Ytterligare specifikationer**: ytterligare information om enheten, till exempel fysiska dimensioner och information om lagring/batteri

1. Välj längst `Save Product Details` ned på sidan produkt information.

## <a name="component-use-requirements-and-recommendations"></a>Krav och rekommendationer för komponent användning

Du kan ha frågor om hur många komponenter som ska tas med eller vilken komponent typ som ska användas. Nedan visas exempel på några exempel scenarier med enheter som du kan certifiera, och hur du kan använda komponenternas funktion.

| Produkttyp                                       | Nej. Komponenter | Komponent 1/Anslutnings typ      | Komponenter 2 +/typ av bifogad fil                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Färdig produkt                                   | 1          | Kund färdig produkt, diskret | Ej tillämpligt                                              |
| Färdig produkt med **löstagbara kring utrustning (er)** | 2 eller flera  | Kund färdig produkt, diskret | Kring utrustning/diskret eller integrerad              |
| Färdig produkt med **integrerad (a) komponent (er)**  | 2 eller flera  | Kund färdig produkt, diskret | Välj lämplig typ/diskret eller integrerad |
| Solution-Ready dev-paket                             | 2 eller flera  | Kund färdig produkt, diskret | Välj lämplig typ/diskret eller integrerad |

## <a name="example-component-usage"></a>Exempel på komponent användning

Nedan visas exempel på hur en OEM-tillverkare som kallas Contoso använder funktionen komponenter för att certifiera sin produkt, som kallas Falcon.

1. Falcon är en komplett fristående enhet som inte integreras i en större produkt.
    1. Nej. av komponenter: 1
    1. Komponent enhets typ: kund färdig produkt
    1. Typ av bilaga: diskret

     ![Bild av kund färdig produkt](./media/images/customer-ready-product.png)

1. Falcon är en enhet som innehåller en integrerad Peripheral-kamera som tillverkas av INC-elektronik som ansluter via USB till Falcon.
    1. Nej. av komponenter: 2
    1. Komponent enhets typ: kund färdig produkt, kring utrustning
    1. Typ av bifogad fil: diskret, integrerad
    
    > [!Note]
    > Peripheral-komponenten anses vara integrerad eftersom den inte är borttagbar.

     ![Bild av exempel komponent för kring utrustning](./media/images/peripheral.png)

1. Falcon är en enhet som innehåller ett integrerat system i modul från INC-elektronik som använder en inbyggd processor Apollo52 från företagets Espressif och har en ARM64-arkitektur.
    1. Nej. av komponenter: 2
    1. Komponent enhets typ: kund färdig produkt, system i modul
    1. Typ av bifogad fil: diskret, integrerad

    > [!Note]
    > Peripheral-komponenten anses vara integrerad eftersom den inte är borttagbar. Som-komponenten inkluderar även processor information.

     ![Bild av system on Module-exempel komponent ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Ytterligare tips

Vi har angett nedan mer information om vår användnings princip för komponenter. Om du har frågor om lämplig komponent användning kontaktar du vårt team på [iotcert@microsoft.com](mailto:iotcert@microsoft.com) och vi kommer att vara mer än glad att hjälpa!

1. Ett projekt får **bara** innehålla en kund färdig produkt komponent. Om du certifierar ett projekt med två oberoende enheter bör enheterna certifieras separat.
1. Det är främst upp till dig att använda (eller inte använda) komponenter för att marknadsföra enhetens kapacitet till potentiella kunder.
1. Under vår granskning av enheten kräver Azures certifierings team bara att minst en kund färdig produkt komponent visas. Vi kan dock begära ändringar av komponent informationen om informationen inte är klar eller verkar saknas (till exempel om komponent tillverkaren inte har angetts för en kund färdig produkt typ).

## <a name="next-steps"></a>Nästa steg

Nu när du är redo att använda vår komponent funktion är du redo att slutföra din enhets information eller redigera projektet för ytterligare klarhet.

- [Självstudie: lägga till enhets information](tutorial-02-adding-device-details.md)
- [Redigera din publicerade enhet](how-to-edit-published-device.md)

