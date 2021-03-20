---
title: LinkedIn-data delning och medgivande – Azure Active Directory | Microsoft Docs
description: Förklarar hur LinkedIn-integrering delar data via Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d13ae1e5198666e115507fd91bb0fedb1c5fa64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545918"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn-konto anslutningar data delning och medgivande

Du kan göra det möjligt för användare i din Active Directory (Azure AD) att samtycka till att ansluta sitt arbets-eller skol konto till Microsoft med ett LinkedIn-konto. När en användare ansluter sina konton är information och högdagrar från LinkedIn tillgängliga i vissa Microsoft-appar och-tjänster. Användarna kan också vänta sig att få nätverks upplevelsen på LinkedIn att förbättras och berikas med information från Microsoft.

Om du vill se LinkedIn-information i Microsoft-appar och-tjänster måste användarna godkänna att de ansluter sina egna Microsoft-och LinkedIn-konton. Användare uppmanas att ansluta sina konton första gången de klickar för att se någons LinkedIn-information på ett profil kort i Outlook, OneDrive eller SharePoint Online. LinkedIn-konto anslutningar är inte helt aktiverade för dina användare förrän de har samtycker till erfarenheten och för att ansluta sina konton.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Fördelar med att dela LinkedIn-information

Åtkomst till LinkedIn-information i Microsoft-appar och-tjänster gör det enklare för användarna att ansluta, engagera och bygga professionella relationer med kollegor, kunder och partner i och utanför organisationen. Nya användare kan komma igång snabbare genom att ansluta till kollegor, lära sig mer om dem och enkelt komma åt mer information. Här är ett exempel på hur LinkedIn-information visas på profil kortet i Microsoft-appar:

![Aktivera LinkedIn-integrering i din organisation](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Aktivera och meddela LinkedIn-integrering

Du måste vara Azure Active Directory administratör för att kunna hantera inställningen för din organisation. Du kan aktivera det för alla användare eller för en speciell uppsättning användare.

1. Om du vill aktivera eller inaktivera integrationen följer du stegen i [medgivande till LinkedIn-integrering för din Azure AD-organisation](linkedin-integration.md).
2. När du tillkännager LinkedIn-integreringen i din organisation kan du peka dina användare till vanliga frågor och svar om [LinkedIn-information i Microsoft-appar och-tjänster](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Artikeln innehåller information om var LinkedIn-information visas, [data delning och sekretess](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400), [hur man ansluter konton](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) med mera.

Du måste tillkännage LinkedIn-integration till dina användare, vilket ger all information om [data delning och sekretess med LinkedIn-integrering](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400). 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Användar medgivande för data åtkomst i Microsoft och LinkedIn

Data som nås från LinkedIn lagras inte permanent i Microsoft-tjänster. Data som nås från Microsoft lagras inte permanent med LinkedIn, med undantag för kontakter. Microsoft-kontakter lagras på LinkedIn tills användarna tar bort dem, enligt beskrivningen i [ta bort importerade kontakter från LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

När användare ansluter sina konton är information och insikter från LinkedIn tillgängliga i vissa Microsoft-appar, t. ex. profil kortet. Användarna kan också vänta sig att få nätverks upplevelsen på LinkedIn att förbättras och berikas med information från Microsoft.
När användare i din organisation ansluter sina LinkedIn-och Microsoft arbets-eller skol konton har de två alternativ:

* Ge åtkomst behörighet till data från båda kontona. Det innebär att de ger behörighet till sitt Microsoft-eller arbets konto för att komma åt data från deras LinkedIn-konto och för [deras LinkedIn-konto för att få åtkomst till data från sitt Microsoft arbets-eller skol konto](https://www.linkedin.com/help/linkedin/answer/84077).
* Ge bara behörighet för LinkedIn-data som ska användas av sitt Microsoft-arbets-och skol konto.

Användare kan när som helst koppla från konton och ta bort data åtkomst behörigheterna och [användarna kan styra hur deras egna LinkedIn-profil visas](https://www.linkedin.com/help/linkedin/answer/83), inklusive om deras profil kan visas i Microsoft-appar.

### <a name="linkedin-account-data"></a>LinkedIn-konto data

När du ansluter dina Microsoft-och LinkedIn-konton tillåter du LinkedIn att tillhandahålla följande data till Microsoft:

* Profil data – innehåller LinkedIn-identitet, kontakt information och den information som du delar med andra på din [LinkedIn-profil](https://www.linkedin.com/help/linkedin/answer/15493).
* Intresse data – innehåller intressen på LinkedIn, till exempel personer och ämnen som du följer, kurser grupper och innehåll som du gillar och delar.
* Prenumerations data – innehåller prenumerationer på LinkedIn-program och-tjänster tillsammans med tillhör ande data. 
* Anslutnings data – innehåller ditt [LinkedIn-nätverk](https://www.linkedin.com/help/linkedin/answer/110) inklusive profiler och kontakt information för dina 1: e-graderade anslutningar.

Data som nås från LinkedIn lagras inte permanent i Microsoft-tjänster. Mer information om Microsofts användning av personliga data finns i [Microsofts sekretess policy](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Microsoft arbets-eller skol konto data

När du ansluter dina Microsoft-och LinkedIn-konton gör det möjligt för Microsoft att tillhandahålla följande data till LinkedIn:

* Profil data – innehåller information som ditt förnamn, efter namn, profil foto, e-postadress, chef och personer som du hanterar.
* Kalender data – innehåller möten i dina kalendrar, deras tider, platser och deltagares kontakt uppgifter. Information om mötet, t. ex. dag ordning, innehåll eller Mötes rubrik, ingår inte i kalender data.
* Data för intressen – innehåller de intressen som är kopplade till ditt konto, baserat på din användning av Microsoft-tjänster, till exempel Cortana och Bing för företag.
* Prenumerations data – innehåller prenumerationer från din organisation till Microsoft-appar och-tjänster, till exempel Microsoft 365.
* Kontakt data – innehåller kontakt listor i Outlook, Skype och andra Microsoft-konto tjänster, inklusive kontakt information för personer som du ofta kommunicerar eller samarbetar med. Kontakter importeras regelbundet, lagras och används av LinkedIn, till exempel för att föreslå anslutningar, hjälpa till att organisera kontakter och Visa uppdateringar om kontakter.

Data som nås från Microsoft lagras inte permanent med LinkedIn, med undantag för kontakter. Microsoft-kontakter lagras på LinkedIn tills användarna tar bort dem. Lär dig mer om att [ta bort importerade kontakter från LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Mer information om LinkedIn användning av person uppgifter finns i [Sekretess policyn för LinkedIn](https://www.linkedin.com/legal/privacy-policy). För LinkedIn-tjänster, data överföring och lagring kan data flöda från Europeiska unionen till USA och tillbaka, och din integritet skyddas enligt beskrivningen i [data överföringar i Europeiska unionen](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Nästa steg

* [LinkedIn i Microsoft-program med ditt arbets-eller skol konto](https://www.linkedin.com/help/linkedin/answer/84077)
