---
title: Använda Azure Portal för att distribuera Service Catalog-appen
description: Visar konsumenter av hanterade program hur du distribuerar en tjänst katalog app via Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81391712"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Snabb start: Distribuera tjänst katalog app via Azure Portal

I [föregående snabb start](publish-service-catalog-app.md)publicerade du en definition för hanterade program. I den här snabb starten skapar du en tjänst katalog app från den definitionen.

## <a name="create-service-catalog-app"></a>Skapa tjänst katalog program

Använd följande steg i Azure Portal:

1. Välj **Skapa en resurs**.

   ![Skapa en resurs](./media/deploy-service-catalog-quickstart/create-new.png)

1. Sök efter **hanterat program för tjänst katalog** och välj det bland de tillgängliga alternativen.

   ![Sök efter tjänst katalog program](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Du ser en beskrivning av tjänsten för hanterade program. Välj **Skapa**.

   ![Välj Skapa](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Portalen visar de definitioner av hanterade program som du har åtkomst till. Välj den som du vill distribuera från tillgängliga definitioner. I den här snabb starten använder du den **hanterade lagrings konto** definition som du skapade i föregående snabb start. Välj **Skapa**.

   ![Välj den definition som ska distribueras](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Ange värden för fliken **grundläggande** . Välj den Azure-prenumeration som du vill distribuera Service Catalog-appen till. Skapa en ny resurs grupp med namnet **variabeln applicationgroup**. Välj en plats för din app. När du är klar väljer du **OK**.

   ![Ange värden för Basic](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Ange ett prefix för lagrings konto namnet. Välj den typ av lagrings konto som ska skapas. När du är klar väljer du **OK**.

   ![Ange värden för lagring](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Granska sammanfattningen. När verifieringen har slutförts väljer du **OK** för att starta distributionen.

   ![Visa Sammanfattning](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Visa resultat

När du har distribuerat tjänst katalogs appen har du två nya resurs grupper. En resurs grupp innehåller tjänst katalogens app. Den andra resurs gruppen innehåller resurserna för tjänst katalog appen.

1. Visa resurs gruppen med namnet **variabeln applicationgroup** för att se appen för tjänst katalogen.

   ![Visa program](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Visa resurs gruppen med namnet **variabeln applicationgroup {hash-characters}** om du vill se resurserna för tjänst katalog programmet.

   ![Visa resurser](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar definitionsfiler för ett hanterat program finns i [skapa och publicera en definition för hanterade program](publish-service-catalog-app.md).
* Information om Azure CLI finns i [distribuera tjänst katalog program med Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* För PowerShell, se [distribuera tjänst katalog program med PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
