---
title: QnA Maker kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Microsoft erbjuder Microsoft-hanterade krypterings nycklar och du kan också hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för QnA Maker och hur du aktiverar och hanterar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524533"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker kryptering av data i vila

QnA Maker krypterar dina data automatiskt när de sparas i molnet, vilket hjälper till att uppfylla organisationens säkerhets-och efterlevnads mål.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Som standard använder din prenumeration krypteringsnycklar som hanteras av Microsoft. Det finns också möjlighet att hantera din prenumeration med dina egna nycklar som kallas Kundhanterade nycklar (CMK). CMK erbjuder större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data. Om CMK har kon figurer ATS för din prenumeration tillhandahålls dubbla kryptering, vilket ger ett sekundärt skydds lager, samtidigt som du kan kontrol lera krypterings nyckeln via din Azure Key Vault.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

QnA Maker använder CMK-stöd från Azure Search. Konfigurera [CMK i Azure Search att använda Azure Key Vault](../../search/search-security-manage-encryption-keys.md). Den här Azure-instansen bör vara kopplad till QnA Maker tjänst för att göra den CMK aktive rad.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

QnA Maker använder [CMK-stöd från Azure Search](../../search/search-security-manage-encryption-keys.md)och associerar automatiskt den angivna CMK för att kryptera de data som lagras i Azure Search-indexet.

---

> [!IMPORTANT]
> Din Azure Search tjänst resurs måste ha skapats efter 2019 januari och får inte finnas på den kostnads fria nivån (delad). Det finns inget stöd för att konfigurera Kundhanterade nycklar i Azure Portal.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

Tjänsten QnA Maker använder CMK från tjänsten Azure Search. Följ de här stegen för att aktivera CMKs:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

1. Skapa en ny Azure Search-instans och aktivera de krav som anges i de [Kundhanterade nyckel kraven för Azure kognitiv sökning](../../search/search-security-manage-encryption-keys.md#prerequisites).

   ![Visa krypterings inställningar 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. När du skapar en QnA Maker resurs associeras den automatiskt med en Azure Search instans. Den här instansen kan inte användas med CMK. Om du vill använda CMK måste du associera den nyligen skapade instansen av Azure Search som skapades i steg 1. Mer specifikt måste du uppdatera `AzureSearchAdminKey` och `AzureSearchName` i QNA Maker-resursen.

   ![Visa krypterings inställningar 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Skapa sedan en ny program inställning:
   * **Namn**: Ange till `CustomerManagedEncryptionKeyUrl`
   * **Värde**: Använd det värde som du fick i steg 1 när du skapade Azure Search-instansen.

   ![Visa krypterings inställningar 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Starta om körningen när du är färdig. Nu är QnA Makers tjänsten CMK-aktiverad.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

1.  Gå till fliken **kryptering** i din QNA Maker hanterade tjänst (för hands version).
2.  Välj alternativet **kundens hanterade nycklar** . Ange information om dina [Kundhanterade nycklar](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) och klicka på **Spara**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="QnA Maker hanterad (för hands version) CMK-inställning" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  Vid en lyckad sparande används CMK för att kryptera de data som lagras i Azure Search indexet.

> [!IMPORTANT]
> Vi rekommenderar att du ställer in din CMK i en ny Azure Kognitiv sökning-tjänst innan alla kunskaps banker skapas. Om du ställer in CMK i en QnA Maker tjänst med befintliga kunskaps baser kan du förlora åtkomsten till dem. Läs mer om att [arbeta med krypterat innehåll](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) i Azure kognitiv search.

> [!NOTE]
> Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar in [formuläret för begäran om Cognitive Services Customer-Managed nyckel](https://aka.ms/cogsvc-cmk).

---

## <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är tillgängliga i alla Azure Search regioner.

## <a name="encryption-of-data-in-transit"></a>Kryptering av data under överföring

QnA Maker portalen körs i användarens webbläsare. Varje åtgärd utlöser ett direkt anrop till respektive kognitiva tjänst-API. Därför är QnA Maker kompatibel för data under överföring.
Men eftersom QnA Maker Portal tjänsten ligger i västra USA är den fortfarande inte idealisk för kunder som inte är USA. 

## <a name="next-steps"></a>Nästa steg

* [Kryptering i Azure Search med CMKs i Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Datakryptering i vila](../../security/fundamentals/encryption-atrest.md)
* [Läs mer om Azure Key Vault](../../key-vault/general/overview.md)