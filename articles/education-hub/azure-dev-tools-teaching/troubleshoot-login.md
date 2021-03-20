---
title: Lös inloggnings fel för Azure dev tools för undervisning
description: Detta beskriver de åtgärder som en student bör vidta om de får ett fel meddelande när de loggar in på Azure dev tools för undervisning.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87099720"
---
# <a name="troubleshooting-student-login-issues"></a>Felsöka problem med student inloggning
Åtkomst till Azure dev tools för undervisning kräver att en användare har ett Microsoft-konto (MSA). Studenter dirigeras automatiskt för att skapa en MSA om deras konto inte redan är en MSA eller länkat till en MSA. Om din domän är associerad med Active Directory, anses alla konton under den domänen redan vara en MSA.

Om en student försöker logga in och får följande fel meddelande, använder du en av de lösningar som beskrivs nedan.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Fel meddelande för inloggning." border="false":::

Det finns två lösningar: skapa en ny Microsoft-konto eller Använd en befintlig Microsoft-konto.

## <a name="create-a-new-microsoft-account"></a>Skapa en ny Microsoft-konto
### <a name="use-a-university-email-address"></a>Använd en e-postadress i University
Om du loggar in med en University-e-postadress (till exempel `John.Smith@university.edu` ) måste du skapa en Microsoft-konto med den e-postadressen. Att skapa ett konto är kostnads fritt och tar bara några minuter. Med en Microsoft-konto kan du automatiskt logga in på alla Microsoft-produkter med ett enda användar namn och lösen ord.

### <a name="use-a-personal-email-address"></a>Använd en personlig e-postadress
Om du loggar in med en personlig e-postadress (till exempel `John.Smith@email.com` ) men också har en e-postadress för universitet, kan du prova att använda University-e-postadressen. Om du tidigare har loggat in på din institutions WebStore med en personlig e-postadress eller inte har någon universitets-e-postadress, måste du skapa eller länka en Microsoft-konto med din personliga e-postadress.

## <a name="use-an-existing-microsoft-account"></a>Använd en befintlig Microsoft-konto
Om en student har en befintlig Microsoft-konto (till exempel Xbox) kan de ansluta det kontot till ett Azure dev tools-konto.

1. Gå till https://account.microsoft.com.
1. Logga in med dina Microsoft-konto autentiseringsuppgifter.
1. Välj **din information** på den översta menyn i menyfliksområdet.

1. Klicka på **hantera hur du loggar** in på Microsoft. Du uppmanas att verifiera din identitet. Du kommer att e-posta en säkerhets kod.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Hantera inloggning." border="false":::

1. Ange den e-postrelaterade säkerhets koden.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Ange inloggnings kod." border="false":::!

1. Klicka på **Lägg till e-post** till ditt konto och ange din e-postadress för University.
Nästa gång du loggar in kan du använda din e-postadress i University för att få åtkomst till dina Azure dev-verktyg för utbildning.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Hantera hur du loggar in på Microsoft." border="false":::

## <a name="next-steps"></a>Nästa steg
- [VANLIGA FRÅGOR OCH SVAR](program-faq.md)

- [Supportalternativ](program-support.md)
