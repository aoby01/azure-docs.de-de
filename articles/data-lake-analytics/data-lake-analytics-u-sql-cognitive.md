---
title: Verwenden von kognitiven U-SQL-Funktionen in Azure Data Lake Analytics | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Intelligenz kognitiver Funktionen in U-SQL einsetzen.
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
translationtype: Human Translation
ms.sourcegitcommit: 21b4d574705d589406f50cac106a47ada71d24cd
ms.openlocfilehash: 596459e25f8ad072a55ad45a2f444c71b27fd60c


---

# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Tutorial: Erste Schritte mit den kognitiven Funktionen von U-SQL

Kognitive Funktionen für U-SQL ermöglichen Entwicklern den Einsatz von Intelligenz in ihren Big Data-Programmen. Der Gesamtprozess ist einfach:

* Verwenden der REFERENCE ASSEMBLY-Anweisung zum Aktivieren der kognitiven Funktionen für das U-SQL-Skript
* Verwenden des PROCESS-Vorgangs zum Verwenden der kognitiven Funktionen 

## <a name="imaging-scenarios"></a>Bildverarbeitungsszenarien

### <a name="a-simple-example-image-tagging"></a>Ein einfaches Beispiel: Bildmarkierung

Das folgende Beispiel zeigt eine durchgängige Verwendung der Bildverarbeitungsfunktionen zum Erkennen von Objekten in Bildern.

    REFERENCE ASSEMBLY ImageCommon;
    REFERENCE ASSEMBLY FaceSdk;
    REFERENCE ASSEMBLY ImageEmotion;
    REFERENCE ASSEMBLY ImageTagging;
    REFERENCE ASSEMBLY ImageOcr;

    @imgs =
        EXTRACT FileName string, ImgData byte[]
        FROM @"/images/{FileName:*}.jpg"
        USING new Cognition.Vision.ImageExtractor();

    // Extract the number of objects on each image and tag them 
    @objects =
        PROCESS @imgs 
        PRODUCE FileName,
                NumObjects int,
                Tags string
        READONLY FileName
        USING new Cognition.Vision.ImageTagger();


### <a name="extract-emotions-from-human-faces"></a>Extrahieren von Emotionen aus menschlichen Gesichtern 

    @emotions =
        PROCESS @imgs
        PRODUCE FileName string,
                NumFaces int,
                Emotion string
        READONLY FileName
        USING new Cognition.Vision.EmotionAnalyzer();

### <a name="estimate-age-and-gender-for-human-faces"></a>Schätzen des Alters und Geschlecht anhand menschlicher Gesichter

    @faces = 
            PROCESS @imgs
            PRODUCE FileName,
                    NumFaces int,
                    FaceAge string,
                    FaceGender string
            READONLY FileName
            USING new Cognition.Vision.FaceDetector();

### <a name="detect-text-in-images-ocr"></a>Erkennen von Text in Bildern (OCR)

    @ocrs =
            PROCESS @imgs
            PRODUCE FileName,
                    Text string
            READONLY FileName
            USING new Cognition.Vision.OcrExtractor();

## <a name="text-scenarios"></a>Textszenarien

### <a name="input-data"></a>Eingabedaten

Angenommen, eine Eingabe besteht aus „Krieg und Frieden“ von Leo Tolstoy.

    REFERENCE ASSEMBLY [TextCommon];
    REFERENCE ASSEMBLY [TextSentiment];
    REFERENCE ASSEMBLY [TextKeyPhrase];

    @WarAndPeace =
        EXTRACT No int,
                Year string,
                Book string,
                Chapter string,
                Text string
        FROM @"/usqlext/samples/cognition/war_and_peace.csv"
        USING Extractors.Csv();

### <a name="extract-key-phrases-for-each-paragraph"></a>Extrahieren von Schlüsselwörtern für jeden Absatz

    @keyphrase =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                KeyPhrase string
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.KeyPhraseExtractor();

    // Tokenize the key phrases.
    @kpsplits =
        SELECT No,
            Year,
            Book,
            Chapter,
            Text,
            T.KeyPhrase
        FROM @keyphrase
            CROSS APPLY
                new Cognition.Text.Splitter("KeyPhrase") AS T(KeyPhrase);
    
### <a name="perform-sentiment-analysis-on-each-paragraph"></a>Ausführen einer Stimmungsanalyse für jeden Absatz

    @sentiment =
        PROCESS @WarAndPeace
        PRODUCE No,
                Year,
                Book,
                Chapter,
                Text,
                Sentiment string,
                Conf double
        READONLY No,
                Year,
                Book,
                Chapter,
                Text
        USING new Cognition.Text.SentimentAnalyzer(true);




<!--HONumber=Dec16_HO3-->


