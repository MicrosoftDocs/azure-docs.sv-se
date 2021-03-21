---
title: Hybrid Identity design – krav för innehålls hantering Azure | Microsoft Docs
description: Ger en inblick i hur du fastställer kraven på innehålls hantering i din verksamhet. Vanligt vis när en användare har sin egen enhet kan de också ha flera autentiseringsuppgifter som är alternerande enligt det program som de använder. Det är viktigt att skilja på vilket innehåll som skapades med hjälp av personliga autentiseringsuppgifter jämfört med de som har skapats med företags uppgifter. Din identitets lösning bör kunna interagera med moln tjänster för att ge en sömlös upplevelse till slutanvändaren samtidigt som du ser till att dess integritet ökar och skyddar mot data läckage.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94408540"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Fastställ krav för innehålls hantering för din hybrid identitets lösning
Att förstå kraven på innehålls hantering för ditt företag kan leda till att ditt beslut om vilken hybrid identitets lösning som ska användas. Med spridning av flera enheter och möjligheten för användare att ta med sig sina egna enheter ([BYOD](/mem/intune/fundamentals/byod-technology-decisions)) måste företaget skydda sina egna data, men det måste också hålla användarens sekretess intakt. Vanligt vis när en användare har sin egen enhet kan de också ha flera autentiseringsuppgifter som är alternerande enligt det program som de använder. Det är viktigt att skilja på vilket innehåll som skapades med hjälp av personliga autentiseringsuppgifter jämfört med de som har skapats med företags uppgifter. Din identitets lösning bör kunna interagera med moln tjänster för att ge en sömlös upplevelse till slutanvändaren samtidigt som du ser till att dess integritet ökar och skyddar mot data läckage. 

Din identitets lösning används av olika tekniska kontroller för att tillhandahålla innehålls hantering såsom visas i bilden nedan:

![säkerhets kontroller](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Säkerhets kontroller som kommer att använda ditt identitets hanterings system**

I allmänhet kommer krav för innehålls hantering att utnyttja ditt identitets hanterings system inom följande områden:

* Sekretess: identifierar användaren som äger en resurs och tillämpar lämpliga kontroller för att upprätthålla integriteten.
* Data klassificering: identifiera användaren eller gruppen och åtkomst nivån för ett objekt enligt dess klassificering. 
* Data läckage skydd: säkerhets kontroller som ansvarar för att skydda data för att undvika läckage måste samverka med identitets systemet för att verifiera användarens identitet. Detta är också viktigt för granskning av spårnings ändamål.

> [!NOTE]
> Läs [data klassificering för moln beredskap](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) för mer information om metod tips och rikt linjer för data klassificering.
> 
> 

När du planerar din hybrid identitets lösning ser du till att följande frågor besvaras enligt organisationens krav:

* Har företaget säkerhets kontroller på plats för att genomdriva data sekretess?
  * Om ja, kommer säkerhets kontrollerna att kunna integreras med den hybrid identitets lösning som du kommer att anta?
* Använder företaget data klassificering?
  * Om ja, är den aktuella lösningen som kan integreras med den hybrid identitets lösning som du kommer att anta?
* Har företaget för närvarande någon lösning för data läckage? 
  * Om ja, är den aktuella lösningen som kan integreras med den hybrid identitets lösning som du kommer att anta?
* Behöver företaget granska åtkomst till resurser?
  * Om ja, vilken typ av resurser?
  * Om ja, vilken informations nivå som krävs?
  * Om ja, där gransknings loggen måste finnas? Lokalt eller i molnet?
* Behöver företaget kryptera alla e-postmeddelanden som innehåller känsliga data (SSNs, kreditkorts nummer osv.)?
* Behöver företaget kryptera alla dokument/innehåll som delas med externa affärs partner?
* Behöver företaget använda företags principer på vissa typer av e-postmeddelanden (svara inte alla, vidarebefordra inte)?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera data skydds strategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) går över tillgängliga alternativ och fördelar/nack delar med varje alternativ.  Genom att ha besvarat dessa frågor väljer du det alternativ som passar dina affärs behov bäst.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa åtkomstkontrollkrav](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)