---
title: Hämta användarnamn och lösenord för att ansluta till data Arc Data Controller
description: Hämta användarnamn och lösenord för att ansluta till data Arc Data Controller
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8cabb48e3620f1a17d1bb9b87e1646ce2793143b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91761710"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Hämta användarnamn och lösenord för att ansluta till data Arc Data Controller

Du kanske är i en situation där du behöver hämta användar namn och lösen ord för datakontrollanten. Detta är de kommandon du behöver när du kör. 

```console
azdata login
```

Om du är Kubernetes-administratör för klustret. Så att du har behörighet att köra kommandon för att hämta från Kubernetes-hemligheten lagrar den information som Azure-bågen sparar där.

> [!NOTE]
>  Om du har använt ett annat namn för namn området där datakontrollanten skapades, måste du ändra `-n arc` parametern i kommandona nedan för att använda namnet på det namn område som du skapade data styrenheten till.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Kör följande kommando för att hämta användar namnet:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Kör följande kommando för att hämta lösen ordet:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Kör följande kommando för att hämta användar namnet:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Kör följande kommando för att hämta lösen ordet:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Nästa steg

Prova andra [scenarier](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)
