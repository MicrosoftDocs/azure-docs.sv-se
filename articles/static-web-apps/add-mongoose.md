---
title: 'Självstudie: få åtkomst till data i Cosmos DB att använda Mongoose med Azure static Web Apps'
description: Lär dig att komma åt data i Cosmos DB att använda Mongoose från en statisk Azure-Web Apps API-funktion.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260070"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Självstudie: få åtkomst till data i Cosmos DB att använda Mongoose med Azure static Web Apps

[Mongoose](https://mongoosejs.com/) är den populäraste ODM-klienten (objekt dokument mappning) för Node.js. Genom att låta dig utforma en data struktur och tillämpa verifiering, innehåller Mongoose alla verktyg som behövs för att interagera med databaser som stöder Mongoose-API: et. [Cosmos DB](../cosmos-db/mongodb-introduction.md) stöder de nödvändiga Mongoose-API: erna och är tillgängliga som server dels Server alternativ på Azure.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> - Skapa ett Cosmos DB server lös konto
> - Skapa en statisk Azure-Web Apps
> - Uppdatera program inställningarna för att lagra anslutnings strängen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- Ett [Azure-konto](https://azure.microsoft.com/free/)
- Ett [GitHub-konto](https://github.com/join)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Skapa en Cosmos DB server lös databas

Börja med att skapa ett [Cosmos DB server](https://docs.microsoft.com/azure/cosmos-db/serverless) lös konto. Genom att använda ett Server lösa konto betalar du bara för resurserna när de används och behöver inte skapa en fullständig infrastruktur.

1. Navigera till [https://portal.azure.com](https://portal.azure.com)
2. Klicka på **skapa en resurs**
3. Ange **Azure Cosmos DB** i sökrutan
4. Klicka på **Azure Cosmos DB**
5. Klicka på **Skapa**
6. Konfigurera ditt Azure Cosmos DB-konto med följande information
    - Prenumeration: Välj den prenumeration som du vill använda
    - Resurs: Klicka på **Skapa ny** och ange namnet till **aswa-Mongoose**
    - Konto namn: ett unikt värde måste anges
    - API: **Azure Cosmos dB för MongoDB-API**
    - Antecknings böcker (förhands granskning): **av**
    - Plats: **västra USA 2**
    - Kapacitets läge: utan **Server (för hands version)**
    - Version: **3,6**
    - Tillgänglighetszoner: **inaktivera** 
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="Skapa ny Cosmos DB instans":::
7. Klicka på **Granska + skapa**
8. Klicka på **Skapa**

Det tar några minuter att skapa processen. Senare steg kommer att gå tillbaka till databasen för att samla in anslutnings strängen.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

I den här självstudien används en mall för GitHub-mallar som hjälper dig att skapa ditt program.

1. Navigera till [Start mal len](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. Välj **ägare** (om du använder en annan organisation än ditt huvud konto)
3. Namnge din databas **aswa – Mongoose – självstudier**
4. Klicka på **skapa databas från mall**
5. Gå tillbaka till [Azure Portal](https://portal.azure.com)
6. Klicka på **skapa en resurs**
7. Skriv **statiska webb program** i sökrutan
8. Välj **statisk webbapp (förhands granskning)**
9. Klicka på **Skapa**
10. Konfigurera Azures statiska webbapp med följande information
    - Prenumeration: Välj samma prenumeration som tidigare
    - Resurs grupp: Välj **aswa-Mongoose**
    - Namn: **aswa-Mongoose – självstudier**
    - Region: **USA, västra 2**
    - Klicka på **Logga in med GitHub**
    - Klicka på **auktorisera** om du uppmanas att tillåta Azure static Web Apps att skapa GitHub-åtgärden för att aktivera distribution
    - Organisation: ditt konto namn
    - Databas: **aswa – Mongoose – självstudier**
    - Gren: **main**
    - Versions för inställningar: Välj **anpassad**
    - App-plats: **/Public**
    - API-plats: **API**
    - Plats för app-artefakt: *lämna tomt* 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="slutförd Azure-Web Apps formulär":::
11. Klicka på **Granska och skapa**
12. Klicka på **Skapa**

Det tar några minuter att skapa processen, men du kan klicka på **gå till resurs** när appen har tillhandahållits.

## <a name="configure-database-connection-string"></a>Konfigurera databas anslutnings sträng

För att webbappen ska kunna kommunicera med-databasen lagras databas anslutnings strängen som en program inställning. Inställning av värden är tillgängliga i Node.js med hjälp av `process.env` objektet.

1. Klicka på **Start** i det övre vänstra hörnet i Azure Portal (eller navigera tillbaka till [https://portal.azure.com](https://portal.azure.com) )
2. Klicka på **resurs grupper**
3. Klicka på **aswa-Mongoose**
4. Klicka på namnet på ditt databas konto – det kommer att ha en typ av **Azure Cosmos DB konto**
5. Under **Inställningar** klickar du på **anslutnings sträng**
6. Kopiera anslutnings strängen som visas under **primär ANSLUTNINGS sträng**
7. I spåren klickar du på **aswa-Mongoose**
8. Klicka på **aswa-Mongoose – självstudier** för att återgå till webbplats instansen
9. Under **Inställningar** klickar du på **konfiguration**
10. Klicka på **Lägg till** och skapa en ny program inställning med följande värden
    - Namn: **CONNECTION_STRING**
    - Värde: klistra in anslutnings strängen som du kopierade tidigare
11. Klicka på **OK**
12. Klicka på **Spara**

## <a name="navigate-to-your-site"></a>Navigera till din webbplats

Nu kan du utforska den statiska webbappen.

1. Klicka på **Översikt**
1. Klicka på den URL som visas i det övre högra hörnet
    1. Det ser ut ungefär så här `https://calm-pond-05fcdb.azurestaticapps.net`
1. Skapa en ny uppgift genom att skriva i en rubrik och klicka på **Lägg till aktivitet**
1. Bekräfta att uppgiften visas (det kan ta en stund)
1. Markera uppgiften som slutförd genom **att klicka på kryss rutan**
1. **Uppdatera sidan** för att bekräfta att en databas används

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort resurs gruppen med följande steg:

1. Gå tillbaka till [Azure Portal](https://portal.azure.com)
2. Klicka på **resurs grupper**
3. Klicka på **aswa-Mongoose**
4. Klicka på **ta bort resurs grupp**
5. Skriv **aswa-Mongoose** i text rutan
6. Klicka på **Ta bort**

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du konfigurerar lokal utveckling...
> [!div class="nextstepaction"]
> [Konfigurera lokal utveckling](./local-development.md)
 
