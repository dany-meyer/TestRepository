# Postman Mock-Server: Praktisches Handout
## Unsere erste Test-API in Postman

---

## Lernziel

Die Studierenden können:
* eine Postman-Collection mit mehreren Requests anlegen,
* für jeden Request Examples (Beispiel-Antworten) hinzufügen,
* daraus einen Mock-Server erzeugen und
* diesen Mock als Testziel für andere Anwendungen oder Teams nutzen.

---

## Szenario

Ihr arbeitet im Team an einer **Studentenverwaltung** für die Hochschule Neu-Ulm. Ihr wollt testen, wie euer Client (z. B. eine Java-Anwendung) mit einem Webservice kommuniziert — aber der echte Server existiert noch nicht. Deshalb erstellt ihr ihn virtuell mit Postman.

---

## Teil 1: Collection erstellen

### Schritt 1: Neue Collection anlegen

1. Öffne Postman
2. Klicke auf **"New"** → **"Collection"**
3. Benenne die Collection: **"Studentenverwaltung API"**
4. Füge eine Beschreibung hinzu:
   ```
   Mock-API für die Studentenverwaltung
   Endpoints: Studenten abrufen, erstellen, aktualisieren, löschen
   ```

### Schritt 2: Base URL als Variable definieren

1. In der Collection auf den **Variables**-Tab klicken
2. Neue Variable hinzufügen:
   - **Variable**: `baseUrl`
   - **Initial Value**: `https://api.studentenverwaltung.de`
   - **Current Value**: `https://api.studentenverwaltung.de`

---

## Teil 2: Requests mit Examples erstellen

### Request 1: Alle Studenten abrufen (GET)

#### Request anlegen:
1. In der Collection: **"Add request"**
2. **Name**: "Alle Studenten abrufen"
3. **Method**: GET
4. **URL**: `{{baseUrl}}/api/students`

#### Example 1 hinzufügen: "Erfolgreiche Antwort"
1. Klicke auf **"Save Response"** → **"Save as example"**
2. **Example Name**: "Erfolgreiche Antwort - 2 Studenten"
3. **Status Code**: 200 OK
4. **Response Body** (JSON):

```json
[
  {
    "id": 1,
    "matrikelnummer": "12345",
    "vorname": "Max",
    "nachname": "Mustermann",
    "email": "max.mustermann@student.de",
    "studiengang": "DEM",
    "semester": 3,
    "einschreibungsdatum": "2023-10-01"
  },
  {
    "id": 2,
    "matrikelnummer": "12346",
    "vorname": "Anna",
    "nachname": "Schmidt",
    "email": "anna.schmidt@student.de",
    "studiengang": "KIM",
    "semester": 5,
    "einschreibungsdatum": "2022-04-01"
  },
  {
    "id": 3,
    "matrikelnummer": "12347",
    "vorname": "Lisa",
    "nachname": "Müller",
    "email": "lisa.mueller@student.de",
    "studiengang": "IMA",
    "semester": 2,
    "einschreibungsdatum": "2023-04-01"
  }
]
```

#### Example 2 hinzufügen: "Keine Studenten bei unbekanntem Studiengang"
1. Klicke erneut auf **"Add example"**
2. **Example Name**: "Keine Studenten bei Filter"
3. **Status Code**: 200 OK
4. **URL anpassen**: `{{baseUrl}}/api/students?studiengang=XYZ`
5. **Response Body**:

```json
[]
```

> **Hinweis:** Dieses Example wird nur zurückgegeben, wenn der Query-Parameter `studiengang=XYZ` mitgesendet wird. So können verschiedene Szenarien getestet werden.

---

### Request 2: Student mit ID 1 abrufen (GET)

#### Request anlegen:
1. **Name**: "Student mit ID 1 abrufen"
2. **Method**: GET
3. **URL**: `{{baseUrl}}/api/students/1`

#### Example: "Student gefunden"
- **Status Code**: 200 OK
- **Response Body**:

