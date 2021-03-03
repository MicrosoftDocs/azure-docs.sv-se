---
title: Logga in på LUIS-portalen
description: Om du är en ny användare som loggar in på LUIS-portalen, kommer inloggningen att skilja sig något beroende på ditt aktuella användar konto.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706783"
---
# <a name="sign-in-to-luis-portal"></a>Logga in på LUIS-portalen

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Använd den här artikeln för att komma igång med LUIS-portalen och skapa en redigerings resurs. När du har slutfört stegen i den här artikeln kommer du att kunna skapa och publicera LUIS-appar.

## <a name="access-the-portal"></a>Åtkomst till portalen


1. Kom igång med LUIS genom att gå till [Luis-portalen](https://www.luis.ai). Om du inte redan har en prenumeration uppmanas du att sätta igång med att skapa ett [kostnads fritt konto](https://azure.microsoft.com//free/cognitive-services/) och tillbaka till portalen.
2. Uppdatera sidan för att uppdatera den med din nyligen skapade prenumeration
3. Välj din prenumeration i list rutan

    > [!div class="mx-imgBorder"]
    > ![Välj prenumerationer](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Om din prenumeration är under en annan klient kan du inte byta innehavare från det befintliga fönstret. Du kan byta innehavare genom att stänga det här fönstret och välja den till höger avatar som innehåller dina initialer i det översta fältet. Klicka på **Välj en annan redigerings resurs** längst upp för att öppna fönstret igen.

    > [!div class="mx-imgBorder"]
    > ![Växla kataloger](./media/migrate-authoring-key/switch-directories.png)

5. Om du har en befintlig LUIS Authoring-resurs som är associerad med din prenumeration, väljer du den i list rutan. Du kan visa alla program som skapas under den här redigerings resursen.
6. Om inte, klickar du på **skapa en ny redigerings resurs** längst ned i den här spärren.
7.  När du skapar en ny redigerings resurs anger du följande information:

    > [!div class="mx-imgBorder"]
    > ![Skapa ny resurs](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Klient** organisations namn – klienten som din Azure-prenumeration är associerad med. Du kommer inte att kunna växla klienter från det befintliga fönstret. Du kan byta innehavare genom att stänga det här fönstret och välja avataren i det övre högra hörnet på skärmen, som innehåller dina initialer. Välj **Välj en annan redigerings resurs** längst upp för att öppna fönstret igen.
    * **Namn på Azure-resurs grupp** – ett anpassat resurs grupps namn som du väljer i din prenumeration. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering. Om du för närvarande inte har en resurs grupp i din prenumeration kommer du inte att kunna skapa en i LUIS-portalen. Gå till [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) för att skapa en och gå sedan till Luis för att fortsätta inloggnings processen.
    * **Azure-resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för dina redigerings transaktioner. Resurs namnet får bara innehålla alfanumeriska tecken, `-` och får inte börja eller sluta med `-` . Om något annat kortnamn ingår i namnet kommer det inte att gå att skapa en resurs.
    * **Plats** – Välj att redigera dina program på någon av de [tre redigerings platser](./luis-reference-regions.md) som för närvarande stöds av Luis, inklusive: västra USA, Västeuropa och östra Australien
    * **Pris nivå** – som standard är pris nivån för F0-redigering valt som rekommenderas. Skapa en [kundhanterad nyckel](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding) från Azure Portal om du letar efter ett extra säkerhets lager.
8. Nu har du loggat in på LUIS. Nu kan du börja skapa program.

## <a name="troubleshooting"></a>Felsökning

* När du skapar en ny resurs måste du se till att resurs namnet endast innehåller alfanumeriska tecken,-och får inte börja eller sluta med-. Annars fungerar det inte.
* Kontrol lera att du har [rätt behörigheter för din prenumeration för att skapa en Azure-resurs](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Om du inte har rätt behörighet kan du kontakta prenumerationens administratör för att ge dig tillräckliga behörigheter.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [startar en ny app](luis-how-to-start-new-app.md)