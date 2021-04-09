---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358291"
---
1. Bläddra till src/Edge i Visual Studio Code. Du ser den. kuvert-fil som du skapade tillsammans med några filer för distributions mal len.

    Distributions mal len refererar till distributions manifestet för gräns enheten med några plats hållarnas värden. . Kuvert-filen innehåller värdena för dessa variabler.
1. Bläddra sedan till mappen src/Cloud-to-Device-console-app. Här ser du appsettings.jspå filen som du skapade tillsammans med några andra filer:

    * C2D-console-app. CSPROJ: det här är projekt filen för Visual Studio Code.
    * operations.jspå: den här filen visar de olika åtgärder som du vill att programmet ska köra.
    * Program. CS: den här exempel koden:

        * Läser in appinställningar.
        * Anropar live video analys på IoT Edge modulens direkta metoder för att skapa topologi, instansiera grafen och aktivera grafen.
        * Pausar så att du kan granska diagrammets utdata i **terminalfönstret** och de händelser som skickas till IoT Hub i fönstret **utdata** .
        * Inaktiverar graf-instansen, tar bort graf-instansen och tar bort diagram sto pol Ogin.
