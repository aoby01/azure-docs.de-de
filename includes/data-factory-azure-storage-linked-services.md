### <a name="azure-storage-linked-service"></a>Mit Azure-Speicher verknüpfter Dienst
Sie können einen mit **Azure Storage verknüpften Dienst** verwenden, um ein Azure-Speicherkonto mithilfe des **Kontoschlüssels** mit einer Azure Data Factory zu verknüpfen. Dadurch erhält die Data Factory globalen Zugriff auf Azure Storage. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure Storage verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf **AzureStorage** |Ja |
| connectionString |Geben Sie Informationen, die zur Verbindung mit dem Azure-Speicher erforderlich sind, für die connectionString-Eigenschaft ein. |Ja |

Im folgenden Artikel finden Sie Schritte zum Anzeigen bzw. Kopieren des Kontoschlüssels für einen Azure-Speicher: [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](../articles/storage/storage-create-storage-account.md#manage-your-storage-account).

**Beispiel:**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

### <a name="azure-storage-sas-linked-service"></a>Mit Azure Storage SAS verknüpfter Dienst
Shared Access Signatures (SAS) bieten delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Sie ermöglichen es Ihnen, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu erteilen, ohne Ihre Konto-Zugriffsschlüssel weitergeben zu müssen. Die SAS ist ein URI, dessen Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Für den Zugriff auf Speicherressourcen mit der SAS braucht der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode zu übergeben. Weitere Informationen zu SAS finden Sie unter [Shared Access Signatures: Grundlagen zum SAS-Modell](../articles/storage/storage-dotnet-shared-access-signature-part-1.md).

Sie können einen mit Azure Storage SAS verknüpften Dienst verwenden, um ein Azure-Speicherkonto mithilfe eines Shared Access Signature (SAS) mit einer Azure Data Factory zu verknüpfen. Dies ermöglicht der Data Factory eingeschränkten/zeitgebundenen Zugriff auf alle bzw. bestimmte Ressourcen (Blob/Container) im Speicher. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure Storage SAS verknüpften Dienst spezifisch sind. 

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf **AzureStorageSas** |Ja |
| sasUri |Geben Sie den Shared Access Signature-URI für Azure-Speicher-Ressourcen wie BLOB, Container oder Tabelle an.  |Ja |

**Beispiel:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Beim Erstellen eines **SAS-URI**sollten Sie Folgendes berücksichtigen:  

* Azure Data Factory unterstützt nur **Dienst-SAS**, nicht Konto-SAS. Unter [Typen von Shared Access Signatures](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) finden Sie ausführliche Informationen zu diesen beiden Typen.
* Legen Sie für Objekte basierend darauf, wie der verknüpfte Dienst (Lesen, Schreiben, Lesen/Schreiben) in Ihrer Data Factory verwendet wird, geeignete Lese-/Schreib-**Berechtigungen** fest.
* Legen Sie für **Ablaufzeit** einen geeigneten Wert fest. Stellen Sie sicher, dass der Zugriff auf Azure Storage-Objekte nicht im aktiven Zeitraum der Pipeline abläuft.
* URI sollte je nach Bedarf auf der richtigen Container-/BLOB- oder Tabellenebene erstellt werden. Mithilfe eines SAS-URI für einen Azure-BLOB kann der Data Factory-Dienst auf diesen bestimmten BLOB zugreifen. Mithilfe eines SAS-URI für einen Azure-BLOB-Container kann der Data Factory-Dienst die BLOBs in diesem Container durchlaufen. Wenn Sie den Zugriff später auf mehrere Objekte ausweiten/auf weniger Objekte beschränken oder die SAS-URI aktualisieren möchten, denken Sie daran, den verknüpften Dienst mit dem neuen URI zu aktualisieren.   

