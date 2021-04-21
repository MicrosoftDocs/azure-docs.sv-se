---
title: Om Azure Key Vault hemligheter – Azure Key Vault
description: Översikt över Azure Key Vault hemligheter.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 6d4f3f744a85c14c42ffef1c894b237081e871f8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752434"
---
# <a name="about-azure-key-vault-secrets"></a>Om Azure Key Vault-hemligheter

[Key Vault](../general/overview.md) säker lagring av allmänna hemligheter, till exempel lösenord och databasanslutningssträngar.

Ur ett utvecklarperspektiv kan du Key Vault API:er accepterar och returnerar hemliga värden som strängar. Internt lagrar Key Vault och hanterar hemligheter som sekvenser med oktett (8-bitars byte), med en maximal storlek på 25 000 byte vardera. Tjänsten Key Vault tillhandahåller inte semantik för hemligheter. Den accepterar bara data, krypterar dem, lagrar dem och returnerar en hemlig identifierare ("id"). Identifieraren kan användas för att hämta hemligheten vid ett senare tillfälle.  

För data som är mycket känsliga bör klienter överväga ytterligare skyddslager för data. Ett exempel är kryptering av data med hjälp av en separat skyddsnyckel före lagring i Key Vault.  

Key Vault också stöd för ett contentType-fält för hemligheter. Klienter kan ange innehållstypen för en hemlighet för att hjälpa till att tolka hemliga data när de hämtas. Det här fältet får vara högst 255 tecken långt. Det finns inga fördefinierade värden. Den föreslagna användningen är ett tips för att tolka hemliga data. En implementering kan till exempel lagra både lösenord och certifikat som hemligheter och sedan använda det här fältet för att särskilja. Det finns inga fördefinierade värden.  

## <a name="encryption"></a>Kryptering

Alla hemligheter i din Key Vault lagras krypterade. Den här krypteringen är transparent och kräver ingen åtgärd från användaren. Tjänsten Azure Key Vault krypterar dina hemligheter när du lägger till dem och dekrypterar dem automatiskt när du läser dem. Krypteringsnyckeln är unik för varje nyckelvalv.

## <a name="secret-attributes"></a>Hemliga attribut

Utöver hemliga data kan följande attribut anges:  

