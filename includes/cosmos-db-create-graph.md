Sie können jetzt den Daten-Explorer verwenden, um einen Graph-Container zu erstellen und Daten zu Ihrer Datenbank hinzuzufügen. 

1. Klicken Sie im Azure-Portal im Navigationsmenü auf **Daten-Explorer**. 
2. Klicken Sie auf dem Blatt „Daten-Explorer“ auf **Neues Diagramm**, und füllen Sie dann die Seite mit den folgenden Informationen aus.

    ![Daten-Explorer im Azure-Portal](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Einstellung|Empfohlener Wert|Beschreibung
    ---|---|---
    Datenbank-ID|sample-database|Die ID Ihrer neuen Datenbank. Datenbanknamen müssen zwischen 1 und 255 Zeichen lang sein und dürfen weder `/ \ # ?` noch nachgestellte Leerzeichen enthalten.
    Diagramm-ID|sample-graph|Die ID Ihres neuen Diagramms. Für Diagrammnamen gelten dieselben Zeichenanforderungen wie für Datenbank-IDs.
    Speicherkapazität| 10 GB|Behalten Sie den Standardwert bei. Dies ist die Speicherkapazität der Datenbank.
    Durchsatz|400 RUs|Behalten Sie den Standardwert bei. Sie können den Durchsatz später zentral hochskalieren, wenn Sie Latenzen reduzieren möchten.
    Partitionsschlüssel|/userid|Ein Partitionsschlüssel, der Daten gleichmäßig auf alle Partitionen verteilt. Die Auswahl des richtigen Partitionsschlüssels ist wichtig für die Erstellung eines leistungsfähigen Diagramms. Weitere Informationen finden Sie unter [Entwerfen für Partitionierung](../articles/cosmos-db/partition-data.md#designing-for-partitioning).

3. Wenn das Formular ausgefüllt ist, klicken Sie auf **OK**.