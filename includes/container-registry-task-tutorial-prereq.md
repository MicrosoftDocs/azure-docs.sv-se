---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 79e2ca71d6b8178be63c3429edba89cf3bb523e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030005"
---
## <a name="prerequisites"></a>Förutsättningar

### <a name="get-sample-code"></a>Hämta exempelkod

Självstudien förutsätter att du redan har slutfört stegen i [föregående självstudie](../articles/container-registry/container-registry-tutorial-quick-task.md), samt att du har förgrenat och klonat exempellagringsplatsen. Om du inte har gjort det, slutför du stegen i avsnittet [Krav](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) i föregående självstudie innan du fortsätter.

### <a name="container-registry"></a>Containerregister

Du måste ha ett Azure-containerregister i din Azure-prenumeration för att kunna slutföra den här självstudien. Om du behöver ett register kan du gå till [föregående självstudie](../articles/container-registry/container-registry-tutorial-quick-task.md) eller [Snabbstart: Skapa ett containerregister med hjälp av Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md).

### <a name="create-a-github-personal-access-token"></a>Skapa en personlig åtkomsttoken för GitHub

Om du vill utlösa en aktivitet för att genomföra en git-lagringsplats behöver ACR-aktiviteter en personlig åtkomsttoken (PAT) för att få åtkomst till lagrings platsen. Om du inte redan har en PAT, följer du de här stegen för att generera en i GitHub:

1. Gå till sidan där personliga åtkomsttokens skapas i GitHub på https://github.com/settings/tokens/new
1. Ange en kort **beskrivning** för din token, till exempel ”ACR Tasks Demo”
1. Välj omfång för ACR för att få åtkomst till lagrings platsen. För att få åtkomst till en offentlig lagrings platsen som i den här självstudien, under **lagrings platsen**, aktiverar du **lagrings platsen: status** och **public_repo**

   ![Skärmbild av sidan Personlig åtkomsttoken i GitHub][build-task-01-new-token]

   > [!NOTE]
   > Om du vill generera en PAT för åtkomst till en *privat* lagrings platsen väljer du omfattningen för fullständig **lagrings platsen** -kontroll.

1. Välj knappen **Generera token** (du kan behöva bekräfta lösenordet)
1. Kopiera och spara din genererade token på en **säker plats** (du använder denna token när du definierar en uppgift i följande avsnitt)

   ![Skärmbild av genererad personlig åtkomsttoken i GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
