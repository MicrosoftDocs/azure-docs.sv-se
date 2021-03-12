---
title: Registrera en resurs-app i Azure AD – Azure API för FHIR
description: Registrera en resurs-eller API-app i Azure Active Directory så att klient program kan begära åtkomst till resursen vid autentisering.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020089"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrera ett resurs program i Azure Active Directory

I den här artikeln får du lära dig hur du registrerar ett resurs-eller API-program i Azure Active Directory. Ett resurs program är en Azure Active Directory representation av själva FHIR-serverns API och klient program kan begära åtkomst till resursen vid autentisering. Resurs programmet kallas även för *mål gruppen* i OAuth-parlance.

## <a name="azure-api-for-fhir"></a>Azure API för FHIR

Om du använder Azure-API: et för FHIR skapas ett resurs program automatiskt när du distribuerar tjänsten. Så länge du använder Azure-API: et för FHIR i samma Azure Active Directory klient organisation som du distribuerar ditt program, kan du hoppa över den här instruktions guiden och distribuera ditt Azure-API för FHIR för att komma igång.

Om du använder en annan Azure Active Directory klient (som inte är associerad med din prenumeration) kan du importera Azure-API: et för FHIR resurs program till din klient med PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

eller så kan du använda Azure CLI:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR Server för Azure

Om du använder FHIR-servern med öppen källkod för Azure kan du registrera ett resurs program genom att följa stegen i [GitHub-lagrings platsen](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) . 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett resurs program i Azure Active Directory. Registrera sedan ditt konfidentiella klient program.
 
>[!div class="nextstepaction"]
>[Registrera konfidentiellt klient program](register-confidential-azure-ad-client-app.md)