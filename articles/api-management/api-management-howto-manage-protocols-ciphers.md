---
title: Hantera protokoll och chiffrering i Azure API Management | Microsoft Docs
description: Lär dig hur du hanterar protokoll (TLS) och chiffersviter (DES) i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 043a3d0b63dfc74f587b58b3c2ac42f1a084cc4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86250319"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Hantera protokoll och chiffer i Azure API Management

Azure API Management stöder flera versioner av TLS-protokoll för både klient-och Server sidor samt 3DES-chiffer.

Den här guiden visar hur du hanterar protokoll och chiffrering av konfigurationen för en Azure API Management-instans.

![Hantera protokoll och chiffrering i APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Förutsättningar

För att följa stegen i den här artikeln måste du ha:

* En API Management-instans

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Hantera TLS-protokoll och 3DES-chiffer

1. Navigera till din **API Management-instans** i Azure Portal.
2. Välj **protokoll inställningar** på menyn.  
3. Aktivera eller inaktivera önskade protokoll eller chiffer.
4. Klicka på **Spara**. Ändringar kommer att tillämpas inom en timme.  

## <a name="next-steps"></a>Nästa steg

* Läs mer om [TLS (Transport Layer Security)](/dotnet/framework/network-programming/tls).
* Se fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