```json
{
  "id": 1,
  "matrikelnummer": "12345",
  "vorname": "Max",
  "nachname": "Mustermann",
  "email": "max.mustermann@student.de",
  "studiengang": "DEM",
  "semester": 3,
  "einschreibungsdatum": "2023-10-01",
  "adresse": {
    "strasse": "Musterstraße 1",
    "plz": "89231",
    "ort": "Neu-Ulm"
  },
  "kontakt": {
    "telefon": "+49 731 123456",
    "mobiltelefon": "+49 170 1234567"
  }
}
```

---

### Request 3: Student mit ID 999 abrufen (GET)

#### Request anlegen:
1. **Name**: "Student mit ID 999 abrufen"
2. **Method**: GET
3. **URL**: `{{baseUrl}}/api/students/999`

#### Example: "Student nicht gefunden"
- **Status Code**: 404 Not Found
- **Response Body**:

```json
{
  "error": "Not Found",
  "message": "Student mit ID 999 nicht gefunden",
  "timestamp": "2024-10-31T10:15:30",
  "status": 404
}
```

---

### Request 4: Neuen Student erstellen (POST)

#### Request anlegen:
1. **Name**: "Neuen Student erstellen"
2. **Method**: POST
3. **URL**: `{{baseUrl}}/api/students`
4. **Headers**: `Content-Type: application/json`
5. **Body** (raw, JSON):

```json
{
  "matrikelnummer": "12347",
  "vorname": "Lisa",
  "nachname": "Müller",
  "email": "lisa.mueller@student.de",
  "studiengang": "IMA",
  "semester": 1
}
```

#### Example 1: "Erfolgreich erstellt"
- **Status Code**: 201 Created
- **Response Body**:

```json
{
  "id": 3,
  "matrikelnummer": "12347",
  "vorname": "Lisa",
  "nachname": "Müller",
  "email": "lisa.mueller@student.de",
  "studiengang": "IMA",
  "semester": 1,
  "einschreibungsdatum": "2024-10-31",
  "message": "Student erfolgreich angelegt"
}
```

#### Example 2: "Validierungsfehler"
- **Status Code**: 400 Bad Request
- **Response Body**:

```json
{
  "error": "Bad Request",
  "message": "Validierungsfehler",
  "status": 400,
  "timestamp": "2024-10-31T10:20:00",
  "errors": [
    {
      "field": "email",
      "message": "Email-Format ungültig"
    },
    {
      "field": "matrikelnummer",
      "message": "Matrikelnummer bereits vergeben"
    }
  ]
}
```

---

### Request 5: Student aktualisieren (PUT)

#### Request anlegen:
1. **Name**: "Student aktualisieren"
2. **Method**: PUT
3. **URL**: `{{baseUrl}}/api/students/:id`
4. **Path Variable**: `id` = `1`
5. **Body** (raw, JSON):

```json
{
  "vorname": "Maximilian",
  "nachname": "Mustermann",
  "email": "maximilian.mustermann@student.de",
  "semester": 4
}
```

#### Example 1: "Erfolgreich aktualisiert"
- **Status Code**: 200 OK
- **Response Body**:

```json
{
  "id": 1,
  "matrikelnummer": "12345",
  "vorname": "Maximilian",
  "nachname": "Mustermann",
  "email": "maximilian.mustermann@student.de",
  "studiengang": "DEM",
  "semester": 4,
  "einschreibungsdatum": "2023-10-01",
  "message": "Student erfolgreich aktualisiert"
}
```

#### Example 2: "Student nicht gefunden"
- **Status Code**: 404 Not Found
- **Response Body**:

```json
{
  "error": "Not Found",
  "message": "Student mit ID 999 existiert nicht",
  "timestamp": "2024-10-31T10:25:00",
  "status": 404
}
```

---

### Request 6: Student löschen (DELETE)

#### Request anlegen:
1. **Name**: "Student löschen"
2. **Method**: DELETE
3. **URL**: `{{baseUrl}}/api/students/:id`
4. **Path Variable**: `id` = `1`

#### Example 1: "Erfolgreich gelöscht"
- **Status Code**: 204 No Content
- **Response Body**: (leer)

#### Example 2: "Mit Bestätigung"
- **Status Code**: 200 OK
- **Response Body**:

```json
{
  "message": "Student mit ID 1 wurde erfolgreich gelöscht",
  "deletedId": 1,
  "timestamp": "2024-10-31T10:30:00"
}
```

