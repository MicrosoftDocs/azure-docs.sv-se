---
title: Kopiera eller klona en data fabrik i Azure Data Factory
description: Lär dig hur du kopierar eller klonar en data fabrik i Azure Data Factory
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1ea16785502ce8a82087b79dd8e7f0014c0059fc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375246"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiera eller klona en data fabrik i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs hur du kopierar eller klonar en data fabrik i Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Använda fall för kloning av en data fabrik

Här följer några situationer där det kan vara användbart att kopiera eller klona en data fabrik:

- **Flytta data Factory** till en ny region. Om du vill flytta Data Factory till en annan region är det bästa sättet att skapa en kopia i mål regionen och ta bort den befintliga.

- **Byta namn på Data Factory**. Azure har inte stöd för att byta namn på resurser. Om du vill byta namn på en data fabrik kan du klona data fabriken med ett annat namn och ta bort den befintliga.

- **Fel sökning av ändringar** när fel söknings funktionerna inte räcker. I de flesta fall kan du använda [fel sökning](iterative-development-debugging.md). I andra testerar vi ändringar i en klonad sandbox-miljö. Om du till exempel vill att din parameterstyrda ETL-pipeline ska bete sig när en utlösare utlöses vid en fil införsel jämfört med rullande tidsfönstret, kan det hända att det inte är enkelt att testable via fel sökning. I dessa fall kanske du vill klona en sandbox-miljö för experimentering. Eftersom Azure Data Factory avgifter främst för antalet körningar leder en andra fabrik inte till några ytterligare kostnader.

## <a name="how-to-clone-a-data-factory"></a>Så här klonar du en data fabrik

1. Som en förutsättning måste du först skapa mål data fabriken från Azure Portal.

1. Om du är i GIT-läge:
    1. Varje gång du publicerar från portalen sparas fabriks resurs hanterarens mall i GIT i filen för ADF- \_ publicering
    1. Anslut den nya fabriken till _samma_ lagrings plats och bygg från ADF \_ Publish-gren. Resurser, till exempel pipelines, data uppsättningar och utlösare, kommer att utföras

1. Om du är i Live-läge:
    1. Med Data Factory-gränssnittet kan du exportera hela nytto lasten för din data fabrik till en Resource Manager-mallfil och en parameter fil. De kan nås från **arm-mallen \ exportera Resource Manager-mal len** i portalen.
    1. Du kan göra lämpliga ändringar i parameter filen och byta till nya värden för den nya fabriken
    1. Sedan kan du distribuera det via standard metoder för distribution av Resource Manager-mallar.

1. Om du har en SelfHosted-IntegrationRuntime i din käll fabrik måste du förskapa den med samma namn i mål fabriken. Om du vill dela SelfHosted-Integration Runtime mellan olika fabriker kan du använda det mönster som publicerats [här](create-shared-self-hosted-integration-runtime-powershell.md) vid delning av SelfHosted IR.

1. Av säkerhets skäl innehåller den genererade Resource Manager-mallen ingen hemlig information, till exempel lösen ord för länkade tjänster. Därför måste du ange autentiseringsuppgifterna som distributions parametrar. Om du inte vill att autentiseringsuppgifter manuellt är önskvärt för dina inställningar bör du överväga att hämta anslutnings strängarna och lösen orden från Azure Key Vault i stället. [Mer information](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Nästa steg

Granska vägledningen för att skapa en data fabrik i Azure Portal i [skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](quickstart-create-data-factory-portal.md).
