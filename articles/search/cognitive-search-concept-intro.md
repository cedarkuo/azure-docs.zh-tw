---
title: AI 擴充簡介
titleSuffix: Azure Cognitive Search
description: 內容解壓縮、自然語言處理（NLP）和影像處理是用來在 Azure 認知搜尋索引中，使用預先定義的認知技能和自訂 AI 演算法來建立可搜尋的內容。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283016"
---
# <a name="getting-started-with-ai-enrichment"></a>AI 擴充入門

AI 擴充是 Azure 認知搜尋索引編制的功能，可用來從影像、blob 和其他非結構化資料來源中解壓縮文字。 擴充和解壓縮可讓您的內容在[索引](search-what-is-an-index.md)或[知識存放區](knowledge-store-concept-intro.md)中更容易搜尋。 系統會使用附加至索引管線的*認知技能*來執行提取和擴充。 服務內建的認知技能可分為下列幾個類別： 

+ **自然語言處理**技術，包括[實體辨識](cognitive-search-skill-entity-recognition.md)、[語言偵測](cognitive-search-skill-language-detection.md)、[關鍵片語擷取](cognitive-search-skill-keyphrases.md)、文字操作、[情感偵測](cognitive-search-skill-sentiment.md)和 [PII 偵測](cognitive-search-skill-pii-detection.md)。 有了這些技能，非結構化的文字會對應為索引中的可搜尋且可篩選的欄位。

+ **影像處理**技能包括[光學字元辨識（OCR）](cognitive-search-skill-ocr.md)和[視覺功能](cognitive-search-skill-image-analysis.md)的識別，例如臉部偵測、影像轉譯、影像辨識（著名的人和地標）或影像方向等屬性。 這些技能會建立影像內容的文字標記法，讓它可使用 Azure 認知搜尋的查詢功能加以搜尋。

![擴充管線圖表](./media/cognitive-search-intro/cogsearch-architecture.png "擴充管線概觀")

Azure 認知搜尋中的認知技能是以認知服務 API：[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)和[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)中預先定型的機器學習模型為基礎。 

在資料擷取階段中會套用自然語言和影像處理，且其結果會在 Azure 認知搜尋服務的可搜尋索引中成為文件撰寫的一部分。 資料會作為 Azure 資料集的來源，然後使用您所需的[內建技能](cognitive-search-predefined-skills.md)透過索引管線推送出去。 其架構是可延伸的，因此如果內建技能不敷使用，您可以建立及附加[自訂技能](cognitive-search-create-custom-skill-example.md)，以整合自訂處理。 其範例包括以特定領域為目標 (例如金融、科學出版品或醫藥) 的自訂實體模組或文件分類器。

## <a name="when-to-use-ai-enrichment"></a>使用 AI 擴充的時機

如果您的原始內容是非結構化文字、影像內容或需要語言偵測和翻譯的內容，您應該考慮使用內建認知技能。 透過內建認知技能來套用 AI，可將此內容解除鎖定，在您的搜尋和資料科學應用程式中增加其價值和效用。 

此外，如果您有想要整合到管線中的開放原始碼、第三方或第一方程式碼，可以考慮新增自訂技能。 識別各種檔案類型之明顯特性的分類模型會屬於此類別，但可使用任何將值新增至內容的套件。

### <a name="more-about-built-in-skills"></a>深入了解內建技能

使用內建技能組合的[技能集](cognitive-search-defining-skillset.md)非常適合下列應用程式案例：

+ 想要使其成為可供全文檢索搜尋的掃描文件 (JPEG)。 您可以連結光學字元辨識 (OCR) 技能以識別、擷取和內嵌 JPEG 檔案中的文字。

+ 結合了影像和文字的 PDF。 在索引編制期間，可以在不使用擴充步驟的情況下擷取 PDF 中的文字，但新增影像和自然語言處理往往能夠產生比標準索引編制程序還要好的結果。

+ 想要對其套用語言偵測和 (可能的話) 文字轉譯的多語言內容。