---

### Request 7: Studenten nach Studiengang filtern (GET)

#### Request anlegen:
1. **Name**: "Studenten nach Studiengang"
2. **Method**: GET
3. **URL**: `{{baseUrl}}/api/students?studiengang={{studiengang}}`
4. **Query Params**: `studiengang` = `DEM`

#### Example 1: "Gefilterte Studenten - DEM"
- **Status Code**: 200 OK
- **URL**: `{{baseUrl}}/api/students?studiengang=DEM`
- **Response Body**:

```json
[
  {
    "id": 1,
    "matrikelnummer": "12345",
    "vorname": "Max",
    "nachname": "Mustermann",
    "email": "max.mustermann@student.de",
    "studiengang": "DEM",
    "semester": 3
  },
  {
    "id": 4,
    "matrikelnummer": "12348",
    "vorname": "Tom",
    "nachname": "Weber",
    "email": "tom.weber@student.de",
    "studiengang": "DEM",
    "semester": 7
  }
]
```

#### Example 2: "Gefilterte Studenten - KIM"
- **Status Code**: 200 OK
- **URL**: `{{baseUrl}}/api/students?studiengang=KIM`
- **Response Body**:

```json
[
  {
    "id": 2,
    "matrikelnummer": "12346",
    "vorname": "Anna",
    "nachname": "Schmidt",
    "email": "anna.schmidt@student.de",
    "studiengang": "KIM",
    "semester": 5
  }
]
```

---

## Teil 3: Mock-Server erstellen

### Schritt 1: Mock-Server anlegen

1. Öffne die Collection **"Studentenverwaltung API"**
2. Rechts oben auf die drei Punkte **"..."** klicken
3. **"Mock collection"** auswählen
4. **Mock-Server konfigurieren**:
   - **Mock Server Name**: "Studentenverwaltung Mock"
   - **Environment**: (optional) "No Environment" oder ein Test-Environment
   - Checkbox: ✅ **"Make mock server private"** (falls gewünscht)
   - Checkbox: ✅ **"Save the mock server URL as an environment variable"**
5. Klicke auf **"Create Mock Server"**

### Schritt 2: Mock-URL kopieren

Nach dem Erstellen erhältst du eine URL wie:
```
https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.mock.pstmn.io
```

**Diese URL kopieren!** Sie ersetzt deine `baseUrl` beim Testen.

---

## Teil 4: Mock-Server testen

### In Postman testen

1. **Neue Tab öffnen** oder bestehende Requests verwenden
2. **URL anpassen**: 
   - Vorher: `{{baseUrl}}/api/students`
   - Jetzt: `https://deine-mock-url.mock.pstmn.io/api/students`

3. **Request senden** → Du erhältst die Example-Antworten!

### Mit cURL testen

Öffne das Terminal und teste:

```bash
# Alle Studenten abrufen
curl https://deine-mock-url.mock.pstmn.io/api/students

# Einzelnen Student abrufen
curl https://deine-mock-url.mock.pstmn.io/api/students/1

# Neuen Student erstellen
curl -X POST https://deine-mock-url.mock.pstmn.io/api/students \
  -H "Content-Type: application/json" \
  -d '{
    "matrikelnummer": "99999",
    "vorname": "Test",
    "nachname": "User",
    "email": "test@student.de",
    "studiengang": "Test",
    "semester": 1
  }'
```

---

## Teil 5: Mock in Java-Anwendung nutzen

### Beispiel: Java HTTP Client

```java
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.URI;

public class StudentApiClient {
    
    private static final String MOCK_URL = 
        "https://deine-mock-url.mock.pstmn.io";
    
    public static void main(String[] args) throws Exception {
        // HTTP Client erstellen
        HttpClient client = HttpClient.newHttpClient();
        
        // Request erstellen
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(MOCK_URL + "/api/students"))
            .GET()
            .build();
        
        // Request senden
        HttpResponse<String> response = client.send(
            request, 
            HttpResponse.BodyHandlers.ofString()
        );
        
        // Antwort ausgeben
        System.out.println("Status: " + response.statusCode());
        System.out.println("Body: " + response.body());
    }
}
```

