---
author: baanders
description: inkludera fil för att konfigurera lokal autentisering för DefaultAzureCredential i Azure Digitals dubbla exempel – utan Intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212830"
---
Med `DefaultAzureCredential` söker exemplet efter autentiseringsuppgifter i din lokala miljö, t. ex. en Azure-inloggning i ett lokalt [Azure CLI](/cli/azure/install-azure-cli) eller i Visual Studio eller Visual Studio Code. Därför bör du *Logga in på Azure lokalt* via någon av dessa metoder för att ställa in autentiseringsuppgifter för exemplet.

Om du använder Visual Studio eller Visual Studio Code för att köra kod exemplet måste du kontrol lera att du är inloggad i redigeraren med samma Azure-autentiseringsuppgifter som du vill använda för att få åtkomst till Azure Digitals-instansen.

Annars kan du [installera den lokala Azure CLI](/cli/azure/install-azure-cli), starta en kommando tolk på datorn och köra `az login` kommandot för att logga in på ditt Azure-konto. När du har loggat in när du kör kod exemplet ska du logga in automatiskt.