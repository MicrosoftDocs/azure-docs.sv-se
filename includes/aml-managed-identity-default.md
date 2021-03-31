---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89147601"
---
**Standard-hanterad identitet** är den systemtilldelade hanterade identiteten eller den första användarspecifika hanterade identiteten.

Under en körning finns det två program för en identitet:

1. Systemet använder en identitet för att ställa in användarens lagrings monteringar, behållar registret och data lager.

    * I det här fallet kommer systemet att använda standardhanterad identitet.

1. Användaren använder en identitet för att få åtkomst till resurser inifrån koden för en skickad körning

    * I det här fallet anger du *client_id* som motsvarar den hanterade identitet som du vill använda för att hämta en autentiseringsuppgift.
    * Du kan också hämta klient-ID: t för den användare som har tilldelats identiteten via *DEFAULT_IDENTITY_CLIENT_ID* -miljövariabeln.

    Till exempel för att hämta en token för ett data lager med standardhanterad identitet:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```