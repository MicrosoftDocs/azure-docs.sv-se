---
title: Säkerhets rekommendationer för Queue Storage
titleSuffix: Azure Storage
description: Lär dig mer om säkerhets rekommendationer för Queue Storage. Genom att implementera den här vägledningen kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: db0e033adf553c25c6b7b401f8d0df1a2cd5995f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592168"
---
# <a name="security-recommendations-for-queue-storage"></a>Säkerhets rekommendationer för Queue Storage

Den här artikeln innehåller säkerhets rekommendationer för Queue Storage. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell. Mer information om vad Microsoft gör för att uppfylla ansvaret för service leverantörer finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Några av rekommendationerna som ingår i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvars linjen i att skydda dina resurser i Azure. Information om Azure Security Center finns i [Azure Security Center?](../../security-center/security-center-introduction.md).

Azure Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Sedan får du rekommendationer om hur du kan åtgärda dem. Mer information om Azure Security Center rekommendationer finns [i säkerhets rekommendationer i Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Använd Azure Resource Manager distributions modell | Skapa nya lagrings konton med hjälp av Azure Resource Manager distributions modell för viktiga säkerhets förbättringar, inklusive överlägsen Azure rollbaserad åtkomst kontroll (Azure RBAC) och granskning, Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till Azure Key Vault för hemligheter och Azure AD-baserad autentisering och auktorisering för att få åtkomst till Azure Storage data och resurser. Migrera om möjligt Befintliga lagrings konton som använder den klassiska distributions modellen för att använda Azure Resource Manager. Mer information om Azure Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md). | - |
| Aktivera avancerat skydd för alla dina lagrings konton | Avancerat skydd för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Säkerhets aviseringar utlöses i Azure Security Center när avvikelser i aktivitet sker och också skickas via e-post till prenumerations administratörer, med information om misstänkt aktivitet och rekommendationer för att undersöka och åtgärda hot. Mer information finns i [Avancerat skydd mot Azure Storage](../common/azure-defender-storage-configure.md). | [Ja](../../security-center/security-center-remediate-recommendations.md) |
| Begränsa endast SAS-token (signatur för delad åtkomst) till HTTPS-anslutningar | Att kräva HTTPS när en klient använder en SAS-token för att komma åt Queue data bidrar till att minimera risken för avlyssning. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Använd Azure Active Directory (Azure AD) för att ge åtkomst till köade data | Azure AD ger överlägsen säkerhet och lätt att använda för autentisering med delad nyckel för att auktorisera begär anden till Queue Storage. Mer information finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md). | - |
| Kom ihåg huvud kontot för minsta behörighet när du tilldelar behörigheter till ett säkerhets objekt för Azure AD via Azure RBAC | När du tilldelar en roll till en användare, grupp eller ett program, så beviljar du detta säkerhets objekt endast de behörigheter som krävs för att utföra sina uppgifter. Genom att begränsa åtkomsten till resurser kan du förhindra både oavsiktlig och skadlig missbruk av dina data. | - |
| Skydda konto åtkomst nycklar med Azure Key Vault | Microsoft rekommenderar att Azure AD används för att auktorisera begär anden till Azure Storage. Men om du måste använda autentisering med delad nyckel kan du skydda dina konto nycklar med Azure Key Vault. Du kan hämta nycklarna från nyckel valvet vid körning i stället för att spara dem i ditt program. | - |
| Återskapa dina konto nycklar regelbundet | Genom att rotera konto nycklarna minskar du regelbundet risken för att exponera dina data till skadliga aktörer. | - |
| Kom ihåg huvud kontot för minsta behörighet när du tilldelar behörigheter till en SAS | När du skapar en SAS anger du endast de behörigheter som krävs av klienten för att utföra sin funktion. Genom att begränsa åtkomsten till resurser kan du förhindra både oavsiktlig och skadlig missbruk av dina data. | - |
| Ha en återkallnings plan för alla SAS som du utfärdar till klienter | Om en SAS komprometteras, vill du återkalla SAS så snart som möjligt. Om du vill återkalla en användar Delegerings-SAS ska du återkalla användar Delegerings nyckeln så att alla signaturer som är associerade med den här nyckeln blir snabbt Om du vill återkalla en tjänst-SAS som är associerad med en lagrad åtkomst princip kan du ta bort den lagrade åtkomst principen, byta namn på principen eller ändra förfallo tiden till en tid som redan har passerat. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).  | - |
| Om en tjänst-SAS inte är associerad med en lagrad åtkomst princip anger du förfallo tiden till en timme eller mindre | En tjänst-SAS som inte är associerad med en lagrad åtkomst princip kan inte återkallas. Därför bör du begränsa förfallo tiden så att SAS är giltig i en timme eller mindre. | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Konfigurera den minsta version av Transport Layer Security (TLS) som krävs för ett lagrings konto.  | Kräv att klienter använder en säkrare version av TLS för att göra förfrågningar mot ett Azure Storage konto genom att konfigurera den lägsta versionen av TLS för det kontot. Mer information finns i [Konfigurera minsta nödvändiga version av Transport Layer Security (TLS) för ett lagrings konto](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| Aktivera alternativet för **säker överföring som krävs** på alla dina lagrings konton | När du aktiverar alternativet för **säker överföring krävs** måste alla begär Anden som görs mot lagrings kontot äga rum via säkra anslutningar. Förfrågningar som görs över HTTP kommer att Miss klar. Mer information finns i [Kräv säker överföring i Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Ja](../../security-center/security-center-remediate-recommendations.md) |
| Aktivera brandväggsregler | Konfigurera brand Väggs regler för att begränsa åtkomsten till ditt lagrings konto till begär Anden som kommer från angivna IP-adresser eller intervall, eller från en lista över undernät i ett virtuellt Azure-nätverk (VNet). Mer information om hur du konfigurerar brand Väggs regler finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Tillåt att betrodda Microsoft-tjänster har åtkomst till lagrings kontot | Att aktivera brand Väggs regler för ditt lagrings konto blockerar inkommande begär Anden om data som standard, om inte begär Anden kommer från en tjänst som körs i ett Azure VNet eller från tillåtna offentliga IP-adresser. Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare. Du kan tillåta förfrågningar från andra Azure-tjänster genom att lägga till ett undantag för att tillåta betrodda Microsoft-tjänster att komma åt lagrings kontot. Mer information om hur du lägger till ett undantag för betrodda Microsoft-tjänster finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Använda privata slutpunkter | En privat slut punkt tilldelar en privat IP-adress från ditt virtuella Azure-nätverk till lagrings kontot. Den säkrar all trafik mellan ditt VNet och lagrings kontot via privat länk. Mer information om privata slut punkter finns i [ansluta privat till ett lagrings konto med hjälp av en privat Azure-slutpunkt](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Använda VNet-tjänstens Taggar | En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. Mer information om service märken som stöds av Azure Storage finns i [Översikt över Azure Service-Taggar](../../virtual-network/service-tags-overview.md). En själv studie kurs som visar hur du använder service märken för att skapa utgående nätverks regler finns i [begränsa åtkomsten till PaaS-resurser](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Begränsa nätverks åtkomst till vissa nätverk | Att begränsa nätverks åtkomsten till nätverk som är värd för klienter som kräver åtkomst minskar exponeringen för dina resurser till nätverks attacker. | [Ja](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Loggning och övervakning

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Spåra hur begär Anden auktoriseras | Aktivera Azure Storage loggning för att spåra hur varje begäran som görs mot Azure Storage auktoriserades. Loggarna anger om en begäran har gjorts anonymt, genom att använda en OAuth 2,0-token med hjälp av en delad nyckel eller med hjälp av en signatur för delad åtkomst (SAS). Mer information finns i [Azure Storage Analytics-loggning](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-säkerhet](../../security/index.yml)
- [Skydda utvecklings dokumentationen](../../security/develop/index.yml).
