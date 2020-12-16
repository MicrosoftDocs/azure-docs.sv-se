---
title: Verifiering av Indatatyp – Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Läs mer om verifiering av indatatyper i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: c816fbad05831c83c891c70849986b38cb7fdbeb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589550"
---
# <a name="security-frame-input-validation--mitigations"></a>Säkerhets ram: verifiering av Indatatyp | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webb program** | <ul><li>[Inaktivera XSLT-skript för alla transformeringar med obetrodda formatmallar](#disable-xslt)</li><li>[Se till att varje sida som kan innehålla innehåll som kan vara användarvänligt visas som en automatisk MIME-identifiering](#out-sniffing)</li><li>[Skärp eller inaktivera matchning av XML-enheter](#xml-resolution)</li><li>[Program som använder http.sys utföra autentisering av URL-auktorisering](#app-verification)</li><li>[Se till att lämpliga kontroller är på plats när du accepterar filer från användare](#controls-users)</li><li>[Se till att typ säkra parametrar används i webb program för data åtkomst](#typesafe)</li><li>[Använd separata modell bindnings klasser eller bindnings filter listor för att förhindra säkerhets risk för MVC-Mass tilldelning](#binding-mvc)</li><li>[Koda icke-betrodda webbutdata innan åter givning](#rendering)</li><li>[Utföra verifiering och filtrering av indatatyper för alla egenskaper för sträng typ modell](#typemodel)</li><li>[Sanerad används på formulär fält som accepterar alla tecken, t. ex. RTF-redigerare](#richtext)</li><li>[Tilldela inte DOM-element till handfat som inte har inbyggd kodning](#inbuilt-encode)</li><li>[Verifiera att alla omdirigeringar i programmet är stängda eller utförda på ett säkert sätt](#redirect-safe)</li><li>[Implementera verifiering av autentisering för alla sträng typs parametrar som accepteras av styrenhets metoder](#string-method)</li><li>[Ange tids gräns för övre gräns för reguljär uttrycks bearbetning för att förhindra DoS på grund av felaktiga reguljära uttryck](#dos-expression)</li><li>[Undvik att använda HTML. RAW i kniva vyer](#html-razor)</li></ul> | 
| **Databas** | <ul><li>[Använd inte dynamiska frågor i lagrade procedurer](#stored-proc)</li></ul> |
| **Webb-API** | <ul><li>[Se till att modell validering görs på webb-API-metoder](#validation-api)</li><li>[Implementera verifiering av autentisering för alla sträng typs parametrar som accepteras av webb-API-metoder](#string-api)</li><li>[Se till att typ säkra parametrar används i webb-API för data åtkomst](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Använd parametriserade SQL-frågor för Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Verifiering av WCF-autentisering via schema bindning](#schema-binding)</li><li>[Verifiering av WCF-inflöde via parameter kontroller](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Inaktivera XSLT-skript för alla transformeringar med obetrodda formatmallar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [XSLT-säkerhet](/previous-versions/windows/desktop/ms763800(v=vs.85)), [XsltSettings. ENABLESCRIPT-egenskap](/dotnet/api/system.xml.xsl.xsltsettings.enablescript) |
| **Steg** | XSLT stöder skript inuti formatmallar med hjälp av `<msxml:script>` elementet. Detta gör att anpassade funktioner kan användas i en XSLT-omvandling. Skriptet körs under kontexten för processen som utför transformeringen. XSLT-skript måste inaktive ras när de är i en ej betrodd miljö för att förhindra körning av obetrodd kod. *Om du använder .net:* XSLT-skript är inaktiverat som standard. Du måste dock se till att den inte uttryckligen har Aktiver ATS via `XsltSettings.EnableScript` egenskapen.|

### <a name="example"></a>Exempel 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder MSXML 6,0 är XSLT-skript inaktiverat som standard. Du måste dock se till att den inte uttryckligen har Aktiver ATS via XML DOM-AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder MSXML 5 eller lägre är XSLT-skript aktiverat som standard och du måste inaktivera det explicit. Ange egenskapen AllowXsltScript till false för XML DOM-objektet. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Se till att varje sida som kan innehålla innehåll som kan vara användarvänligt visas som en automatisk MIME-identifiering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IE8 säkerhets del V – omfattande skydd](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **Steg** | <p>För varje sida som kan innehålla innehåll som kan vara användarvänligt måste du använda HTTP-huvudet `X-Content-Type-Options:nosniff` . För att uppfylla det här kravet kan du antingen ange sidhuvuds sidan som krävs av Page för de sidor som kan innehålla innehåll som kan användas för användare eller så kan du ställa in det globalt för alla sidor i programmet.</p><p>Varje typ av fil som levereras från en webb server har en associerad [MIME-typ](https://en.wikipedia.org/wiki/Mime_type) (kallas även *innehålls typ*) som beskriver innehållets beskaffenhet (dvs. bild, text, program osv.)</p><p>Huvudet X-Content-Type-Options är ett HTTP-huvud som gör det möjligt för utvecklare att ange att deras innehåll inte ska vara MIME-sniffed. Den här rubriken är utformad för att minimera MIME-Sniffing attacker. Stöd för den här rubriken har lagts till i Internet Explorer 8 (IE8)</p><p>Endast användare av Internet Explorer 8 (IE8) kommer att ha nytta av X-Content-Type-Options. I tidigare versioner av Internet Explorer respekteras för närvarande inte huvudet X-Content-Type-Options</p><p>Internet Explorer 8 (och senare) är de enda större webbläsare som implementerar en avanmälans funktion för MIME-identifiering. Om och när andra större webbläsare (Firefox, Safari, Chrome) implementerar liknande funktioner, kommer den här rekommendationen att uppdateras så att den även inkluderar syntax för dessa webbläsare</p>|

### <a name="example"></a>Exempel
Om du vill aktivera nödvändigt sidhuvud globalt för alla sidor i programmet kan du göra något av följande: 

* Lägg till rubriken i web.config-filen om programmet finns i Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Lägg till rubriken via global Application \_ BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementera anpassad HTTP-modul 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Du kan bara aktivera den nödvändiga rubriken för specifika sidor genom att lägga till den i enskilda svar: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Skärp eller inaktivera matchning av XML-enheter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Expandering av XML-enheter](https://capec.mitre.org/data/definitions/197.html), [XML-denial of Service-attacker och försvar](/archive/msdn-magazine/2009/november/xml-denial-of-service-attacks-and-defenses), [MSXML-Säkerhetsöversikt](/previous-versions/windows/desktop/ms754611(v=vs.85)), [metod tips för att skydda MSXML-kod](/previous-versions/windows/desktop/ms759188(v=vs.85)), [NSXMLParserDelegate-protokoll referens](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [lösa externa referenser](/dotnet/standard/data/xml/resolving-external-resources) |
| **Steg**| <p>Även om den inte används ofta, finns det en funktion i XML som gör det möjligt för XML-parsern att expandera makro enheter med värden som definieras antingen i själva dokumentet eller från externa källor. Dokumentet kan till exempel definiera en entitet "företags namn" med värdet "Microsoft", så varje gång texten " &companyname; " visas i dokumentet ersätts den automatiskt med texten Microsoft. Eller så kan dokumentet definiera en entitet "MSFTStock" som refererar till en extern webb tjänst för att hämta det aktuella värdet för Microsoft stock.</p><p>När som helst " &MSFTStock; " visas i dokumentet ersätts det automatiskt med det aktuella aktie priset. Den här funktionen kan dock missbrukas för att skapa dos-villkor (Denial of Service). En angripare kan kapsla flera entiteter för att skapa ett exponentiellt expansions-XML-bomb som förbrukar allt tillgängligt minne i systemet. </p><p>Du kan också skapa en extern referens som strömmar en oändlig mängd data eller som helt enkelt låser tråden. Därför måste alla team inaktivera intern och/eller extern XML-enhets upplösning helt om programmet inte använder den eller manuellt begränsa mängden minne och tid som programmet kan använda för enhets matchning om den här funktionen är absolut nödvändig. Om inte enhets matchning krävs av ditt program inaktiverar du det. </p>|

### <a name="example"></a>Exempel
För .NET Framework-kod kan du använda följande metoder:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observera att standardvärdet för `ProhibitDtd` i `XmlReaderSettings` är sant, men att `XmlTextReader` det är falskt. Om du använder XmlReaderSettings behöver du inte ange ProhibitDtd till True explicit, men det rekommenderas av säkerhets skäl. Observera också att klassen XmlDocument tillåter enhets matchning som standard. 

### <a name="example"></a>Exempel
Om du vill inaktivera enhets matchning för XmlDocuments använder du `XmlDocument.Load(XmlReader)` överlagringen för load-metoden och anger lämpliga egenskaper i argumentet XmlReader för att inaktivera matchning, som visas i följande kod: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exempel
Om det inte går att inaktivera enhets matchning för ditt program ställer du in egenskapen XmlReaderSettings. MaxCharactersFromEntities på ett rimligt värde enligt programmets behov. Detta begränsar påverkan av potentiella attacker med exponentiell expansions DoS. Följande kod visar ett exempel på den här metoden: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exempel
Om du behöver lösa infogade entiteter, men inte behöver lösa externa entiteter, anger du egenskapen XmlReaderSettings.Xmlresolver till null. Exempel: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observera att ProhibitDTD är inställt på sant (inaktive rad DTD-bearbetning) som standard i MSXML6. För Apple OSX/iOS-kod finns det två XML-tolkare som du kan använda: NSXMLParser och libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Program som använder http.sys utföra autentisering av URL-auktorisering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Alla program som använder http.sys bör följa dessa rikt linjer:</p><ul><li>Begränsa URL-längden till högst 16 384 tecken (ASCII eller Unicode). Detta är den absoluta högsta URL-längden baserat på standard Internet Information Services (IIS) 6-inställningen. Webbplatser bör sträva efter en längd som är kortare än detta om möjligt</li><li>Använd standard-.NET Framework i/O-klasser (till exempel FileStream) eftersom dessa kommer att dra nytta av reglerna för att skapa kanoniska data i .NET FX</li><li>Bygg en lista med kända fil namn uttryckligen</li><li>Explicit avvisa kända filtyper du kommer inte att betjäna UrlScan-avslag: exe, bat, cmd, com, HTW, Ida, IDQ, HTR, IDC, SHTM [l], STM, skrivare, ini, pol, DAT-filer</li><li>Fånga följande undantag:<ul><li>System. ArgumentException (för enhets namn)</li><li>System. NotSupportedException (för data strömmar)</li><li>System. IO. FileNotFoundException (för ogiltiga undantagna fil namn)</li><li>System. IO. DirectoryNotFoundException (för ogiltig undantagen kataloger)</li></ul></li><li>Anropa *inte* Win32-fil-i/O-API: er. På en ogiltig URL returnerar användaren ett 400-fel för användaren och loggar det riktiga felet.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Se till att lämpliga kontroller är på plats när du accepterar filer från användare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Obegränsad fil uppladdning](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), [tabell för fil signering](https://www.garykessler.net/library/file_sigs.html) |
| **Steg** | <p>Överförda filer representerar en betydande risk för program.</p><p>Det första steget i många attacker är att hämta kod till systemet som ska angripas. Sedan behöver angreppet bara hitta ett sätt att hämta koden. Genom att använda en fil uppladdning kan angriparen utföra det första steget. Konsekvenserna av obegränsad fil uppladdning kan variera, inklusive fullständigt system övertag Ande, ett överbelastat fil system eller en databas, vidarebefordran till backend-system och enkel avsluts punkt.</p><p>Det beror på vad programmet gör med den överförda filen och särskilt var den lagras. Verifiering av Server sidan för fil överföringar saknas. Följande säkerhets kontroller ska implementeras för fil överförings funktioner:</p><ul><li>Fil tilläggs kontroll (endast en giltig uppsättning tillåten filtyp ska godkännas)</li><li>Maximal fil storleks gräns</li><li>Filen ska inte överföras till Webroot; platsen ska vara en katalog på en annan enhet än en systemen het</li><li>Namngivnings konventionen bör följas, så att det uppladdade fil namnet har viss slumpmässig het, så att fil överskrivning kan förhindras</li><li>Filer ska genomsökas efter virus innan de skrivs till disken</li><li>Kontrol lera att fil namnet och andra metadata (t. ex. fil Sök väg) är verifierade för skadliga tecken</li><li>Fil formatets signatur bör kontrol leras för att förhindra att en användare laddar upp en maskerad fil (t. ex. genom att ändra tillägget till txt)</li></ul>| 

### <a name="example"></a>Exempel
För den sista punkten angående verifiering av fil format, se nedanstående klass för mer information: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Se till att typ säkra parametrar används i webb program för data åtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Om du använder-parameter samlingen behandlar SQL indatamängden som ett exakt värde i stället för den körbara koden. Parameter samlingen kan användas för att genomdriva begränsningar för typ och längd på indata. Värden utanför intervallet utlöser ett undantag. Om typ-säkra SQL-parametrar inte används kan angripare kunna köra inmatnings attacker som är inbäddade i ofiltrerad inmatning.</p><p>Använd typ säkra parametrar vid konstruktion av SQL-frågor för att undvika eventuella SQL-injektering-attacker som kan uppstå med ofiltrerad inmatning. Du kan använda typ säkra parametrar med lagrade procedurer och med dynamiska SQL-uttryck. Parametrar behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrol leras också för typ och längd.</p>|

### <a name="example"></a>Exempel 
Följande kod visar hur du använder typ säkra parametrar med SqlParameterCollection när du anropar en lagrad procedur. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
I föregående kod exempel får indatavärdet inte vara längre än 11 tecken. Om data inte följer den typ eller längd som definieras av parametern, genererar SqlParameter-klassen ett undantag. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Använd separata modell bindnings klasser eller bindnings filter listor för att förhindra säkerhets risk för MVC-Mass tilldelning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Metadata-attribut](/dotnet/api/system.componentmodel.dataannotations.metadatatypeattribute), [säkerhets risk för offentliga nycklar och skydds](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)åtgärder, [fullständig vägledning för Mass tilldelning i ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [komma igång med EF med MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Steg** | <ul><li>**När bör jag titta efter problem med att publicera? –** Säkerhets risker med överbokföring kan ske på alla platser som du binder modell klasser från användarindata. Ramverk som MVC kan representera användar data i anpassade .NET-klasser, inklusive vanliga gamla CLR-objekt (POCOs). MVC fyller automatiskt i dessa modell klasser med data från begäran, vilket ger en bekväm representation för att hantera användarindata. När dessa klasser innehåller egenskaper som inte ska ställas in av användaren kan programmet vara sårbart för överboknings attacker som tillåter användar kontroll över data som programmet aldrig avsåg. Precis som MVC-modell bindning är åtkomst tekniker för databaser, till exempel objekt/Relations mappningar, som Entity Framework ofta också stöder användning av POCO-objekt för att representera databas data. Dessa data modell klasser ger samma bekvämlighet vid hantering av databas data som MVC för att hantera användarindata. Eftersom både MVC och databasen stöder liknande modeller, t. ex. POCO objekt, verkar det enkelt att återanvända samma klasser för båda syftena. Den här metoden kan inte bevara separering av problem och det är ett gemensamt område där oönskade egenskaper exponeras för modell bindning, vilket möjliggör överboknings attacker.</li><li>**Varför ska jag inte använda mina ofiltrerade databas modell klasser som parametrar i mina MVC-åtgärder?-** Eftersom MVC-modellens bindning binder vad som helst i klassen. Även om data inte visas i din vy, kan en obehörig användare skicka en HTTP-begäran med dessa data, och MVC binder det genom att din åtgärd anger att databas klassen är den form av data som den ska acceptera för användarindata.</li><li>**Varför ska jag bry mig om formen som används för modell bindning?-** Om du använder ASP.NET MVC modell binding med över breda modeller visas ett program för att skicka vidare attacker. Överbokföring kan göra det möjligt för angripare att ändra program data utöver vad utvecklaren avsåg, till exempel att åsidosätta priset för ett objekt eller säkerhets behörigheterna för ett konto. Program bör använda åtgärds specifika bindnings modeller (eller specifika tillåtna egenskaps filter listor) för att tillhandahålla ett explicit kontrakt för det som inte är betrott och som tillåts via modell bindning.</li><li>**Behöver separata bindnings modeller bara duplicera kod?-** Nej, det är en fråga om separering av problem. Om du återanvänder databas modeller i åtgärds metoder, säger du att en egenskap (eller under egenskap) i klassen kan ställas in av användaren i en HTTP-begäran. Om det inte är det du vill att MVC ska göra, behöver du en filter lista eller en separat klass form för att Visa MVC vilka data som kan komma från användarindata i stället.</li><li>Måste **jag duplicera alla mina data antecknings attribut om jag har separata bindnings modeller för användarindata? –** Inte nödvändigt vis. Du kan använda MetadataTypeAttribute i databas modell klassen för att länka till metadata i en modell bindnings klass. Observera bara att typen som refereras av MetadataTypeAttribute måste vara en delmängd av den refererande typen (den kan ha färre egenskaper, men inte fler).</li><li>**Det är omständligt att flytta data fram och tillbaka mellan inmatnings modeller och databas modeller. Kan jag bara kopiera över alla egenskaper med hjälp av reflektion? -** Ja. De enda egenskaper som visas i bindnings modellerna är de som du har fastställt vara säkra för användarindata. Det finns ingen säkerhets orsak som förhindrar att du använder reflektion för att kopiera över alla egenskaper som finns gemensamt mellan dessa två modeller.</li><li>**Vad händer om [BIND (exclude = "â &euro; ¦")]. Kan jag använda det i stället för att ha separata bindnings modeller? –** Den här metoden rekommenderas inte. Använda [BIND (exclude = "â &euro; ¦")] innebär att alla nya egenskaper kan bindas som standard. När en ny egenskap läggs till, finns det ett extra steg att komma ihåg att skydda dem, i stället för att designen ska vara säker som standard. Beroende på utvecklare kontrollerar den här listan varje gång en egenskap läggs till är riskfylld.</li><li>**Är [BIND (include = "â &euro; ¦")] användbart för redigerings åtgärder?-** nej. [BIND (include = "â &euro; ¦")] är bara lämpligt för infognings åtgärder (lägga till nya data). Använd en annan metod, som att ha separata bindnings modeller eller skicka en explicit lista över tillåtna egenskaper till UpdateModel eller TryUpdateModel, för åtgärder med UPPDATERINGs åtgärder (att ändra befintliga data). Om du lägger till attributet [BIND (include = "â &euro; ¦")] i en Edit-åtgärd innebär det att MVC skapar en objekt instans och bara anger de egenskaper som visas, och lämnar alla andra standardvärden. När data har sparats ersätter den den befintliga entiteten och återställer värdena för alla utelämnade egenskaper till standardinställningarna. Om t. ex. IsAdmin utelämnades från ett [bind-attribut (include = "â &euro; ¦")] i en Edit-åtgärd, återställs alla användare vars namn har redigerats via den här åtgärden till IsAdmin = false (alla redigerade användare skulle förlora administratörs status). Om du vill förhindra uppdateringar av vissa egenskaper kan du använda någon av de andra metoderna ovan. Observera att vissa versioner av MVC-verktyg genererar styrenhets klasser med [BIND (include = "â &euro; ¦")] vid redigerings åtgärder och innebär att borttagning av en egenskap från den listan förhindrar överboknings attacker. Men enligt beskrivningen ovan fungerar inte den här metoden som avsett och kommer istället att återställa data i de utelämnade egenskaperna till standardvärdena.</li><li>**För Create-åtgärder finns det några varningar som använder [BIND (include = "â &euro; ¦")] i stället för separata bindnings modeller?-** Ja. Den första metoden fungerar inte för redigerings scenarier, vilket kräver att du underhåller två separata metoder för att minimera alla problem vid överbokföring. För det andra förväntar sig separata bindnings modeller separering av problem mellan den form som används för användarindata och den form som används för persistence, något [BIND (include = "â &euro; ¦")] har inte gjort det. För det tredje, Observera att [BIND (include = "â &euro; ¦")] bara kan hantera toppnivå egenskaper. du kan inte bara tillåta delar av underordnade egenskaper (t. ex. "details.name") i attributet. Slutligen, och kanske viktigast, med [BIND (include = "â &euro; ¦")] lägger till ett extra steg som måste läggas till varje gång klassen används för modell bindning. Om en ny åtgärds metod binder till data-klassen direkt och glömmer att inkludera ett [BIND (include = "â &euro; ¦")]-attribut kan det vara sårbart för överboknings attacker, så att [BIND (inklusive = "â &euro; ¦")-metoden är något mindre säkert som standard. Om du använder [BIND (inklusive = "â &euro; ¦")], bör du alltid vara noga med att komma ihåg att ange det varje gång dina data klasser visas som åtgärds metod parametrar.</li><li>**För Create-åtgärder, vad gäller att placera attributet [BIND (include = "â &euro; ¦")] i själva modell klassen? Undviker den här metoden att du inte behöver komma ihåg att placera attributet i varje åtgärds metod? –** Den här metoden fungerar i vissa fall. Genom att använda [BIND (include = "â &euro; ¦")] i själva modell typen (i stället för på åtgärds parametrar som använder den här klassen) behöver du inte komma ihåg att ta med attributet [BIND (include = "â &euro; ¦")] i varje åtgärds metod. Genom att använda attributet direkt i klassen skapas ett separat arean av den här klassen för modell bindnings syfte. Den här metoden tillåter dock bara en modell bindnings form per modell klass. Om en åtgärds metod behöver tillåta modell bindning för ett fält (till exempel en endast administratörs åtgärd som uppdaterar användar roller) och andra åtgärder som behöver för att förhindra modell bindning för det här fältet fungerar inte den här metoden. Varje klass kan bara ha en modell bindnings form. om olika åtgärder behöver olika modell bindnings former måste de representera dessa separata former med antingen separata modell bindnings klasser eller separata [BIND (include = "â &euro; ¦")] attribut på åtgärds metoderna.</li><li>**Vad är bindnings modeller? Är de samma sak som att Visa modeller? –** Det här är två relaterade begrepp. Termen bindnings modell refererar till en modell klass som används i en åtgärds parameter lista (den form som skickades från MVC-modellens bindning till åtgärds metoden). Termen View-modellen refererar till en modell klass som skickas från en åtgärds metod till en vy. Att använda en vy-speciell modell är en vanlig metod för att skicka data från en åtgärds metod till en vy. Den här formen är ofta lämplig för modell bindning och termen vy modell kan användas för att referera till samma modell som används på båda platserna. För att vara exakt, är den här proceduren specifik för bindnings modeller, som fokuserar på den form som skickas till åtgärden, vilket är vad som är viktigt för Mass tilldelnings syfte.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Koda icke-betrodda webbutdata innan åter givning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generiska, webb formulär, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Förhindra skript för Cross-Site i ASP.net](/previous-versions/msp-n-p/ff649310(v=pandp.10)), [Cross-Site Scripting](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) förhindra lathund blad](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Steg** | Skript körning mellan webbplatser (vanligt vis förkortat som XSS) är en angrepps vektor för onlinetjänster eller program/komponenter som använder sig av ininformation från webben. XSS-sårbarheter kan låta en angripare köra skript på en annan användares dator via ett sårbart webb program. Skadliga skript kan användas till att stjäla cookies och på annat sätt manipulera en skadelidandes dator via Java Script. XSS förhindras genom att verifiera användarindata, se till att det är korrekt utformat och kodning innan det återges på en webb sida. Verifiering av indata och utdata kan göras med hjälp av webb skydds bibliotek. För förvaltad kod (C \# , VB.net osv.) använder du en eller flera lämpliga kodnings metoder från webb skydds biblioteket (stöldskydd), beroende på i vilken kontext användar indata visas:| 

### <a name="example"></a>Exempel

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Utföra verifiering och filtrering av indatatyper för alla egenskaper för sträng typ modell

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generisk, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Lägga till validering](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [validera modell data i ett MVC-program](/previous-versions/dd410404(v=vs.90)), [guid-principer för dina ASP.NET MVC-program](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Steg** | <p>Alla indataparametrar måste verifieras innan de används i programmet för att säkerställa att programmet skyddas mot indata från skadlig användare. Verifiera indatavärdena med hjälp av reguljära uttrycks validering på Server sidan med en validerings strategi för tillåten lista. Indata som inte är sanerade eller parametrar som skickas till metoderna kan orsaka sårbarheter för kod inmatning.</p><p>För webb program kan start punkter även innehålla formulär fält, frågesträngar, cookies, HTTP-huvuden och webb tjänst parametrar.</p><p>Följande verifierings kontroller måste utföras vid modell bindningen:</p><ul><li>Modell egenskaperna ska kommenteras med reguljärt uttryck-anteckning, för att acceptera tillåtna tecken och maximalt tillåten längd</li><li>Kontroll enhetens metoder bör utföra ModelState giltighet</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Sanerad används på formulär fält som accepterar alla tecken, t. ex. RTF-redigerare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Koda osäkra indatatyper](/previous-versions/msp-n-p/ff647397(v=pandp.10)#paght000003_step3), [HTML-språkspråks](https://github.com/mganss/HtmlSanitizer) skydd |
| **Steg** | <p>Identifiera alla statiska koder som du vill använda. En vanlig metod är att begränsa formateringen till säkra HTML-element, t `<b>` . ex. (fetstil) och `<i>` (kursiv).</p><p>Innan du skriver data ska HTML-kodas. Detta gör skadlig skript säker genom att orsaka att den hanteras som text, inte som körbar kod.</p><ol><li>Inaktivera verifiering av ASP.NET-begäran genom att lägga till attributet ValidateRequest = "false" i \@ Page-direktivet</li><li>Koda inmatade strängar med metoden HtmlEncode</li><li>Använd en StringBuilder och anropa metoden replace för att selektivt ta bort kodningen för de HTML-element som du vill tillåta</li></ol><p>Sidan-i referenserna inaktiverar verifiering av ASP.NET-begäran genom inställningen `ValidateRequest="false"` . IT-HTML – kodar indatamängden och selektivt tillåter `<b>` och `<i>` ett .NET-bibliotek för HTML-språkspråkning kan också användas.</p><p>HtmlSanitizer är ett .NET-bibliotek för rensning av HTML-fragment och dokument från konstruktioner som kan leda till XSS-attacker. Den använder AngleSharp för att parsa, manipulera och återge HTML och CSS. HtmlSanitizer kan installeras som ett NuGet-paket och användarindata kan skickas via relevanta HTML-eller CSS-språkmetoder, i tillämpliga fall, på Server sidan. Observera att den sanerade som säkerhets kontroll endast bör betraktas som ett sista alternativ.</p><p>Verifiering av indata och utdata av utdata betraktas som bättre säkerhets kontroller.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Tilldela inte DOM-element till handfat som inte har inbyggd kodning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Många JavaScript-funktioner gör inte kodning som standard. När du tilldelar icke-betrodda indatamängder till DOM-element via sådana funktioner kan det resultera i XSS-körningar (Cross Site script).| 

### <a name="example"></a>Exempel
Följande är osäkra exempel: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Använd inte. `innerHtml` Använd i stället `innerText` . På samma sätt använder du i stället för `$("#elm").html()``$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Verifiera att alla omdirigeringar i programmet är stängda eller utförda på ett säkert sätt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [OAuth 2,0 Authorization Framework – öppna omdirigeringar](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Steg** | <p>Program design som kräver omdirigering till en plats som användaren tillhandahåller måste begränsa de möjliga omdirigerings målen till en fördefinierad "säker" lista över platser eller domäner. Alla omdirigeringar i programmet måste vara stängda/säkra.</p><p>Gör så här:</p><ul><li>Identifiera alla omdirigeringar</li><li>Implementera lämplig minskning för varje omdirigering. Lämpliga åtgärder innefattar lista över tillåtna omdirigerade användare eller användar bekräftelse. Om en webbplats eller tjänst med en öppen omdirigerings sårbarhet använder Facebook/OAuth/OpenID Identity providers kan en angripare stjäla en användares inloggningssession och personifiera den användaren. Detta är en överordnad risk när du använder OAuth, som dokumenteras i RFC 6749 "OAuth 2,0 Authorization Framework", avsnitt 10,15 "Öppna omdirigeringar". användarnas autentiseringsuppgifter kan komprometteras av spjut phishing-attacker med öppna omdirigeringar</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementera verifiering av autentisering för alla sträng typs parametrar som accepteras av styrenhets metoder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generisk, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Validera modell data i ett MVC-program](/previous-versions/dd410404(v=vs.90)), [GUID-principer för dina ASP.NET MVC-program](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Steg** | För metoder som bara accepterar primitiv datatyp, och inte modeller som argument, bör inmatnings verifiering med reguljärt uttryck göras. Här är regex. IsMatch ska användas med ett giltigt regex-mönster. Om indatatypen inte matchar det angivna reguljära uttrycket bör kontrollen inte fortsätta vidare och en lämplig varning om verifierings fel bör visas.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Ange tids gräns för övre gräns för reguljär uttrycks bearbetning för att förhindra DoS på grund av felaktiga reguljära uttryck

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generiska, webb formulär, MVC5, MVC6  |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [DefaultRegexMatchTimeout-egenskap](/dotnet/api/system.web.configuration.httpruntimesection.defaultregexmatchtimeout) |
| **Steg** | För att se till att denial of Service-attacker mot dåligt skapade reguljära uttryck, som orsakar massor av spår, anger du den globala standard tids gränsen. Om bearbetnings tiden tar längre tid än den definierade övre gränsen, orsakar det ett timeout-undantag. Om inget är konfigurerat skulle tids gränsen vara oändlig.| 

### <a name="example"></a>Exempel
Följande konfiguration kommer till exempel att utlösa en RegexMatchTimeoutException, om bearbetningen tar mer än 5 sekunder: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Undvik att använda HTML. RAW i kniva vyer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Steg | ASP.NET webb sidor (kniv) utför automatisk HTML-kodning. Alla strängar som skrivs med inbäddad kod Nuggets (@ Blocks) är automatiskt HTML-kodade. Men när `HtmlHelper.Raw` metoden anropas returnerar den markering som inte är HTML-kodad. Om `Html.Raw()` Hjälp metoden används kringgås det automatiska kodnings skyddet som kniven ger.|

### <a name="example"></a>Exempel
Följande är ett osäkert exempel: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Använd inte `Html.Raw()` om du inte behöver Visa pålägg. Den här metoden utför inte utgående kodning implicit. Använd andra ASP.NET-hjälpfiler, t. ex., `@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Använd inte dynamiska frågor i lagrade procedurer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>En SQL-injektering utnyttjar sårbarheter i indata-verifiering för att köra godtyckliga kommandon i databasen. Det kan inträffa när ditt program använder indata för att konstruera dynamiska SQL-uttryck för att komma åt databasen. Det kan också inträffa om din kod använder lagrade procedurer som har överförts strängar som innehåller rå indata från användaren. Angriparen kan köra godtyckliga kommandon i databasen med hjälp av SQL-injektering-attacken. Alla SQL-uttryck (inklusive SQL-instruktionerna i lagrade procedurer) måste vara parameterstyrda. Parametriserade SQL-uttryck kommer att acceptera tecken som har en särskild betydelse för SQL (som enkla citat tecken) utan problem eftersom de är starkt skrivna. |

### <a name="example"></a>Exempel
Följande är ett exempel på en osäker dynamisk lagrad procedur: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Exempel
Följande är samma lagrade procedur som implementeras på ett säkert sätt: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Se till att modell validering görs på webb-API-metoder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Modell validering i ASP.NET webb-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | När en klient skickar data till ett webb-API är det obligatoriskt att verifiera data innan du utför någon bearbetning. För ASP.NET webb-API: er som accepterar modeller som indata använder du data anteckningar i modeller för att ange verifierings regler för modellens egenskaper.|

### <a name="example"></a>Exempel
Följande kod visar samma: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Exempel
I åtgärds-metoden för API-styrenheterna måste du kontrol lera att modellen är giltig enligt följande: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementera verifiering av autentisering för alla sträng typs parametrar som accepteras av webb-API-metoder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Generisk, MVC 5, MVC 6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Validera modell data i ett MVC-program](/previous-versions/dd410404(v=vs.90)), [GUID-principer för dina ASP.NET MVC-program](/archive/msdn-magazine/2009/brownfield/extreme-asp-net-guiding-principles-for-your-asp-net-mvc-applications) |
| **Steg** | För metoder som bara accepterar primitiv datatyp, och inte modeller som argument, bör inmatnings verifiering med reguljärt uttryck göras. Här är regex. IsMatch ska användas med ett giltigt regex-mönster. Om indatatypen inte matchar det angivna reguljära uttrycket bör kontrollen inte fortsätta vidare och en lämplig varning om verifierings fel bör visas.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Se till att typ säkra parametrar används i webb-API för data åtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Om du använder-parameter samlingen behandlar SQL indatamängden som ett exakt värde i stället för den körbara koden. Parameter samlingen kan användas för att genomdriva begränsningar för typ och längd på indata. Värden utanför intervallet utlöser ett undantag. Om typ-säkra SQL-parametrar inte används kan angripare kunna köra inmatnings attacker som är inbäddade i ofiltrerad inmatning.</p><p>Använd typ säkra parametrar vid konstruktion av SQL-frågor för att undvika eventuella SQL-injektering-attacker som kan uppstå med ofiltrerad inmatning. Du kan använda typ säkra parametrar med lagrade procedurer och med dynamiska SQL-uttryck. Parametrar behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrol leras också för typ och längd.</p>|

### <a name="example"></a>Exempel
Följande kod visar hur du använder typ säkra parametrar med SqlParameterCollection när du anropar en lagrad procedur. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
I föregående kod exempel får indatavärdet inte vara längre än 11 tecken. Om data inte följer den typ eller längd som definieras av parametern, genererar SqlParameter-klassen ett undantag. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Använd parametriserade SQL-frågor för Cosmos DB

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Vi presenterar SQL-Parameterisering i Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Steg** | Även om Azure Cosmos DB bara har stöd för skrivskyddade frågor, kan SQL-injektering fortfarande användas om frågor skapas genom att sammanfoga med användarindata. Det kan vara möjligt för en användare att få åtkomst till data som de inte ska ha åtkomst till i samma samling genom att ta skadliga SQL-frågor. Använd parametriserade SQL-frågor om frågor konstrueras baserat på användarindata. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>Verifiering av WCF-autentisering via schema bindning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff647820(v=pandp.10)) |
| **Steg** | <p>Brist på verifiering leder till olika typ inmatnings attacker.</p><p>Meddelande verifieringen representerar en försvars linje vid skyddet av ditt WCF-program. Med den här metoden verifierar du meddelanden med hjälp av scheman för att skydda WCF-tjänstens åtgärder från angrepp av en skadlig klient. Verifiera alla meddelanden som tas emot av klienten för att skydda klienten från angrepp av en skadlig tjänst. Med meddelande verifiering kan du verifiera meddelanden när åtgärder förbrukar meddelande kontrakt eller data kontrakt, som inte kan göras med parameter validering. Med meddelande validering kan du skapa validerings logik i scheman, vilket ger större flexibilitet och minskar utvecklings tiden. Scheman kan återanvändas mellan olika program i organisationen och skapa standarder för data representation. Dessutom kan du använda meddelande valideringen för att skydda åtgärder när de använder mer komplexa data typer som rör kontrakt som representerar affärs logik.</p><p>För att utföra meddelande valideringen skapar du först ett schema som representerar driften av tjänsten och de data typer som används av dessa åtgärder. Sedan kan du skapa en .NET-klass som implementerar en anpassad klient meddelande kontroll och en anpassad avsändares meddelande kontroll för att validera meddelanden som skickas/tas emot till/från tjänsten. Därefter implementerar du ett anpassat slut punkts beteende för att aktivera meddelande validering på både klienten och tjänsten. Slutligen implementerar du ett anpassat konfigurations element i klassen som gör att du kan exponera det utökade anpassade slut punkts beteendet i konfigurations filen för tjänsten eller klienten "</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>Verifiering av WCF-inflöde via parameter kontroller

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff647875(v=pandp.10)) |
| **Steg** | <p>Indata och data verifiering representerar en viktig försvars linje vid skyddet av ditt WCF-program. Du bör verifiera alla parametrar som exponeras i WCF-tjänstens åtgärder för att skydda tjänsten mot angrepp från en skadlig klient. Däremot bör du även validera alla retur värden som tas emot av klienten för att skydda klienten mot angrepp av en skadlig tjänst</p><p>WCF tillhandahåller olika utöknings punkter som gör att du kan anpassa WCF runtime-beteendet genom att skapa anpassade tillägg. Meddelande kontroller och parameter kontroller är två utöknings bara metoder som används för att få bättre kontroll över data som skickas mellan en klient och en tjänst. Du bör använda parameter kontroller för verifiering av indatatyper och endast använda meddelande kontrollanter när du behöver kontrol lera hela meddelandet som flödar in och ut ur en tjänst.</p><p>Om du vill utföra verifiering av autentisering skapar du en .NET-klass och implementerar en anpassad parameter kontroll för att verifiera parametrar för åtgärder i din tjänst. Du implementerar sedan ett anpassat slut punkts beteende för att aktivera verifiering på både klienten och tjänsten. Slutligen implementerar du ett anpassat konfigurations element i klassen som gör att du kan exponera det utökade anpassade slut punkts beteendet i konfigurations filen för tjänsten eller klienten</p>|