---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: f783245b17a880a67b62aa24a3feabb9b113053b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122263"
---
## <a name="prepare-your-repository"></a>Förbered din lagrings plats

Om du vill hämta automatiserade versioner från Azure App Service build Server kontrollerar du att din lagrings plats rot har rätt filer i projektet.

| Körning | Rot Katalog-filer |
|-|-|
| ASP.NET (endast Windows) | _*. SLN_, _*. CSPROJ_ eller _default. aspx_ |
| ASP.NET Core | _*. SLN_ eller _*. CSPROJ_ |
| PHP | _index. php_ |
| Ruby (endast Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ eller _package.jspå_ med ett start skript |
| Python | _\* . py_, _requirements.txt_ eller _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default. asp_, _index.htm_, _index.html_ eller _iisstart.htm_ |
| WebJobs | _\<job_name>/Run.\<extension>_ under _AppData \_ /jobb/kontinuerligt_ för kontinuerliga Webbjobb eller _AppData/ \_ jobb/utlöses_ för utlösta WebJobs. Mer information finns i [kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Se [kontinuerlig distribution för Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Om du vill anpassa distributionen inkluderar du en *. distributions* fil i lagrings platsens rot. Mer information finns i [Anpassa distributioner](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) och [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Om du utvecklar i Visual Studio [skapar du en lagrings plats för Visual Studio](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio&preserve-view=true). Projektet är omedelbart klart att distribueras med hjälp av git.
>

