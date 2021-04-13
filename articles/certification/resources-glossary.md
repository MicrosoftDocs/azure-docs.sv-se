---
title: Ord lista för Azure Certified Device-program
description: En lista med vanliga termer som används i programmet Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: f58c29c6bd2c22f37d2285ac6e93c6f54e47c4e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305097"
---
# <a name="azure-certified-device-program-glossary"></a>Ord lista för Azure Certified Device-program

Den här guiden innehåller definitioner av termer som ofta används i programmet Azure Certified Device och Portal. Se den här ord listan för att klargöra certifierings processen. Den här ord listan kategoriseras utifrån viktiga certifierings koncept som du kan ha frågor om.

## <a name="device-class"></a>Enhets klass

När du skapar ett certifierings projekt uppmanas du att ange en enhets klass. Enhets klass avser den form faktor eller klassificering som bäst representerar din enhet.

- **Gateway**

    En enhet som bearbetar data som skickas över ett IoT-nätverk.

- **Mäta**

    En enhet som identifierar och svarar på ändringar i en miljö och ansluter till gateways för att bearbeta ändringarna.

- **Övrigt**

    Om du väljer övrigt lägger du till en beskrivning av enhets klassen i dina egna ord. Med tiden kan vi fortsätta att lägga till nya värden i den här listan, särskilt när vi fortsätter att övervaka feedback från våra partner.

## <a name="device-type"></a>Enhetstyp

Du uppmanas också att välja en av två enhets typer under certifierings processen.

- **Färdig produkt**

    En enhet som är lösnings klar och redo för produktions distribution. Vanligt vis i en färdig Forms faktor med inbyggd program vara och ett operativ system. Detta kan vara generella enheter som kräver ytterligare anpassning eller specialiserade enheter som inte kräver några ändringar av användningen.
- **Lösnings klar dev-paket**

    Ett utvecklings paket som innehåller maskin-och program vara som är idealisk för enkel prototyper, vanligt vis inte i en färdig Forms faktor. Innehåller vanligt vis exempel kod och självstudier för att aktivera snabb prototyper.

## <a name="component-type"></a>Komponent typ

I avsnittet enhets information beskriver du enheten genom att lista komponenter efter komponent typ. Du kan visa mer information om komponenterna [här](./how-to-using-the-components-feature.md).

- **Kund färdig produkt**

    En komponent representation av den övergripande eller primära enheten. Detta skiljer sig från en **färdig produkt**, som är en klassificering av enheten som redo för kund användning utan ytterligare utveckling. En färdig produkt kommer att innehålla en kund färdig produkt komponent.
- **Utvecklings tavlan**

    Antingen en integrerad eller avtagbar tavla med mikroprocessor för enkel anpassning.
- **Routrar**

    Antingen ett integrerat eller löstagbart tillägg till produkten (till exempel ett tillbehör). Detta är vanligt vis enheter som ansluter till huvud enheten, men som inte bidrar till enhetens primära funktioner. Den innehåller i stället ytterligare funktioner. Minnes-, RAM-, lagrings-, hård diskar och CPU: er betraktas inte som kring utrustning (de bör istället visas under ytterligare specifikationer för kund klara produkt komponenter).
- **System-on-Module**  

    En krets på kort nivå som integrerar en systemfunktion i en enda modul.

## <a name="component-attachment-method"></a>Metod för komponent bilaga

Komponentens bilaga metod är en annan komponent information som informerar kunden om hur komponenten integreras i den övergripande produkten.

- **Integrerat**
 
    Avser när en enhets komponent är en del av produktens huvud chassi. Detta avser oftast en komponent typ för kring utrustning som inte kan tas bort från enheten.  
    Exempel: en integrerad temperatur sensor i ett Gateway-chassi.

- **Åtskild**

    Avser när en komponent **inte** ingår i huvud-chassit för produkten.  
    Exempel: en extern temperatur sensor som måste kopplas till enheten.


## <a name="next-steps"></a>Nästa steg

Den här ord listan vägleder dig genom processen för att certifiera ditt projekt på portalen. Nu är du redo att börja projektet!
- [Självstudie: skapa ditt projekt](./tutorial-01-creating-your-project.md)
