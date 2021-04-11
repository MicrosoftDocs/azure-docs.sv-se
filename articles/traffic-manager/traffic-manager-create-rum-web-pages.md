---
title: Faktisk slutanvändarmätning med webb sidor – Azure Traffic Manager
description: I den här artikeln lär du dig hur du konfigurerar dina webb sidor för att skicka Faktisk slutanvändarmätning till Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580351"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Så här skickar du Faktisk slutanvändarmätning till Azure Traffic Manager med webb sidor

Du kan konfigurera dina webb sidor så att de skickar Faktisk slutanvändarmätning till Traffic Manager genom att skaffa en Faktisk slutanvändarmätning (RUM) nyckel och bädda in den genererade koden på webb sidan.

## <a name="obtain-a-real-user-measurements-key"></a>Hämta en Faktisk slutanvändarmätning nyckel

De mätningar du tar och skickar till Traffic Manager från ditt klient program identifieras av tjänsten med hjälp av en unik sträng, som kallas för **nyckeln faktisk slutanvändarmätning (rum)**. Du kan hämta en RUM nyckel med hjälp av Azure Portal, en REST API, PowerShell eller Azure CLI.

Hämta RUM-nyckeln med hjälp av Azure Portal:
1. Logga in på Azure Portal från en webbläsare. Om du inte redan har ett konto kan du registrera dig för en kostnadsfri utvärderingsmånad.

1. I portalens sökfält söker du efter namnet på Traffic Manager profilen som du vill ändra och väljer sedan Traffic Manager profilen i resultaten som visas.

1. På sidan Traffic Manager profil väljer **faktisk slutanvändarmätning** under **Inställningar**.

1. Välj **generera nyckel** för att skapa en ny rum nyckel.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Skärm bild av hur du genererar nyckel."::: 

   **Bild 1: Faktisk slutanvändarmätning nyckel skapande**

1. På sidan visas nu den RUM nyckel som skapats och ett kods tycke för JavaScript-kod som måste bäddas in på HTML-sidan.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Skärm bild av genererad JavaScript-kod."::: 

    **Bild 2: skript för att Faktisk slutanvändarmätning nyckel och mätning**
 
1. Välj **kopierings** knappen för att kopiera JavaScript-koden. 

> [!IMPORTANT]
> Använd det genererade JavaScript-kommandot för Faktisk slutanvändarmätning funktionen för att fungera korrekt. Eventuella ändringar av det här skriptet eller skripten som används av Faktisk slutanvändarmätning kan leda till oförutsägbara beteenden.

## <a name="embed-the-code-to-an-html-web-page"></a>Bädda in koden på en HTML-webbsida

När du har fått nyckeln till RUM är nästa steg att bädda in det kopierade JavaScript-kommandot på en HTML-sida som dina slutanvändare besöker. Redigera HTML kan göras på många sätt och med olika verktyg och arbets flöden. Det här exemplet visar hur du uppdaterar en HTML-sida för att lägga till det här skriptet. Du kan använda den här vägledningen för att anpassa den till ditt arbets flöde för hantering av HTML-datakälla.

1. Öppna HTML-sidan i en text redigerare.

1. Klistra in den JavaScript-kod som du kopierade i det sista avsnittet i avsnittet BRÖDTEXT i HTML-koden. Den kopierade koden finns på rad 8 & 9, se bild 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Skärm bild av genererat JavaScript-inbäddat på webb sidan."::: 

    **Bild 3: enkel HTML med Embedded Faktisk slutanvändarmätning Java Script**

1. Spara HTML-filen och placera den på en webb server som är ansluten till Internet.

1. Nästa gång den här sidan renderas i en webbläsare, hämtas JavaScript-skriptet och skriptet kör mät-och rapporterings åtgärderna.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [faktisk slutanvändarmätning](traffic-manager-rum-overview.md)
- Lär dig [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Läs mer om [metoderna för trafik-routning](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Lär dig hur du [skapar en Traffic Manager-profil](./quickstart-create-traffic-manager-profile.md)
