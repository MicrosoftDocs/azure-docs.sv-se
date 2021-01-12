---
title: Säkerhets rekommendationer för Blob Storage
titleSuffix: Azure Storage
description: Lär dig mer om säkerhets rekommendationer för Blob Storage. Genom att implementera den här vägledningen kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 1593c47b7a6cf3b861f3e1af148b7a5fbfae9a19
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127387"
---
# <a name="security-recommendations-for-blob-storage"></a>Säkerhets rekommendationer för Blob Storage

Den här artikeln innehåller säkerhets rekommendationer för Blob Storage. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell. Mer information om hur Microsoft uppfyller tjänst leverantörens ansvar finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Några av rekommendationerna som ingår i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvars linjen i att skydda dina resurser i Azure. Mer information om Azure Security Center finns i [Azure Security Center?](../../security-center/security-center-introduction.md)

Azure Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Sedan får du rekommendationer om hur du kan åtgärda dem. Mer information om Azure Security Center rekommendationer finns [i säkerhets rekommendationer i Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Använd Azure Resource Manager distributions modell | Skapa nya lagrings konton med hjälp av Azure Resource Manager distributions modell för viktiga säkerhets förbättringar, inklusive överlägsen Azure rollbaserad åtkomst kontroll (Azure RBAC) och granskning, Resource Manager-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till Azure Key Vault för hemligheter och Azure AD-baserad autentisering och auktorisering för att få åtkomst till Azure Storage data och resurser. Migrera om möjligt Befintliga lagrings konton som använder den klassiska distributions modellen för att använda Azure Resource Manager. Mer information om Azure Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md). | - |
| Aktivera Azure Defender för alla dina lagrings konton | Azure Defender för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Säkerhets aviseringar utlöses i Azure Security Center när avvikelser i aktivitet sker och också skickas via e-post till prenumerations administratörer, med information om misstänkt aktivitet och rekommendationer för att undersöka och åtgärda hot. Mer information finns i [Konfigurera Azure Defender för Azure Storage](../common/azure-defender-storage-configure.md). | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Aktivera mjuk borttagning för blobbar | Med mjuk borttagning kan du återställa BLOB-data när de har tagits bort. Mer information om mjuk borttagning finns i [mjuk borttagning för Azure Storage blobbar](./soft-delete-blob-overview.md). | - |
| Aktivera mjuk borttagning för behållare | ???. | - |
| Lås lagrings konto för att förhindra borttagning av oavsiktligt konto | Du kan låsa en Azure Resource Manager-resurs, till exempel en prenumeration, en resurs grupp eller ett lagrings konto, för att förhindra att andra användare i organisationen oavsiktligt tar bort eller ändrar det. Att låsa ett lagrings konto förhindrar inte att data i det kontot tas bort. Det förhindrar bara att själva kontot tas bort. Mer information finns i [låsa resurser för att förhindra oväntade ändringar](../../azure-resource-manager/management/lock-resources.md).
| Lagra affärs kritiska data i oföränderliga blobbar | Konfigurera juridiska undantag och tidsbaserade bevarande principer för att lagra BLOB-data i en mask (Skriv en gång, Läs många). Blobs lagrade immutably kan läsas, men kan inte ändras eller tas bort under lagrings intervallets varaktighet. Mer information finns i [lagra affärs kritiska BLOB-data med oföränderlig lagring](storage-blob-immutable-storage.md). | - |
| Kräv säker överföring (HTTPS) till lagrings kontot | ??? | - |
| Begränsa endast SAS-token (signatur för delad åtkomst) till HTTPS-anslutningar | Att kräva HTTPS när en klient använder en SAS-token för att få åtkomst till BLOB-data hjälper till att minimera risken för avlyssning. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Använda Azure Active Directory (Azure AD) för att auktorisera åtkomst till BLOB-data | Azure AD ger överlägsen säkerhet och enkel användning över delad nyckel för auktorisering av förfrågningar till Blob Storage. Mer information finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md). | - |
| Kom ihåg huvud kontot för minsta behörighet när du tilldelar behörigheter till ett säkerhets objekt för Azure AD via Azure RBAC | När du tilldelar en roll till en användare, grupp eller ett program, så beviljar du detta säkerhets objekt endast de behörigheter som krävs för att utföra sina uppgifter. Genom att begränsa åtkomsten till resurser kan du förhindra både oavsiktlig och skadlig missbruk av dina data. | - |
| Använd en användar Delegerings-SAS för att ge begränsad åtkomst till BLOB-data till klienter | En användar Delegerings-sa skyddas med Azure Active Directory (Azure AD)-autentiseringsuppgifter och även av de behörigheter som angetts för SAS. En användar Delegerings-SAS motsvarar en tjänst-SAS med avseende på dess omfattning och funktion, men ger säkerhets förmåner över tjänstens SAS. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | - |
| Skydda konto åtkomst nycklar med Azure Key Vault | Microsoft rekommenderar att Azure AD används för att auktorisera begär anden till Azure Storage. Men om du måste använda autentisering med delad nyckel kan du skydda dina konto nycklar med Azure Key Vault. Du kan hämta nycklarna från nyckel valvet vid körning i stället för att spara dem i ditt program. Mer information om Azure Key Vault finns i [Azure Key Vault översikt](../../key-vault/general/overview.md). | - |
| Återskapa dina konto nycklar regelbundet | Genom att rotera konto nycklarna minskar du regelbundet risken för att exponera dina data till skadliga aktörer. | - |
| Tillåt inte auktorisering av delad nyckel | Om du inte tillåter autentisering med delad nyckel för ett lagrings konto, Azure Storage neka alla efterföljande begär anden till det kontot som har behörighet till kontots åtkomst nycklar. Endast säkra begär Anden som har auktoriserats med Azure AD kommer att lyckas. Mer information finns i [förhindra auktorisering av delad nyckel för ett Azure Storage-konto](../common/shared-key-authorization-prevent.md). | - |
| Kom ihåg huvud kontot för minsta behörighet när du tilldelar behörigheter till en SAS | När du skapar en SAS anger du endast de behörigheter som krävs av klienten för att utföra sin funktion. Genom att begränsa åtkomsten till resurser kan du förhindra både oavsiktlig och skadlig missbruk av dina data. | - |
| Ha en återkallnings plan för alla SAS som du utfärdar till klienter | Om en SAS komprometteras, vill du återkalla SAS så snart som möjligt. Om du vill återkalla en användar Delegerings-SAS ska du återkalla användar Delegerings nyckeln så att alla signaturer som är associerade med den här nyckeln blir snabbt Om du vill återkalla en tjänst-SAS som är associerad med en lagrad åtkomst princip kan du ta bort den lagrade åtkomst principen, byta namn på principen eller ändra förfallo tiden till en tid som redan har passerat. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).  | - |
| Om en tjänst-SAS inte är associerad med en lagrad åtkomst princip anger du förfallo tiden till en timme eller mindre | En tjänst-SAS som inte är associerad med en lagrad åtkomst princip kan inte återkallas. Därför bör du begränsa förfallo tiden så att SAS är giltig i en timme eller mindre. | - |
| Inaktivera anonym offentlig Läs behörighet till behållare och blobbar | Anonym offentlig Läs behörighet till en behållare och dess blobbar beviljar skrivskyddad åtkomst till dessa resurser till alla klienter. Undvik att aktivera offentlig Läs behörighet om inte ditt scenario kräver det. Information om hur du inaktiverar anonym offentlig åtkomst för ett lagrings konto finns i [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md).  | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Konfigurera den minsta version av Transport Layer Security (TLS) som krävs för ett lagrings konto.  | Kräv att klienter använder en säkrare version av TLS för att göra förfrågningar mot ett Azure Storage konto genom att konfigurera den lägsta versionen av TLS för det kontot. Mer information finns i [Konfigurera minsta nödvändiga version av Transport Layer Security (TLS) för ett lagrings konto](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)| - |
| Aktivera alternativet för **säker överföring som krävs** på alla dina lagrings konton | När du aktiverar alternativet för **säker överföring krävs** måste alla begär Anden som görs mot lagrings kontot äga rum via säkra anslutningar. Förfrågningar som görs över HTTP kommer att Miss klar. Mer information finns i [Kräv säker överföring i Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | [Ja](../../security-center/security-center-remediate-recommendations.md) |
| Aktivera brandväggsregler | Konfigurera brand Väggs regler för att begränsa åtkomsten till ditt lagrings konto till begär Anden som kommer från angivna IP-adresser eller intervall, eller från en lista över undernät i ett Azure-Virtual Network (VNet). Mer information om hur du konfigurerar brand Väggs regler finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | - |
| Tillåt att betrodda Microsoft-tjänster har åtkomst till lagrings kontot | Att aktivera brand Väggs regler för ditt lagrings konto blockerar inkommande begär Anden om data som standard, om inte begär Anden kommer från en tjänst som körs i ett Azure-Virtual Network (VNet) eller från tillåtna offentliga IP-adresser. Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare. Du kan tillåta förfrågningar från andra Azure-tjänster genom att lägga till ett undantag för att tillåta betrodda Microsoft-tjänster att komma åt lagrings kontot. Mer information om hur du lägger till ett undantag för betrodda Microsoft-tjänster finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).| - |
| Använda privata slutpunkter | En privat slut punkt tilldelar en privat IP-adress från ditt Azure-Virtual Network (VNet) till lagrings kontot. Den skyddar all trafik mellan ditt VNet och lagrings kontot via en privat länk. Mer information om privata slut punkter finns i [ansluta privat till ett lagrings konto med hjälp av den privata Azure-slutpunkten](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Använda VNet-tjänstens Taggar | En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. Mer information om service märken som stöds av Azure Storage finns i [Översikt över Azure Service-Taggar](../../virtual-network/service-tags-overview.md). En själv studie kurs som visar hur du använder service märken för att skapa utgående nätverks regler finns i [begränsa åtkomsten till PaaS-resurser](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Begränsa nätverks åtkomst till vissa nätverk | Att begränsa nätverks åtkomsten till nätverk som är värd för klienter som kräver åtkomst minskar exponeringen för dina resurser till nätverks attacker. | [Ja](../../security-center/security-center-sql-service-recommendations.md) |
| Konfigurera inställningar för nätverksroutning | Du kan konfigurera inställningar för nätverks routning för ditt Azure Storage-konto om du vill ange hur nätverks trafik dirigeras till ditt konto från klienter via Internet med hjälp av Microsofts globala nätverk eller Internet-routning. Mer information finns i [Konfigurera inställningar för nätverks routning för Azure Storage](../common/network-routing-preference.md). | - |

## <a name="loggingmonitoring"></a>Loggning/övervakning

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Spåra hur begär Anden auktoriseras | Aktivera Azure Storage loggning för att spåra hur varje begäran som görs mot Azure Storage auktoriserades. Loggarna anger om en begäran har gjorts anonymt, genom att använda en OAuth 2,0-token med hjälp av delad nyckel eller genom att använda en signatur för delad åtkomst (SAS). Mer information finns i [övervaka Azure Blob Storage med Azure Monitor](monitor-blob-storage.md) eller [Azure Storage Analytics-loggning med klassisk övervakning](../common/storage-analytics-logging.md). | - |
| Konfigurera aviseringar i Azure Monitor | Konfigurera logg aviseringar för att utvärdera resurs loggar med en angiven frekvens och utlösa en avisering baserat på resultaten. Mer information finns i [logg aviseringar i Azure Monitor](../../azure-monitor/platform/alerts-unified-log.md). | - |

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-säkerhet](../../security/index.yml)
- [Skydda utvecklings dokumentationen](../../security/develop/index.yml).