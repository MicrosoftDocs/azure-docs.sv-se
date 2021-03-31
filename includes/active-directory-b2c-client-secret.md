---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f227021f6cd71686eb58b43dc16a03d6fc010b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84672548"
---
#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/) 

1. Välj **Certifikat och hemligheter** under **Hantera**.
1. Välj **Ny klienthemlighet**.
1. Ange en beskrivning av klient hemligheten i rutan **Beskrivning** . Till exempel *clientsecret1*.
1. Under **upphör ande** väljer du en varaktighet för vilken hemligheten är giltig och väljer sedan **Lägg till**.
1. Registrera hemlighetens **värde**. Du använder det här värdet för konfiguration i ett senare steg.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Under **API-åtkomst** väljer du **nycklar**.
1. Ange en beskrivning av nyckeln i rutan **nyckel Beskrivning** . Till exempel *clientsecret1*.
1. Välj en giltighets **tid** och välj sedan **Spara**.
1. Registrera nyckelns **värde**. Du använder det här värdet för konfiguration i ett senare steg.