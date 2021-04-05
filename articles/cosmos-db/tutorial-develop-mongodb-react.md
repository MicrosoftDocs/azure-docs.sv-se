---
title: Självstudier om MongoDB, React och Node.js för Azure
description: Lär dig hur du skapar en MongoDB-app med React och Node.js i Azure Cosmos DB med hjälp av exakt samma API:er som du använder för MongoDB i den här videobaserade självstudieserien.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jopapa
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 1425b89e42450123c1696ddcee4458e1f69b8a6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348576"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Skapa en MongoDB-app med React och Azure Cosmos DB  
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här videobaserade självstudiekursen i flera dela demonstrerar hur du skapar en hero-spårningsapp med en React-klient. Appen använder Node och Express för servern, ansluter till Cosmos-databasen konfigurerad med [Azure Cosmos DB:s API för MongoDB](mongodb-introduction.md) och ansluter sedan React-klientdelen till appens serverdel. I den här självstudien lär du dig även hur du skalar Cosmos DB på Azure-portalen genom att peka och klicka samt hur du distribuerar appen till Internet så att alla kan spåra sina heroes-favoritkomponenter. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stöder kompatibilitet för trådprotokoll med MongoDB, vilket gör att klienter kan använda Azure Cosmos DB i stället för MongoDB.  

Den här självstudiekursen i flera delar består av följande uppgifter:

> [!div class="checklist"]
> * Introduktion
> * Konfigurera projektet
> * Skapa användargränssnittet med React
> * Skapa ett Azure Cosmos DB-konto med hjälp av Azure-portalen
> * Ansluta till Azure Cosmos DB med hjälp av Mongoose
> * Lägga till React-, Create-, Update- och Delete-åtgärder till appen

Vill du skapa den här appen med Angular? Gå [den videobaserade självstudieserien för Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Förutsättningar
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Det färdiga projektet
Hämta det färdiga programmet [från GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Introduktion 

I den här videon ger Burke Holland en introduktion av Azure Cosmos DB och går igenom appen som skapas i den här videoserien. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Projektkonfiguration

Den här videon visar hur du konfigurerar Express och React i samma projekt. Därefter går Burke igenom koden i projektet.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Skapa användargränssnittet

Den här videon visar hur du skapar programmets användargränssnitt (UI) med React. 

> [!NOTE]
> CSS-koden som vi refererar till i den här videon finns i [GitHub-databasen react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Ansluta till Azure Cosmos DB

Den här videon visar hur du skapar ett nytt Azure Cosmos DB-konto på Azure-portalen och hur du installerar MongoDB- och Mongoose-paketen och sedan ansluter appen till det nya kontot med hjälp av Azure Cosmos DB-anslutningssträngen. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Läsa och skapa heroes-komponenter i appen

Den här videon visar hur du läser hjältar och skapar hjältar i Cosmos-databasen, samt hur du testar dessa metoder med Postman och det reagera användar gränssnittet. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Ta bort och uppdatera heroes-komponenter i appen

Den här videon visar hur du tar bort och uppdaterar heroes-komponenter från appen och hur du visar uppdateringarna i användargränssnittet. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Slutföra appen

Den här videon visar hur du slutför appen och hur du kopplar användargränssnittet till API:et för serverdelen. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda den här appen mer följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudiekursen på Azure-portalen. 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att:

> [!div class="checklist"]
> * Skapa en app med React, Node, Express och Azure Cosmos DB 
> * Skapa ett Azure Cosmos DB-konto
> * Ansluta appen till Azure Cosmos DB-kontot
> * Testa appen med hjälp av Postman
> * Köra programmet och lägga till heroes-komponenter i databasen

Fortsätt med nästa självstudiekurs och lär dig hur du importerar MongoDB-data till Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
