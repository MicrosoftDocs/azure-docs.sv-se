---
title: Kräv MFA från ej betrodda nätverk – Azure Active Directory
description: Lär dig hur du konfigurerar en princip för villkorlig åtkomst i Azure Active Directory (Azure AD) till för åtkomst försök från ej betrodda nätverk.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c020a9be7683bf045dbcc747dad3cb45058dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077684"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Gör så här: Kräv MFA för åtkomst från ej betrodda nätverk med villkorlig åtkomst   

Azure Active Directory (Azure AD) möjliggör enkel inloggning till enheter, appar och tjänster var som helst. Dina användare kan komma åt dina molnappar, inte bara från din organisations nätverk, utan även från en obetrodd Internet-plats. En vanlig metod för åtkomst från ej betrodda nätverk är att kräva multifaktorautentisering (MFA).

Den här artikeln innehåller den information du behöver för att konfigurera en princip för villkorlig åtkomst som kräver MFA för åtkomst från ej betrodda nätverk. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med de [grundläggande begreppen](overview.md) för villkorlig åtkomst. 

## <a name="scenario-description"></a>Scenariobeskrivning

För att kunna hantera balansen mellan säkerhet och produktivitet kan det vara tillräckligt för att du bara behöver ett lösen ord för inloggningar från din organisations nätverk. För åtkomst från en obetrodd nätverks plats finns det dock en ökad risk att inloggningar inte utförs av legitima användare. För att åtgärda detta kan du blockera åtkomst från ej betrodda nätverk. Du kan också kräva Multi-Factor Authentication (MFA) för att få tillbaka ytterligare garantier att ett försök har gjorts av kontots legitima ägare. 

Med villkorlig åtkomst i Azure AD kan du hantera det här kravet med en enda princip som ger åtkomst: 

- Till valda molnappar
- För valda användare och grupper  
- Kräver Multi-Factor Authentication 
- När åtkomsten härstammar från: 
   - En plats som inte är betrodd

## <a name="implementation"></a>Implementering

Utmaningen med det här scenariot är att översätta *åtkomst från en icke betrodd nätverks plats* till ett villkor för villkorlig åtkomst. I en princip för villkorlig åtkomst kan du konfigurera [plats villkoret](location-condition.md) för att hantera scenarier som är relaterade till nätverks platser. Med villkoret platser kan du välja namngivna platser, som är logiska grupperingar av IP-adressintervall, länder och regioner.  

Organisationen äger vanligt vis ett eller flera adress intervall, till exempel 199.30.16.0-199.30.16.15.
Du kan konfigurera en namngiven plats genom att:

- Ange det här intervallet (199.30.16.0/28) 
- Tilldela ett beskrivande namn, till exempel **företags nätverk** 

I stället för att försöka definiera vilka platser som inte är betrodda kan du:

- Ta med valfri plats 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="Skärm bild av fönstret Azure A D-platser, med konfigurera Ställ in på Ja, fliken inkludera visas och alternativet alla platser markerat och markerat." border="false":::

- Undanta alla betrodda platser 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="Skärm bild av fönstret Azure A D-platser, med konfigurera Ställ in på Ja, fliken Uteslut visas och alternativet alla betrodda platser har valts." border="false":::

## <a name="policy-deployment"></a>Distribution av princip

Med den metod som beskrivs i den här artikeln kan du nu konfigurera en princip för villkorlig åtkomst för ej betrodda platser. För att se till att principen fungerar som förväntat, är den rekommenderade rekommenderade metoden att testa den innan den distribueras till produktion. Vi rekommenderar att du använder en test klient för att kontrol lera om den nya principen fungerar som den ska.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om villkorlig åtkomst, se [Vad är villkorlig åtkomst i Azure Active Directory?](./overview.md)
