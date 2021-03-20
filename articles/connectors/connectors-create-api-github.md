---
title: Åtkomst, övervaka och hantera dina GitHub-lagrings platsen
description: Övervaka GitHub-händelser och hantera dina GitHub-lagrings platsen genom att skapa automatiserade arbets flöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999559"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Övervaka och hantera din GitHub-lagrings platsen med hjälp av Azure Logic Apps

GitHub är en webbaserad värd tjänst för git-lagring som erbjuder alla funktioner för distribuerad revision och SCM (hantering av käll kods hantering) i git och andra funktioner.

Kom igång med GitHub-anslutningen genom att [först skapa en Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Skapa en anslutning till GitHub

Om du vill använda GitHub-anslutningen i en Logic-app måste du först skapa en *anslutning* och sedan ange information om de här egenskaperna: 

| Egenskap | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| Token | Ja | Ange dina GitHub-autentiseringsuppgifter. |

När du har skapat anslutningen kan du köra åtgärderna och lyssna efter de utlösare som beskrivs i den här artikeln.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i [kopplingens referens sida](/connectors/github/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)