---
title: API-krav – Azure Marketplace
description: 'Krav för att använda Cloud Partner Portal API: er.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91261166"
---
# <a name="api-prerequisites"></a>API-krav

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. Använd bara CPP-API: er för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Du behöver två nödvändiga programmerings resurser för att använda Cloud Partner Portal API: er: ett tjänst huvud namn och en Azure Active Directory (Azure AD)-åtkomsttoken.

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Skapa tjänstens huvud namn i Azure Active Directory klient organisation

Först måste du skapa ett huvud namn för tjänsten i din Azure AD-klient. Den här klienten tilldelas en egen uppsättning behörigheter i Cloud Partner Portal. Din kod anropar API: er med den här klienten i stället för dina personliga autentiseringsuppgifter. En fullständig förklaring av hur du skapar ett huvud namn för tjänsten finns i [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="add-service-principal-to-your-account"></a>Lägg till tjänstens huvud namn i ditt konto

Nu när du har skapat tjänstens huvud namn i din klient organisation kan du lägga till det som en användare till ditt partner Center Portal konto. Precis som en användare kan tjänstens huvud namn vara en ägare eller deltagare till portalen. Mer information finns i **Nästa steg** nedan.

## <a name="next-steps"></a>Nästa steg

Se [Hantera Azure AD-program](partner-center-portal/manage-account.md#manage-azure-ad-applications).
