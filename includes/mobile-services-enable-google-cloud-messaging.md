---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b32ff0926aa2b797bdb8dfcbb01cf845080a0ce9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93375976"
---
1. Navigera till [Google Cloud-konsolen](https://console.developers.google.com/cloud-resource-manager) och logga in med dina Google-kontouppgifter. 
2. Välj **Skapa projekt** i verktygsfältet. 
   
    ![Skapa nytt projekt](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. För **Projektnamn** anger du ett namn för projektet och klickar på **Skapa**.
4. Välj knappen **Aviseringar** i verktygsfältet och välj ditt projekt i listan. Instrumentpanelen för projektet visas. Du kan också gå direkt till instrumentpanelen genom att ange URL:en `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Välj ditt projekt i Aviseringar](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Anteckna **projektnumret** på panelen **Projektinformation** på instrumentpanelen. 

    ![Projekt-ID](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. Välj panelen **API:er** på instrumentpanelen och välj sedan **Go to APIs overview** (Gå till API-översikt). 

    ![Länk för API-översikt](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Välj **AKTIVERA API:ER OCH TJÄNSTER** på sidan **API**. 

    ![Knappen Aktivera API:er och tjänster](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Leta upp och välj **Google Cloud Messaging**. 

    ![Leta upp och välj Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Aktivera Google Cloud Messaging för projektet genom att välja **AKTIVERA**.

    ![Aktivera Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Välj **Skapa autentiseringsuppgifter** i verktygsfältet. 

    ![Knappen Skapa autentiseringsuppgifter](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Välj länken **API-nyckel** på sidan för att **lägga till autentiseringsuppgifter till ditt projekt**. 

    ![Lägg till autentiseringsuppgifter](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Välj **Skapa/spara** på sidan **API-nyckel**. I följande exempel är alternativet **IP-adresser** markerat och **0.0.0.0/0** har angetts för tillåtna IP-adresser. Du bör begränsa din API-nyckel på lämpligt sätt. 

    ![API-nyckel – knappen Skapa](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Kopiera **API-nyckeln** till Urklipp och spara den på lämplig plats. 

    ![Kopiera API-nyckeln](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Du kommer att använda API-nyckelvärdet för att autentisera med GCM och skicka push-meddelanden för din app. Gå tillbaka till instrumentpanelen för projektet genom att ange URL:en `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

