---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 81d2804d99896200ea6f68592ea168112e172c20
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762628"
---
Välj **build**. I fönstret som öppnas väljer du en mapp att exportera Xcode-projektet till.

   När exporten är klar visas en mapp som innehåller det exporterade Xcode-projektet.

   > [!NOTE]
   > Om ett fönster visas med ett meddelande som frågar om du vill ersätta eller lägga till, rekommenderar vi att du väljer **Lägg till**, eftersom det är snabbare. Du bör endast välja **Ersätt** om du har ändrat till gångar i din scen. Du kan till exempel lägga till, ta bort eller ändra överordnade och underordnade relationer, eller så kan du lägga till, ta bort eller ändra egenskaper. Om du bara gör käll kods ändringar bör **tillägg** vara tillräckligt.

## <a name="open-the-xcode-project"></a>Öppna Xcode-projektet

Nu kan du öppna `Unity-iPhone.xcodeproj` projektet i Xcode. 

Du kan antingen starta Xcode och öppna det exporterade `Unity-iPhone.xcodeproj` projektet eller starta projektet i Xcode genom att köra följande kommando från den plats där du exporterade projektet:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Välj den rot **Uppunions-iPhone-** noden för att visa projekt inställningarna och välj sedan fliken **Allmänt** .

Under **distributions information** ser du till att distributions målet är inställt på **iOS 11,0**.

Välj fliken **signerings & funktioner** och se till att **Automatisk hantering av signering** är aktiverat. Om det inte är det aktiverar du det och återställer sedan build-inställningarna genom att välja **Aktivera automatisk** i fönstret som visas.

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Anslut iOS-enheten till Mac och Ställ in det **aktiva schemat** på din iOS-enhet.

   ![Skärm bild av min iPhone-knapp för att välja enheten.](./media/spatial-anchors-unity/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

   ![Skärm bild av pilknappen "distribuera och köra".](./media/spatial-anchors-unity/deploy-run.png)
