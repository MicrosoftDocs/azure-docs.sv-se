---
title: Logga in på Azure Storage Explorer | Microsoft Docs
description: Dokumentation om att logga in på Azure Storage Explorer
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568727"
---
# <a name="sign-in-to-storage-explorer"></a>Logga in på Storage Explorer

Inloggning är det rekommenderade sättet att komma åt dina Azure-lagringsresurser med Storage Explorer. Genom att logga in kan du dra nytta av Azure AD-stödda behörigheter, till exempel RBAC och Gen2 POSIX-ACL:er. 

## <a name="how-to-sign-in"></a>Logga in

Om du vill logga in Storage Explorer öppnar du **dialogrutan Anslut.** Du kan öppna dialogrutan **Anslut antingen** från det vänstra lodräta verktygsfältet eller genom att klicka på Lägg till **konto...** på **kontopanelen**.

När dialogrutan är öppen väljer du Prenumeration **som den** typ av resurs som du vill ansluta till och klickar på **Nästa.**

Nu måste du välja vilken Azure-miljö du vill logga in på. Du kan välja från någon av de kända miljöerna, till exempel Azure eller Azure Kina, eller så kan du lägga till en egen miljö. När du har valt din miljö klickar du på **Nästa.**

Nu startas operativsystemets **standardwebbläsare** och en inloggningssida öppnas. För bästa resultat lämnar du det här webbläsarfönstret öppet så länge du använder Storage Explorer eller åtminstone tills du har utfört all förväntad MFA. När du har loggat in kan du gå tillbaka till Storage Explorer.

## <a name="managing-accounts"></a>Hantera konton

Du kan hantera och ta bort Azure-konton som du har loggat in på på **kontopanelen.** Du kan öppna **kontopanelen genom** att klicka på **knappen Hantera** konton i det vänstra lodräta verktygsfältet.

I **kontopanelen visas** alla konton som du har loggat in på. Under varje konto kommer att vara:
- De klienter som kontot tillhör
- För varje klientorganisation har de prenumerationer som du har åtkomst till

Som standard loggar Storage Explorer dig till din hemklientorganisation. Om du vill visa prenumerationer och resurser från en annan klientorganisation måste du aktivera den klientorganisationen. Om du vill aktivera en klient markerar du kryssrutan bredvid den. När du är klar med att arbeta med en klientorganisation kan du avmarkera dess kryssruta för att inaktivera den. Du kan inte inaktivera din hemklientorganisation.

När du har aktiverat en klient kan du behöva återaktivera dina autentiseringsuppgifter innan Storage Explorer kan läsa in prenumerationer eller komma åt resurser från klientorganisationen. Att behöva återindela dina autentiseringsuppgifter sker vanligtvis på grund av en princip för villkorlig åtkomst (CA), till exempel multifaktorautentisering (MFA). Och även om du kanske redan har utfört MFA för en annan klient kan du fortfarande behöva göra det igen. Om du vill ange dina autentiseringsuppgifter igen klickar du bara på **Ange autentiseringsuppgifter igen...**. Du kan också klicka på **Felinformation... för** att se exakt varför prenumerationerna inte kunde läsas in.

När dina prenumerationer har lästs in kan du välja vilka som du vill filtrera in/ut genom att markera eller avmarkera deras kryssrutor.

Om du vill ta bort hela ditt Azure-konto klickar du på **Ta** bort bredvid kontot.

## <a name="changing-where-sign-in-happens"></a>Ändra var inloggning sker

Som standard sker inloggningen i operativsystemets **standardwebbläsare.** Inloggning med din standardwebbläsare effektiviserar hur du kommer åt resurser som skyddas via CA-principer, till exempel MFA. Om inloggningen med operativsystemets  standardwebbläsare av någon anledning inte fungerar kan du ändra var eller hur Storage Explorer utför inloggningen.

Under **Inställningar**  >  **för**  >  **program inloggning** letar du upp inställningen Logga in **med.** Det finns tre alternativ:
- **Standardwebbläsare:** inloggning sker i operativsystemets **standardwebbläsare.** Det här alternativet rekommenderas.
- **Integrerad inloggning:** inloggning sker i ett Storage Explorer fönster. Det här alternativet kan vara användbart om du försöker logga in med flera Microsoft-konton (MSA) samtidigt. Du kan ha problem med vissa CA-principer om du väljer det här alternativet.
- **Enhetskodflöde:** Storage Explorer ger dig en kod som du kan ange i ett webbläsarfönster. Det här alternativet rekommenderas inte. Enhetskodflödet är inte kompatibelt med många CA-principer.

## <a name="troubleshooting-sign-in-issues"></a>Felsöka inloggningsproblem

Om du har problem med att logga in eller har problem med ett Azure-konto efter att du har loggat in kan du gå till [avsnittet logga in i Storage Explorer felsökningsguiden](./storage-explorer-troubleshooting.md#sign-in-issues).