+ 非結構化或半結構化的文件，其含有具有內在意義的內容或隱藏在較大型文件中的內容。 

  尤其是 Blob 往往會包含封裝到單一「欄位」的大型內容主體。 藉由將影像和自然語言處理技能連結至索引子，您可以建立現存於原始內容但又不會呈現為相異欄位的新資訊。 某些立即可用、對您有所幫助的內建認知技能：關鍵片語擷取、情感分析和實體辨識 (人員、組織和位置)。

  此外，內建技能也可用來透過文字分割、合併和成形作業來重新建構內容。

### <a name="more-about-custom-skills"></a>深入了解自訂技能

自訂技能可支援更複雜的案例 (例如辨識表單) 或使用您在[自訂技能 Web 介面](cognitive-search-custom-skill-interface.md)中提供及包裝的模型來進行自訂實體偵測。 自訂技能的幾個範例包括[表單辨識器](/azure/cognitive-services/form-recognizer/overview)、[Bing 實體搜尋 API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example) 的整合，以及[自訂實體辨識](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。


## <a name="steps-in-an-enrichment-pipeline"></a>擴充管線中的步驟

擴充管線是以[*索引子*](search-indexer-overview.md)為基礎。 索引子會根據索引和資料來源之間的欄位對欄位對應填入索引，以進行檔破解。 現在已附加至索引子的技能，會根據您定義的技能集來攔截和擴充檔。 完成索引編製後，您即可使用 [Azure 認知搜尋服務所支援的所有查詢類型](search-query-overview.md)，透過搜尋要求來存取內容。  如果您不熟悉索引子，本節將引導您逐步完成相關步驟。

### <a name="step-1-connection-and-document-cracking-phase"></a>步驟1：連接和檔破解階段

在管線開始時，您會有非結構化文字或非文字內容（例如影像、掃描的檔或 JPEG 檔案）。 資料必須存在於可由索引子存取的 Azure 資料儲存體服務中。 索引子可以「萃取」來源文件，以從來源資料中擷取文字。 檔破解是在編制索引期間，從非文字來源解壓縮或建立文字內容的過程。

![文件萃取階段](./media/cognitive-search-intro/document-cracking-phase-blowup.png "文件萃取")

 支援的來源包括 Azure Blob 儲存體、Azure 資料表儲存體、Azure SQL Database 和 Azure Cosmos DB。 以文字為基礎的內容可以從下列檔案類型中擷取：PDF、Word、PowerPoint、CSV 檔案。 如需完整的清單，請參閱[支援的格式](search-howto-indexing-azure-blob-storage.md#supported-document-formats)。 索引編製會從小型、具代表性的資料集展開，然後再隨著解決方案的成熟而逐漸補強。

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>步驟2：認知技能和擴充階段

擴充是利用執行不可部分完成作業的*認知技能*來執行。 例如，一旦您已破解 PDF，您就可以套用實體辨識、語言偵測或關鍵字組解壓縮，在您的索引中產生原本無法在來源中使用的新欄位。 技能的集合會統合用於您的管線中，我們稱之為*技能集*。  

![擴充階段](./media/cognitive-search-intro/enrichment-phase-blowup.png "擴充階段")

技能集以您所提供並連線至技能集的[內建認知技能](cognitive-search-predefined-skills.md)或[自訂技能](cognitive-search-create-custom-skill-example.md)為基礎。 技能集可以是基本或高度複雜的，而且不但會決定處理類型，作業的順序也取決於它。 技能集加上定義為索引子一部分的欄位對應，即可完整指定擴充管線。 如需關於彙整前述各項組件的詳細資訊，請參閱[定義技能集](cognitive-search-defining-skillset.md)。

就內部而言，管線會產生擴充文件的集合。 您可以決定擴充文件的哪些部分應對應至搜尋索引中可編製索引的欄位。 例如，如果您套用關鍵字組的提取和實體辨識技能，這些新的欄位會成為擴充檔的一部分，而且可以對應至索引上的欄位。 若要深入了解輸入/輸出格式，請參閱[註解](cognitive-search-concept-annotations-syntax.md)。

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>新增 knowledgeStore 元素以儲存擴充

[搜尋 REST api-version=2019-05-06-Preview](search-api-preview.md) 可利用提供 Azure 儲存體連線的 `knowledgeStore` 定義，以及描述擴充儲存方式的投影，來擴展技能集。 這是您的索引以外的其他功能。 在標準 AI 管線中，擴充的文件是暫時性的，只會在編製索引期間使用，其後即捨棄。 透過知識存放區，擴充的文件得以保留。 如需詳細資訊，請參閱[知識存放區 (預覽版)](knowledge-store-concept-intro.md)。

### <a name="step-3-search-index-and-query-based-access"></a>步驟3：搜尋索引和以查詢為基礎的存取

處理完成後，您即具有由擴充文件組成、可在 Azure 認知搜尋服務中進行全文檢索搜尋的搜尋索引。 [查詢索引](search-query-overview.md)是開發人員和使用者存取管線所產生之擴充內容的具體方式。 

![索引搭配搜尋圖示](./media/cognitive-search-intro/search-phase-blowup.png "索引搭配搜尋圖示")

索引就像任何其他您可能為 Azure 認知搜尋服務建立的項目一樣：您可以使用自訂分析器進行補強、叫用模糊搜尋查詢、新增篩選搜尋，或試用評分設定檔以調整搜尋結果。

索引是從定義連結至特定索引的欄位、屬性和其他建構的索引結構描述產生的，例如評分設定檔和同義字對應。 在定義並填入索引後，而且會擴展，您即可以累加方式編製索引，以收取新的和更新的來源文件。 特定的修改需要完整重建。 在結構描述設計趨於穩定之前，您應使用小型資料集。 如需詳細資訊，請參閱[如何重建索引](search-howto-reindex.md)。

**檢查清單：一般工作流程**

1. 將您的 Azure 來源資料分成代表性範例子集。 索引編製會從小型、具代表性的資料集展開，然後再隨著解決方案的成熟而逐漸補強。

1. 在 Azure 認知搜尋服務中建立[資料來源物件](https://docs.microsoft.com/rest/api/searchservice/create-data-source)，以提供用來擷取資料的連接字串。

1. 透過擴充步驟建立[技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。

1. 定義[索引結構描述](https://docs.microsoft.com/rest/api/searchservice/create-index)。 *欄位*集合包含來源資料中的欄位。 您也應該清除多餘的欄位，以保存為擴充期間建立的內容產生的值。

1. 定義參考資料來源、技能集和索引的[索引子](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。

1. 在索引子內新增 *outputFieldMappings*。 這部分會將技能集的輸出 (在步驟 3 中) 對應至索引結構描述中的輸入欄位 (在步驟 4 中)。

1. 傳送您剛剛建立的*建立索引子*要求 (在要求本文中具有索引子定義的 POST 要求)，以表示 Azure 認知搜尋服務中的索引子。 此步驟說明您叫用管線以執行索引子的方式。

1. 執行查詢以評估結果，並修改程式碼以更新技能集、結構描述或索引子組態。

1. 在[重設索引子](search-howto-reindex.md)後重建管線。

## <a name="next-steps"></a>後續步驟

+ [AI 擴充的文件連結](cognitive-search-resources-documentation.md)
+ [範例：建立 AI 擴充的自訂技能（c #）](cognitive-search-create-custom-skill-example.md)
+ [快速入門：在入口網站中試用 AI 擴充逐步解說](cognitive-search-quickstart-blob.md)
+ [教學課程：瞭解 AI 擴充 Api](cognitive-search-tutorial-blob.md)
+ [知識存放區 (預覽)](knowledge-store-concept-intro.md)
+ [在 REST 中建立知識存放區](knowledge-store-create-rest.md)
+ [疑難排解秘訣](cognitive-search-concept-troubleshooting.md)
