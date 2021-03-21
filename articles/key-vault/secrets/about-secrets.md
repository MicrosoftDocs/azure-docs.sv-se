---
title: Om Azure Key Vault hemligheter – Azure Key Vault
description: Översikt över Azure Key Vault hemligheter.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 55814dff5cba572e2e22b5a0f9971bc920e32372
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526653"
---
# <a name="about-azure-key-vault-secrets"></a>Om Azure Key Vault hemligheter

[Key Vault](../general/overview.md) ger säker lagring av allmänna hemligheter, t. ex. lösen ord och databas anslutnings strängar.

I ett utvecklings perspektiv kan Key Vault API: er acceptera och returnera hemliga värden som strängar. Internt Key Vault lagrar och hanterar hemligheter som sekvenser av oktetter (8-bitars byte), med en maximal storlek på 25k byte. Tjänsten Key Vault tillhandahåller inte semantik för hemligheter. Den accepterar bara data, krypterar den, lagrar den och returnerar en hemlig identifierare ("ID"). Identifieraren kan användas för att hämta hemligheten vid ett senare tillfälle.  

För data som är mycket känsliga bör klienter överväga ytterligare skyddslager för data. Ett exempel är kryptering av data med hjälp av en separat skyddsnyckel före lagring i Key Vault.  

Key Vault stöder också ett contentType-fält för hemligheter. Klienter kan ange innehålls typen för en hemlighet för att under lätta tolkningen av hemliga data när den hämtas. Den maximala längden för det här fältet är 255 tecken. Det finns inga fördefinierade värden. Den föreslagna användningen är som ett tips för att tolka hemliga data. Till exempel kan en implementering lagra både lösen ord och certifikat som hemligheter, och sedan använda det här fältet för att skilja. Det finns inga fördefinierade värden.  

## <a name="encryption"></a>Kryptering

Alla hemligheter i Key Vault lagras krypterade. Den här krypteringen är transparent och ingen åtgärd krävs från användaren. Tjänsten Azure Key Vault krypterar dina hemligheter när du lägger till dem och dekrypterar dem automatiskt när du läser dem. Krypterings nyckeln är unik för varje nyckel valv.

## <a name="secret-attributes"></a>Hemliga attribut

Utöver hemliga data kan följande attribut anges:  

