---
title: ta med fil
description: inkludera fil för klient scenariots landnings sidor (daemon, webbapp, webb-API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996022"
---
## <a name="add-a-client-secret-or-certificate"></a>Lägg till en klient hemlighet eller certifikat

Precis som med ett konfidentiellt klient program måste du lägga till en hemlighet eller ett certifikat för att fungera som programmets *autentiseringsuppgifter* så att det kan autentisera sig självt, utan användar interaktion.

Du kan lägga till autentiseringsuppgifter till klient appens registrering med hjälp av [Azure Portal](#add-client-credentials-by-using-the-azure-portal) eller genom att använda ett kommando rads verktyg som [PowerShell](#add-client-credentials-by-using-powershell).

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Lägg till klientautentiseringsuppgifter med hjälp av Azure Portal

Om du vill lägga till autentiseringsuppgifter i ditt konfidentiella klient programs app-registrering följer du stegen i [snabb start: registrera ett program med Microsoft Identity Platform](../articles/active-directory/develop/quickstart-register-app.md) för den typ av autentiseringsuppgift som du vill lägga till:

* [Lägg till en klient hemlighet](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Lägg till ett certifikat](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Lägg till klientautentiseringsuppgifter med hjälp av PowerShell

Du kan också lägga till autentiseringsuppgifter när du registrerar ditt program med Microsoft Identity Platform med hjälp av PowerShell.

Kod exemplet [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) på GitHub visar hur du lägger till en program hemlighet eller ett certifikat när du registrerar ett program:

- Mer information om hur du lägger till en **klient hemlighet** med PowerShell finns i [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Mer information om hur du lägger till ett **certifikat** med PowerShell finns i [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