### Beispiel: Spring RestTemplate

```java
import org.springframework.web.client.RestTemplate;
import org.springframework.http.ResponseEntity;

public class StudentService {
    
    private static final String MOCK_URL = 
        "https://deine-mock-url.mock.pstmn.io";
    
    private RestTemplate restTemplate = new RestTemplate();
    
    public String getAllStudents() {
        String url = MOCK_URL + "/api/students";
        ResponseEntity<String> response = 
            restTemplate.getForEntity(url, String.class);
        
        return response.getBody();
    }
    
    public String getStudentById(Long id) {
        String url = MOCK_URL + "/api/students/" + id;
        ResponseEntity<String> response = 
            restTemplate.getForEntity(url, String.class);
        
        return response.getBody();
    }
}
```

---

## Teil 6: Erweiterte Features

### 6.1: Verschiedene Examples gezielt abrufen

Mit dem Header `x-mock-response-name` kannst du gezielt ein bestimmtes Example abrufen:

```bash
# Erfolgreiches Example
curl -H "x-mock-response-name: Erfolgreiche Antwort - 2 Studenten" \
  https://deine-mock-url.mock.pstmn.io/api/students

# Fehler-Example
curl -H "x-mock-response-name: Student nicht gefunden" \
  https://deine-mock-url.mock.pstmn.io/api/students/999
```

### 6.2: Delay simulieren

Füge einen Delay hinzu, um Netzwerklatenz zu simulieren:

1. Gehe zu deinem Mock-Server
2. **"Edit"** klicken
3. **"Simulate network delay"** aktivieren
4. Delay einstellen (z.B. 2000ms)

### 6.3: Environment-spezifische Mocks

Erstelle verschiedene Environments für verschiedene Test-Szenarien:

**Environment: "Happy Path"**
- Enthält nur Success-Responses

**Environment: "Error Cases"**
- Enthält nur Error-Responses

## 🎓 Häufig gestellte Fragen (FAQ)

**F: Kann ich den Mock-Server mit meinem Team teilen?**  
A: Ja! Teile einfach die Mock-URL oder die gesamte Collection mit deinem Team.

**F: Bleiben die Daten im Mock-Server gespeichert?**  
A: Nein, der Mock-Server liefert nur die vordefinierten Examples zurück. Er speichert keine echten Daten.

**F: Kann ich verschiedene Antworten für denselben Request bekommen?**  
A: Ja, erstelle mehrere Examples und nutze den Header `x-mock-response-name`, um gezielt eines auszuwählen.

**F: Wie lösche ich einen Mock-Server?**  
A: Gehe zu "Mock Servers" in der linken Sidebar → drei Punkte neben deinem Mock → "Delete".

**F: Kostet der Mock-Server etwas?**  
A: Die Free-Version von Postman erlaubt begrenzte Mock-Server-Calls pro Monat (1000 Calls). Für mehr benötigst du ein Paid-Abo.

**F: Kann ich den Mock-Server für Lasttests verwenden?**  
A: Nein, Mock-Server sind für Entwicklung und Testing gedacht, nicht für Performance- oder Lasttests.

---

## Zusammenfassung

**Was ihr gelernt habt:**

1. Postman Collections strukturiert anlegen
2. Requests mit verschiedenen HTTP-Methoden erstellen
3. Examples für verschiedene Szenarien definieren
4. Mock-Server aus einer Collection generieren
5. Mock-Server als Testziel nutzen
6. Mock-Server in eigene Anwendungen integrieren

**Requests erstellt:**
- GET `/api/students` - Alle Studenten
- GET `/api/students/1` - Student gefunden
- GET `/api/students/999` - Student nicht gefunden (404)
- POST `/api/students` - Neuen Student erstellen
- PUT `/api/students/1` - Student aktualisieren
- DELETE `/api/students/1` - Student löschen
- GET `/api/students?studiengang=...` - Nach Studiengang filtern

**Vorteile des Mock-Servers:**

- Entwicklung ohne fertigen Backend
- Paralleles Arbeiten im Team
- Testen verschiedener Szenarien
- API-Dokumentation durch Examples
- Schnelles Prototyping