- *exp*: IntDate, valfritt, standard är för **alltid**. Attributet *exp* (förfallo tid) anger förfallo tid för eller efter vilken hemliga data inte ska hämtas, förutom i [särskilda situationer](#date-time-controlled-operations). Det här fältet används endast i **informations** syfte eftersom det informerar användare av Key Vault-tjänsten att en viss hemlighet inte får användas. Värdet måste vara ett tal som innehåller ett IntDate-värde.   
- *NBF*: IntDate, valfritt, standard är **nu**. Attributet *NBF* (inte före) anger den tid före vilken hemliga data inte ska hämtas, förutom i [särskilda situationer](#date-time-controlled-operations). Det här fältet används endast i **informations** syfte. Värdet måste vara ett tal som innehåller ett IntDate-värde. 
- *aktive rad*: boolesk, valfritt, standardvärdet är **True**. Det här attributet anger om hemliga data kan hämtas. Det aktiverade attributet används tillsammans med *NBF* och *exp* när en åtgärd sker mellan *NBF* och *exp*, men det är bara tillåtet om aktive rad är inställt på **Sant**. Åtgärder utanför *NBF* och *exp* -fönstret tillåts inte automatiskt, förutom i [vissa situationer](#date-time-controlled-operations).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *skapad*: IntDate, valfritt. Attributet created anger när den här versionen av hemligheten skapades. Det här värdet är null för hemligheter som skapats innan det här attributet läggs till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *uppdaterad*: IntDate, valfritt. Det uppdaterade attributet indikerar när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades innan det här attributet lades till. Värdet måste vara ett tal som innehåller ett IntDate-värde.

Information om gemensamma attribut för varje nyckel valv objekt typ finns i [Azure Key Vault nycklar, hemligheter och certifikat översikt](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>Kontrollerade åtgärder för datum/tid

En hemlighet för **Get** -åtgärden fungerar för ej ännu giltiga och utgångna hemligheter, utanför *NBF*  /  *exp* -fönstret. Att anropa en hemlig åtgärd för **hämtning** , för en icke-giltig hemlighet, kan användas i test syfte. Hämtning av (**få fram**) en utgånget hemligt kan användas för återställnings åtgärder.

## <a name="secret-access-control"></a>Åtkomstkontroll till hemlighet

Access Control för hemligheter som hanteras i Key Vault ges på nivån för den Key Vault som innehåller dessa hemligheter. Principen för åtkomst kontroll för hemligheter är distinkt från åtkomst kontroll principen för nycklar i samma Key Vault. Användare kan skapa ett eller flera valv för att hålla hemligheter, och de krävs för att upprätthålla lämplig segmentering och hantering av hemligheter.   

Följande behörigheter kan användas, per huvud konto, i åtkomst kontroll posten för hemligheter i ett valv, och i nära spegling av de åtgärder som tillåts på ett hemligt objekt:  

- Behörigheter för hemliga hanterings åtgärder
  - *Hämta*: Läs en hemlighet  
  - *lista*: visar en lista över hemligheter eller versioner av en hemlighet som lagras i en Key Vault  
  - *Ange*: skapa en hemlighet  
  - *ta bort*: ta bort en hemlighet  
  - *återställa*: Återställ en borttagen hemlighet
  - *säkerhets kopiering*: säkerhetskopiera en hemlighet i ett nyckel valv
  - *återställning*: återställa en säkerhetskopierad hemlighet till ett nyckel valv

- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) en borttagen hemlighet

Mer information om hur du arbetar med hemligheter finns [i avsnittet om hemliga åtgärder i referensen Key Vault REST API](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy). 

Instruktions guider för att kontrol lera åtkomst i Key Vault:
- [Tilldela en Key Vault åtkomst princip med CLI](../general/assign-access-policy-cli.md)
- [Tilldela en Key Vault åtkomst princip med hjälp av PowerShell](../general/assign-access-policy-powershell.md)
- [Tilldela en Key Vault åtkomst princip med hjälp av Azure Portal](../general/assign-access-policy-portal.md)
- [Ge åtkomst till Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure](../general/rbac-guide.md)

## <a name="secret-tags"></a>Hemliga Taggar  
Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som var och en kan ha ett 256-namn och ett värde på 256.  

>[!Note]
>Taggarna kan läsas av en anropare om de har *listan* eller *Get* -behörighet.

## <a name="azure-storage-account-key-management"></a>Azure Storage konto nyckel hantering

Key Vault kan hantera [Azure Storage-konto](../../storage/common/storage-account-overview.md) nycklar:

- Internt Key Vault kan ange (Sync) nycklar med ett Azure Storage-konto. 
- Key Vault återskapas (roterar) nycklarna med jämna mellanrum.
- Nyckel värden returneras aldrig som svar på anroparen.
- Key Vault hanterar nycklar för både lagrings konton och klassiska lagrings konton.

Mer information finns i:
- [Lagringskontots åtkomstnycklar](../../storage/common/storage-account-keys-manage.md)
- [Hantering av lagrings konto nycklar i Azure Key Vault](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Åtkomst kontroll för lagrings konto

Följande behörigheter kan användas när du auktoriserar en användare eller ett programs huvud konto för att utföra åtgärder på ett hanterat lagrings konto:  

- Behörigheter för hanterat lagrings konto och SaS-definitions åtgärder
  - *Get*: hämtar information om ett lagrings konto 
  - *lista*: visar en lista över lagrings konton som hanteras av en Key Vault
  - *Uppdatera*: uppdatera ett lagrings konto
  - *ta bort*: ta bort ett lagrings konto  
  - *återställa*: Återställ ett borttaget lagrings konto
  - *säkerhets kopiering*: säkerhetskopiera ett lagrings konto
  - *återställa*: Återställ ett säkerhetskopierat lagrings konto till en Key Vault
  - *Ange*: skapa eller uppdatera ett lagrings konto
  - *regeneratekey*: återskapa ett angivet nyckel värde för ett lagrings konto
  - *hämtar* till: Hämta information om en SAS-definition för ett lagrings konto
  - *lister*: lista över lagrings-SAS-definitioner för ett lagrings konto
  - *borttagningar*: ta bort en SAS-definition från ett lagrings konto
  - *Setas*: skapa eller uppdatera en ny SAS-definition/-attribut för ett lagrings konto

- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) ett hanterat lagrings konto

Mer information finns i [lagrings konto åtgärder i referensen Key Vault REST API](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy).

Instruktions guider för att kontrol lera åtkomst i Key Vault:
- [Tilldela en Key Vault åtkomst princip med CLI](../general/assign-access-policy-cli.md)
- [Tilldela en Key Vault åtkomst princip med hjälp av PowerShell](../general/assign-access-policy-powershell.md)
- [Tilldela en Key Vault åtkomst princip med hjälp av Azure Portal](../general/assign-access-policy-portal.md)
- [Ge åtkomst till Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure](../general/rbac-guide.md)


## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om nycklar](../keys/about-keys.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Säker åtkomst till ett nyckelvalv](../general/secure-your-key-vault.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)