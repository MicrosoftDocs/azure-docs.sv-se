---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "68249923"
---
Ett Azure AD-resurs-ID anger mål gruppen som en token som utfärdas kan användas för att ge åtkomst till en Azure-resurs. Om det är Azure Storage kan resurs-ID: t vara särskilt för ett enda lagrings konto, eller så kan det gälla för alla lagrings konton. I följande tabell beskrivs de värden som du kan ange för resurs-ID:

|Resurs-ID  |Beskrivning  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Tjänstens slut punkt för ett angivet lagrings konto. Använd det här värdet för att hämta en token för att auktorisera begär anden till just Azure Storage konto och tjänster. Ersätt värdet inom hakparenteser med namnet på ditt lagrings konto.      |
|`https://storage.azure.com/`     | Används för att hämta en token för att auktorisera begär anden till ett Azure Storage konto.        |
