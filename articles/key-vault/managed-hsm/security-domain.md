---
title: Om Azure-hanterad HSM-säkerhetsdomän
description: Översikt över den hanterade HSM-säkerhetsdomänen, en uppsättning grundläggande autentiseringsuppgifter som krävs för att återställa en hanterad HSM
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90997210"
---
# <a name="about-the-managed-hsm-security-domain"></a>Om den hanterade HSM-säkerhetsdomänen

Den hanterade HSM-säkerhetsdomänen (SD) är en uppsättning grundläggande autentiseringsuppgifter som krävs för att återställa en hanterad HSM om det finns en katastrof. Säkerhets domänen genereras i den hanterade HSM-maskinvaran och enclaves och representerar "ägande" av HSM.

Varje hanterad HSM måste ha en säkerhets domän för att kunna användas. När du begär en ny hanterad HSM, är den etablerad men inte aktive rad förrän du har laddat ned säkerhets domänen. När en hanterad HSM har tillhandahållits, men inte Aktiver ATS, finns det två sätt att aktivera den:
- Att ladda ned din säkerhets domän är standard metoden, och gör att du på ett säkert sätt kan lagra säkerhets domänen som antingen används med en annan hanterad HSM eller för att återställa från en total katastrof.
- Överför en befintlig säkerhets domän som du redan har, vilket gör att du kan skapa flera hanterade HSM-instanser som delar samma säkerhets domän.

## <a name="download-your-security-domain"></a>Hämta din säkerhets domän

När en hanterad HSM är etablerad men inte aktive rad, avbildas de viktiga autentiseringsuppgifter som krävs för att återställa från en fullständig förlust av all maskin vara. Om du vill hämta säkerhets domänen måste du skapa minst tre (högst 10) RSA-nyckelpar och skicka dessa offentliga nycklar till tjänsten när du begär att säkerhets domänen ska laddas ned. Du måste också ange det minsta antal nycklar som krävs (kvorum) för att dekryptera säkerhets domänen i framtiden. Den hanterade HSM initierar säkerhets domänen och krypterar den med de offentliga nycklar som du anger med hjälp av [Shamir för hemliga delning](https://dl.acm.org/doi/10.1145/359168.359176). Den säkerhets domän-blob som du hämtar kan bara dekrypteras när det finns minst ett kvorum med privata nycklar. Du måste ha de privata nycklarna säkert för att garantera säkerheten för säkerhets domänen. När nedladdningen är klar kommer den hanterade HSM att vara i aktiverat läge klart för användning.  

> [!IMPORTANT]
> För en fullständig haveri beredskap måste du ha säkerhets domänen och kvorumet för privata nycklar som användes för att skydda den och en fullständig HSM-säkerhetskopiering. Om du tappar bort säkerhets domänen eller tillräckliga av RSA-nycklarna (privat nyckel) för att förlora kvorum, och det inte finns några pågående instanser av hanterad HSM, går det inte att göra en katastrof återställning.

## <a name="upload-a-security-domain"></a>Ladda upp en säkerhets domän

När en hanterad HSM är etablerad men inte aktive rad kan du initiera en säkerhets domän återställnings process. Hanterad HSM kommer att generera ett RSA-nyckelpar och returnera den offentliga nyckeln. Sedan kan du ladda upp säkerhets domänen till den hanterade HSM-filen. Innan du laddar upp måste klienten (Azure CLI eller PowerShell) tillhandahålla det lägsta antalet privata nycklar som du använde när du hämtade säkerhets domänen. Klienten dekrypterar säkerhets domänen med det här kvorumet och krypterar om det med den offentliga nyckel som du laddade ned när du begärde återställning. När överföringen är klar kommer den hanterade HSM att vara i aktiverat läge.

## <a name="backup-and-restore-behavior"></a>Beteende för säkerhets kopiering och återställning

Säkerhets kopieringar (antingen fullständig säkerhets kopiering eller en enskild nyckel säkerhets kopia) kan bara återställas utan problem om den hanterade HSM (där säkerhets kopian skapades) och den hanterade HSM-platsen (där säkerhets kopian kommer att återställas) delar samma säkerhets domän. På det här sättet definierar en säkerhets domän även en kryptografisk avgränsning för varje hanterad HSM.

## <a name="next-steps"></a>Nästa steg

- Läs en [Översikt över hanterad HSM](overview.md)
- Lär dig mer om att [Hantera hanterad HSM med Azure CLI](key-management.md)
- Granska [metod tips för hanterad HSM](best-practices.md)
