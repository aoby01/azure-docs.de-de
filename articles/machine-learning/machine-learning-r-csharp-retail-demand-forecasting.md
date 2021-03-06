---
title: "(Veraltet:) Prognose – ETS + STL – Azure  | Microsoft Docs"
description: "(Veraltet:) Prognose – ETS + STL"
services: machine-learning
documentationcenter: 
author: xueshanz
manager: jhubbard
editor: cgronlun
ms.assetid: 153eab4d-6293-45e1-9871-ec339e810dd9
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: yijichen
ROBOTS: NOINDEX
redirect_url: https://gallery.cortanaintelligence.com/
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: cdf6661a36e38bf7a6fca241682be796712bd5d9
ms.contentlocale: de-de
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-forecasting---ets--stl"></a>(Veraltet:) Prognose – ETS + STL

> [!NOTE]
> Der Microsoft DataMarket wird eingestellt, und diese API gilt nun als veraltet. 
> 
> Im [Cortana Intelligence-Katalog](http://gallery.cortanaintelligence.com) finden Sie viele nützliche Beispielexperimente und -APIs. Weitere Informationen zum Katalog finden Sie unter [Teilen und Entdecken von Ressourcen im Cortana Intelligence-Katalog](machine-learning-gallery-how-to-use-contribute-publish.md).

Dieser [Webdienst](https://datamarket.azure.com/dataset/aml_labs/demand_forecast) implementiert Modelle der Saison-Trend-Zerlegung (Seasonal Trend Decomposition, STL) und der exponentiellen Glättung (Exponential Smoothing, ETS), um Vorhersagen auf Grundlage der Verlaufsdaten zu erstellen, die vom Benutzer bereitgestellt werden. Erhöht sich der Bedarf für ein bestimmtes Produkt in diesem Jahr? Kann ich meine Produktverkäufe für die Weihnachtssaison vorhersagen, damit ich meine Inventur effektiv planen kann? Planungsmodelle sind für solche Fragen die passende Lösung. Angesichts der letzten Daten, untersuchen diese Modelle versteckte Trends und Saisonabhängigkeit, um zukünftige Trends vorherzusagen. 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> Dieser Webdienst kann von Benutzern verwendet werden – beispielsweise über eine mobile App, eine Website oder sogar über einen lokalen Computer. Dieser Webdienst ist jedoch auch ein gutes Beispiel dafür, wie Azure Machine Learning zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure Machine Learning Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden – ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.  
> 
> 

## <a name="consumption-of-web-service"></a>Nutzung des Webdiensts
Dieser Dienst akzeptiert 4 Argumente und berechnet die Prognosen.
Die Eingabeargumente sind:

* Frequency – Gibt die Häufigkeit der Rohdaten an (täglich/wöchentlich/monatlich/vierteljährlich/jährlich)
* Horizon – Zeitrahmen der zukünftigen Prognose
* Date – Hinzufügen der neuen Zeitreihendaten für Zeit
* Value – Hinzufügen der neuen Zeitreihendaten für Datenwerte

Die Ausgabe des Dienstes sind die berechneten Werte für die Prognose.

Eine Beispieleingabe wäre: 

* Frequency – 12
* Horizon – 12
* Date – 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012; 1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013; 1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value – 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

> Dieser Dienst, der im Azure Marketplace gehostet wird, ist ein OData-Dienst. Diese Dienste können durch POST- oder GET-Methoden aufgerufen werden. 
> 
> 

Es gibt mehrere Möglichkeiten, den Dienst auf automatisierte Weise zu nutzen ([hier](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx) finden Sie eine Beispiel-App).

### <a name="starting-c-code-for-web-service-consumption"></a>Starten von C#-Code für Webdienstnutzung:
    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };         var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


## <a name="creation-of-web-service"></a>Erstellen des Webdiensts
> Dieser Webdienst wurde mithilfe von Azure Machine Learning erstellt. Eine kostenlose Testversion sowie Einführungsvideos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](machine-learning-publish-a-machine-learning-web-service.md) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.
> 
> 

In Azure Machine Learning wurde ein neues leeres Experiment erstellt. Stichprobeneingabedaten wurden mit einem vordefinierten Datenschema hochgeladen. Mit dem Schema ist ein Modul [Execute R Script][execute-r-script] verknüpft, das die STL- und ETS-Prognosemodelle mithilfe der R-Funktionen stl, ets und forecast generiert. 

### <a name="experiment-flow"></a>Experimentablauf:
![Experimentablauf][2]

#### <a name="module-1"></a>Modul 1:
    # Add in the CSV file with the data in the format shown below 
![Beispieldaten][3]    

#### <a name="module-2"></a>Modul 2:
    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)

    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]

    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)

    # Fit a time series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- stl(train_ts,  s.window="periodic")
    train_model <- forecast(fit1, h = data$horizon, method = 'ets')
    plot(train_model)

    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")

    # Data output
    maml.mapOutputPort("data.forecast");

## <a name="limitations"></a>Einschränkungen
Dies ist ein sehr einfaches Beispiel für die Prognose mit ETS und STL. Wie aus dem oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert, und der Dienst geht davon aus, dass alle Variablen kontinuierliche/positive Werte sind und die Häufigkeit eine ganze Zahl größer als 1 sein sollte. Die Vektoren für Datum und der Wert sollten gleich lang sein, und die Länge der Zeitreihe sollte größer sein als das Zweifache der Häufigkeit. Die Variable für das Datum muss dem Format "mm/tt/jjjj" entsprechen.

## <a name="faq"></a>Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Azure Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/


