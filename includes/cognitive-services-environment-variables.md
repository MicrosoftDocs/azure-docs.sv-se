---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 95bd83575809f6ecda716ff751b47b7bb499cae3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85073378"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurera en miljö variabel för autentisering

Program måste autentisera åtkomst till de Cognitive Services de använder. För att autentisera, rekommenderar vi att du skapar en miljö variabel för att lagra nycklarna för dina Azure-resurser. 

När du har nyckeln kan du skriva den till en ny miljö variabel på den lokala datorn som kör programmet. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `your-key` med en av nycklarna för din resurs.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

När du har lagt till miljövariabeln kan du behöva starta om alla program som körs och som behöver läsa in miljövariabeln, däribland konsolfönstret. Om du till exempel använder Visual Studio som redigerings program måste du starta om Visual Studio innan du kör exemplet.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera din .bash_profile och lägg till miljövariabeln:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

När du har lagt till miljövariabeln så kör `source ~/.bash_profile` från konsolfönstret så att ändringarna träder i kraft.

***
