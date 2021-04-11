---
title: Uppdatera autentiseringsuppgifter för tjänstens huvudnamn
description: Uppdatera autentiseringsuppgifter för ett huvud namn för tjänsten
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669550"
---
# <a name="update-service-principal-credentials"></a>Uppdatera autentiseringsuppgifter för tjänstens huvudnamn

När autentiseringsuppgifterna för tjänstens huvud namn ändras måste du uppdatera hemligheterna i data styrenheten.

Om du till exempel har distribuerat datakontrollanten med en angiven uppsättning värden för tjänstens huvud namn för klient-ID, klient-ID och klient hemlighet och sedan ändrar ett eller flera av dessa värden måste du uppdatera hemligheterna i data styrenheten.  Nedan följer instruktioner för att uppdatera klient-ID, klient-ID eller klient hemlighet. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Bakgrund

Tjänstens huvud namn skapades i [create service huvud namn](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

## <a name="steps"></a>Steg

1. Få åtkomst till tjänstens huvud namns hemlighet i standard redigeraren.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Om du till exempel vill redigera hemligheten för tjänstens huvud namn till en datakontrollant i `arc` namn området kör du följande kommando:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit`Kommandot öppnar filen credentials. yml i standard redigeraren. 


1. Redigera hemligheten för tjänstens huvud namn. 

   I standard redigeraren ersätter du värdena i avsnittet data med uppdaterad autentiseringsinformation.

   Till exempel:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Redigera värdena för `clientID` `clientSecret` och/eller `tenantID` efter behov. 

> [!NOTE]
>Värdena måste vara base64-kodade. Redigera inte några andra egenskaper.

Om ett felaktigt värde har angetts för `clientId` `clientSecret` eller visas `tenantID` ett fel meddelande på följande sätt i `control-xxxx` Pod/Controller-behållar loggarna:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Nästa steg

[Hämta användarnamn och lösenord för att ansluta till data Arc Data Controller](retrieve-the-username-password-for-data-controller.md)

[Skapa tjänstens huvudnamn](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
