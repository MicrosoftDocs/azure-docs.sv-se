---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187947"
---
Om python påträffar ett fel när programmet startas, returneras bara en enkel felsida (t. ex. "det går inte att visa sidan eftersom ett internt Server fel har inträffat.").

Så här hämtar du python-program fel:

1. I den Azure Portal väljer du **Inställningar** i din webbapp.
2. På fliken **Inställningar** väljer du **program inställningar**.
3. Under **appinställningar** anger du följande nyckel/värde-par:
    * Nyckel: WSGI_LOG
    * Värde: D:\home\site\wwwroot\logs.txt (ange ditt val av fil namn)

Du bör nu se fel i logs.txt-filen i mappen wwwroot.
