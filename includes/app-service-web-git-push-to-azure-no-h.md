---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102205990"
---
I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt *\<deploymentLocalGitUrl-from-create-step>* med URL: en för den git-fjärrserver som du sparade från [skapa en webbapp](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När git Credential Manager uppmanas att ange autentiseringsuppgifter, se till att du anger de autentiseringsuppgifter som du skapade i **Konfigurera en distributions användare**, inte de autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```bash
git push azure main
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