- *exp:* IntDate, valfritt, standard är för **alltid**. Attributet *exp* (förfallotid) identifierar den förfallotid på eller efter vilken hemliga data INTE ska hämtas, förutom [i vissa situationer](#date-time-controlled-operations). Det här fältet är **endast i** informationssyfte eftersom det informerar användare av nyckelvalvstjänsten om att en viss hemlighet inte får användas. Värdet MÅSTE vara ett tal som innehåller ett IntDate-värde.   
- *nbf:* IntDate, valfritt, standard är **nu**. Attributet *nbf* (inte före) identifierar den tid innan hemliga data SKA HÄMTAS, förutom i [vissa situationer](#date-time-controlled-operations). Det här fältet **är endast i** informationssyfte. Värdet MÅSTE vara ett tal som innehåller ett IntDate-värde. 
- *enabled*: boolean, optional, default is **true**. Det här attributet anger om hemliga data kan hämtas. Det aktiverade attributet används tillsammans med *nbf* och *exp* när en åtgärd inträffar mellan *nbf* och *exp*. Det tillåts bara om aktiverat har angetts till **true**. Åtgärder utanför *nbf- och* *exp-fönstret* tillåts inte automatiskt, förutom i [vissa situationer](#date-time-controlled-operations).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *created*: IntDate, valfritt. Attributet som skapades anger när den här versionen av hemligheten skapades. Det här värdet är null för hemligheter som skapats före tillägget av det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *updated*: IntDate, valfritt. Det uppdaterade attributet anger när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades före tillägget av det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.

Information om vanliga attribut för varje objekttyp i nyckelvalvet finns i [Azure Key Vault översikt över nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>Datum/tid-kontrollerade åtgärder

En hemlighets **get-åtgärd** fungerar för icke-giltiga och utgångna hemligheter, utanför *exp-fönstret nbf.*  /   Anrop av en **hemlighets get-åtgärd** för en icke-giltig hemlighet kan användas i testsyfte. Hämtning **(get** ting) av en utgången hemlighet kan användas för återställningsåtgärder.

## <a name="secret-access-control"></a>Åtkomstkontroll till hemlighet

Access Control för hemligheter som hanteras i Key Vault tillhandahålls på nivån för de Key Vault som innehåller dessa hemligheter. Åtkomstkontrollprincipen för hemligheter skiljer sig från åtkomstkontrollprincipen för nycklar i samma Key Vault. Användare kan skapa ett eller flera valv som ska innehålla hemligheter och krävs för att upprätthålla lämplig segmentering och hantering av hemligheter i scenariot.   

Följande behörigheter kan användas, per huvudnamn, i åtkomstkontrollposten för hemligheter i ett valv och spegla noggrant de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för hemlighetshanteringsåtgärder
  - *hämta:* Läsa en hemlighet  
  - *lista:* Lista hemligheter eller versioner av en hemlighet som lagras i en Key Vault  
  - *set*: Skapa en hemlighet  
  - *ta bort:* Ta bort en hemlighet  
  - *recover*: Återställa en borttagna hemlighet
  - *säkerhetskopiering:* Säkerhetskopiera en hemlighet i ett nyckelvalv
  - restore :Restore a backed up secret to a key vault *(Återställa* en säkerhetskopierad hemlighet till ett nyckelvalv)

- Behörigheter för privilegierade åtgärder
  - *rensa:* Rensa (ta bort permanent) en borttagna hemlighet

Mer information om hur du arbetar med hemligheter finns [i Hemlighetsåtgärder i Key Vault REST API referens .](/rest/api/keyvault) Information om hur du etablerar behörigheter [finns i Valv – Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – Uppdatera åtkomstprincip.](/rest/api/keyvault/vaults/updateaccesspolicy) 

Anvisningar för att styra åtkomsten i Key Vault:
- [Tilldela en Key Vault åtkomstprincip med HJÄLP av CLI](../general/assign-access-policy-cli.md)
- [Tilldela en Key Vault åtkomstprincip med PowerShell](../general/assign-access-policy-powershell.md)
- [Tilldela en Key Vault åtkomstprincip med hjälp av Azure Portal](../general/assign-access-policy-portal.md)
- [Ge åtkomst Key Vault, certifikat och hemligheter med rollbaserad åtkomstkontroll i Azure](../general/rbac-guide.md)

## <a name="secret-tags"></a>Hemliga taggar  
Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault har stöd för upp till 15 taggar, där var och en kan ha ett namn på 256 tecken och ett värde på 256 tecken.  

>[!Note]
>Taggar kan läsas av en anropare om de har *listan eller* *får* behörighet.

## <a name="azure-storage-account-key-management"></a>Azure Storage hantering av kontonyckel

Key Vault kan hantera Azure [Storage-kontonycklar:](../../storage/common/storage-account-overview.md)

- Internt kan Key Vault (synkronisera) nycklar med ett Azure Storage-konto. 
- Key Vault återskapar (roterar) nycklarna med jämna mellanrum.
- Nyckelvärden returneras aldrig som svar på anroparen.
- Key Vault hanterar nycklar för både lagringskonton och klassiska lagringskonton.

Mer information finns i:
- [Lagringskontots åtkomstnycklar](../../storage/common/storage-account-keys-manage.md)
- [Hantering av lagringskontonycklar i Azure Key Vault](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Åtkomstkontroll för lagringskonto

Följande behörigheter kan användas när du auktoriserar en användare eller ett programhuvudnamn för att utföra åtgärder på ett hanterat lagringskonto:  

- Behörigheter för hanterade lagringskonto- och SaS-definitionsåtgärder
  - *get*: Hämtar information om ett lagringskonto 
  - *list*: Lista över lagringskonton som hanteras av en Key Vault
  - *update*: Uppdatera ett lagringskonto
  - *ta bort:* Ta bort ett lagringskonto  
  - *recover*: Återställa ett borttagna lagringskonto
  - *säkerhetskopiering:* Säkerhetskopiera ett lagringskonto
  - *restore*:Restore a backed-up storage account to a Key Vault
  - *set*: Skapa eller uppdatera ett lagringskonto
  - *regeneratekey:* Återskapa ett angivet nyckelvärde för ett lagringskonto
  - *getsas*: Hämta information om en SAS-definition för ett lagringskonto
  - *listsas*: List storage SAS definitions for a storage account (Lista lagrings-SAS-definitioner för ett lagringskonto)
  - *deletesas:* Ta bort en SAS-definition från ett lagringskonto
  - *setsas*: Skapa eller uppdatera en ny SAS-definition/-attribut för ett lagringskonto

- Behörigheter för privilegierade åtgärder
  - *rensa:* Rensa (permanent ta bort) ett hanterat lagringskonto

Mer information finns i Åtgärder [för lagringskonto i Key Vault REST API referens.](/rest/api/keyvault) Information om hur du etablerar behörigheter [finns i Valv – Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – Uppdatera åtkomstprincip.](/rest/api/keyvault/vaults/updateaccesspolicy)

Anvisningar för att styra åtkomsten i Key Vault:
- [Tilldela en Key Vault åtkomstprincip med HJÄLP av CLI](../general/assign-access-policy-cli.md)
- [Tilldela en Key Vault åtkomstprincip med PowerShell](../general/assign-access-policy-powershell.md)
- [Tilldela en Key Vault åtkomstprincip med hjälp av Azure Portal](../general/assign-access-policy-portal.md)
- [Ge åtkomst Key Vault, certifikat och hemligheter med rollbaserad åtkomstkontroll i Azure](../general/rbac-guide.md)


## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om nycklar](../keys/about-keys.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Säker åtkomst till ett nyckelvalv](../general/security-overview.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)