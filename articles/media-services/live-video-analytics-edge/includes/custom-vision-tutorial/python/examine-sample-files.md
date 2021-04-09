---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358294"
---
1. Bläddra till src/Edge i Visual Studio Code. Du ser den. kuvert-fil som du skapade tillsammans med några filer för distributions mal len.

    Distributions mal len refererar till distributions manifestet för gräns enheten med några plats hållarnas värden. . Kuvert-filen innehåller värdena för dessa variabler.
1. Bläddra sedan till mappen src/Cloud-to-Device-console-app. Här ser du appsettings.jspå filen som du skapade tillsammans med några andra filer:

   * operations.jsden här filen visar en lista över de olika åtgärder som du vill att programmet ska köra.
   * main.py – det här är exempel program koden som gör följande:
    
        * Läser in appinställningar.
        * Anropar live video analys på IoT Edge modulens direkta metoder för att skapa topologi, instansiera grafen och aktivera grafen.
        * Pausar så att du kan granska diagrammets utdata i **terminalfönstret** och de händelser som skickas till IoT Hub i fönstret **utdata** .
        * Inaktiverar graf-instansen, tar bort graf-instansen och tar bort diagram sto pol Ogin.
