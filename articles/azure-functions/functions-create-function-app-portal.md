---
title: Skapa en funktionsapp från Azure Portal
description: Skapa en ny function-app i Azure från portalen.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 8d19a269903de309bf219c2546fa70c3abe7be10
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093598"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Skapa en funktionsapp från Azure Portal

Det här avsnittet visar hur du använder Azure Functions för att skapa en Function-app i Azure Portal. En funktionsapp är en container som är värd för körningen av enskilda funktioner. 

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

När du har skapat funktionsappen kan du skapa enskilda funktioner på ett eller flera olika språk. Skapa funktioner [med hjälp av portalen](functions-create-first-azure-function.md#create-function), [kontinuerlig distribution](functions-continuous-deployment.md) eller [överföring med FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Tjänstplaner

Azure Functions har tre olika Service planer: förbruknings plan, Premium plan och dedikerade (App Service) plan. Du måste välja din tjänst plan när din Function-app skapas och kan sedan inte ändras. Mer information finns i [Choose an Azure Functions hosting plan](functions-scale.md) (Välja en värdplan för Azure Functions).

Om du planerar att köra JavaScript-funktioner på en dedikerad (App Service) plan bör du välja en plan med färre kärnor. Mer information finns i [JavaScript-referensen för Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Krav för lagringskonto

När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring. Functions använder internt Storage för åtgärder som att hantera utlösare och logga funktionskörningar. Vissa lagringskonton stöder inte köer och tabeller, till exempel konton med endast bloblagring, Azure Premium Storage och allmänna lagringskonton med ZRS-replikering. 

Konton av en typ som inte stöds filtreras bort när du skapar en Function-app i Azure Portal. Portalen gör det också möjligt att använda ett befintligt lagrings konto när kontot finns i samma region som den Function-app som du skapar. Om du av någon anledning vill kränka prestanda bästa praxis för att ha det lagrings konto som används av din Function-app i samma region, måste du skapa din Function-app utanför portalen. 

>[!NOTE]
>När du använder förbrukningsvärdplanen lagras funktionskoden och bindningskonfigurationsfilerna i Azure File Storage i huvudlagringskontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas. 

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#core-storage-services). 

## <a name="next-steps"></a>Nästa steg

Medan Azure Portal gör det enkelt att skapa och testa funktioner, rekommenderar vi [lokal utveckling](functions-develop-local.md). När du har skapat en Function-app i portalen måste du fortfarande lägga till en funktion. 

> [!div class="nextstepaction"]
> [Lägg till en HTTP-utlöst funktion](functions-create-first-azure-function.md#create-function)
