---
title: Verktyget What If för villkorlig åtkomst – Azure Active Directory
description: Lär dig hur du kan förstå effekten av dina principer för villkorlig åtkomst i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 119540738ac2703afd79891d9bf8a4a9ff0c7472
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952770"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Felsöka med hjälp av What If-verktyget i Villkorsstyrd åtkomst

[Villkorlig åtkomst](./overview.md) är en funktion i Azure Active Directory (Azure AD) som gör att du kan styra hur auktoriserade användare får åtkomst till dina molnappar. Hur vet du vad du kan förväntar dig från principer för villkorlig åtkomst i din miljö? Om du vill besvara den här frågan kan du använda **verktyget för villkorlig åtkomst what if**.

Den här artikeln förklarar hur du kan använda det här verktyget för att testa dina principer för villkorlig åtkomst.

> [!VIDEO https://www.youtube.com/embed/M_iQVM-3C3E]

## <a name="what-it-is"></a>Vad det är

Med **verktyget för what if princip för villkorlig åtkomst** kan du förstå effekten av dina principer för villkorlig åtkomst på din miljö. I stället för att testa att köra dina principer genom att utföra flera inloggningar manuellt kan du använda det här verktyget för att utvärdera en simulerad inloggning av en användare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport. Rapporten visar inte bara tillämpade principer för villkorlig åtkomst, utan även [klassiska principer](policy-migration.md#classic-policies) om de finns.    

**What If** -verktyget är ett sätt att snabbt fastställa de principer som gäller för en speciell användare. Du kan använda informationen, till exempel om du behöver felsöka ett problem.    

## <a name="how-it-works"></a>Så här fungerar det

I **What If verktyget för villkorlig åtkomst** måste du först konfigurera inställningarna för det inloggnings scenario som du vill simulera. Några exempel på inställningar är:

- Den användare som du vill testa 
- De molnappar som användaren försöker få åtkomst till
- De villkor under vilka åtkomst till de konfigurerade molnappar utförs
     
Som nästa steg kan du starta en simulerings körning som utvärderar dina inställningar. Endast principer som är aktiverade är en del av en utvärderings körning.

När utvärderingen är färdig genererar verktyget en rapport över de berörda principerna. Om du vill samla in mer information om en princip för villkorlig åtkomst kan du ange ytterligare information om principer i läget endast rapport och de principer som är aktiverade för [att få mer](howto-conditional-access-insights-reporting.md) information om principer i läget för enbart rapporter.

## <a name="running-the-tool"></a>Köra verktyget

Du hittar **What If** -verktyget på sidan **[villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** i Azure Portal.

Starta verktyget genom att klicka på **What If** i verktygsfältet överst i listan över principer.

:::image type="content" source="./media/what-if-tool/01.png" alt-text="Skärm bild av sidan villkorlig åtkomst – principer i Azure Portal. I verktygsfältet visas vad om-objektet är markerat." border="false":::

Innan du kan köra en utvärdering måste du konfigurera inställningarna.

## <a name="settings"></a>Inställningar

Det här avsnittet innehåller information om inställningarna för simulerings körning.

:::image type="content" source="./media/what-if-tool/02.png" alt-text="Skärm bild av sidan Azure Portal What If med fält för en användare, molnappar, en I P-adress, en enhets plattform, en klient-app och en inloggnings risk." border="false":::

### <a name="user"></a>User

Du kan bara välja en användare. Detta är det enda obligatoriska fältet.

### <a name="cloud-apps"></a>Molnappar

Standardinställningen för den här inställningen är **alla molnappar**. Standardinställningen utför en utvärdering av alla tillgängliga principer i din miljö. Du kan begränsa omfattningen till principer som påverkar specifika molnappar.

### <a name="ip-address"></a>IP-adress

IP-adressen är en enskild IPv4-adress för att efterlikna [plats villkoret](location-condition.md). Adressen representerar Internet adressen till den enhet som används av din användare för att logga in. Du kan kontrol lera IP-adressen för en enhet genom att t. ex. Navigera till [Vad är min IP-adress](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Enhetsplattformar

Den här inställningen imiterar [villkoret för enhets plattformar](concept-conditional-access-conditions.md#device-platforms) och motsvarar **alla plattformar (inklusive ej stödda)**. 

### <a name="client-apps"></a>Klientappar

Med den här inställningen imiteras [villkoret för klient program](concept-conditional-access-conditions.md#client-apps).
Som standard gör den här inställningen en utvärdering av alla principer som har **webbläsare** eller **mobilappar och skriv bords klienter,** antingen individuellt eller båda markerade. Den identifierar också principer som tillämpar **Exchange ActiveSync (EAS)**. Du kan begränsa den här inställningen genom att välja:

- **Webbläsare** för att utvärdera alla principer som har minst **webbläsare** vald. 
- **Mobilappar och skriv bords klienter** för att utvärdera alla principer som har minst **mobila appar och skriv bords klienter** valda. 

### <a name="sign-in-risk"></a>Inloggningsrisk

Den här inställningen imiterar [inloggnings risk villkoret](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Utvärdering 

Du startar en utvärdering genom att klicka på **What If**. Utvärderings resultatet ger dig en rapport som består av: 

:::image type="content" source="./media/what-if-tool/03.png" alt-text="Skärm bild av en utvärderings rapport. Text anger att minst en klassisk princip har kon figurer ATS. Flikarna är tillgängliga för att visa principer." border="false":::

- En indikator om de klassiska principerna finns i din miljö
- Principer som gäller för din användare
- Principer som inte gäller för din användare

Om det finns [klassiska principer](policy-migration.md#classic-policies) för de valda molnappar visas en indikator för dig. Genom att klicka på indikatorn omdirigeras du till sidan klassiska principer. På sidan klassiska principer kan du migrera en klassisk princip eller bara inaktivera den. Du kan återgå till utvärderings resultatet genom att stänga den här sidan.

I listan över principer som gäller för din valda användare kan du också hitta en lista över de [kontroll](concept-conditional-access-grant.md) -och [sessionsnycklar](concept-conditional-access-session.md) som användaren måste uppfylla.

I listan över principer som inte gäller för din användare kan du även se varför dessa principer inte tillämpas. För varje listad princip representerar orsaken det första villkoret som inte uppfylldes. En möjlig orsak till en princip som inte tillämpas är en inaktive rad princip eftersom de inte utvärderas ytterligare.   

## <a name="next-steps"></a>Nästa steg

- Mer information om principer för villkorlig åtkomst finns i princip läget endast rapport läge med hjälp av [villkorlig åtkomst insikter och rapportering](howto-conditional-access-insights-reporting.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa [vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md).
