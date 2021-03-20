---
title: Visa tjänstens huvud namn för en hanterad identitet – Azure CLI – Azure AD
description: Stegvisa instruktioner för att Visa tjänstens huvud namn för en hanterad identitet med Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92892051"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Visa tjänstens huvud namn för en hanterad identitet med Azure CLI

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du visar tjänstens huvud namn för en hanterad identitet med hjälp av Azure CLI.

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser, se [Vad är hanterade identiteter för Azure-resurser?](overview.md).

- Aktivera [systemtilldelad identitet på en virtuell dator eller ett](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [program](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>Visa tjänstens huvud namn

Följande kommando visar hur du visar tjänstens huvud namn för en virtuell dator eller ett program med hanterad identitet aktive rad. Ersätt `<Azure resource name>` med dina egna värden.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Azure AD-tjänstens huvud namn med Azure CLI finns i [AZ AD SP](/cli/azure/ad/sp).
