# LawnCare AI - Besplatna aplikacija za travnjak

## Pregled

Mobilna aplikacija za Android/iOS koja pomaže korisnicima da:
1. Na osnovu lokacije odrede tip trave za sadnju
2. Prikazu postupke za sadnju trave
3. Na osnovu slika sa kamere provere da li su dobro uradili
4. Dijagnostikuju probleme na travnjaku i dobiju rešenja

---

## Tech Stack

| Komponenta | Tehnologija | Cena |
|------------|-------------|------|
| Mobilna app | Flutter | Besplatno |
| Baza podataka | Firebase Firestore | Besplatno (free tier) |
| Autentifikacija | Firebase Auth | Besplatno |
| Storage slika | Firebase Storage | Besplatno (free tier) |
| Prepoznavanje slika | ML Kit | Besplatno |
| Geocoding | Google Geocoding API | Besplatno (do 40,000 req/mes) |
| AI analiza (opcija) | OpenAI API | 0-5 USD/mes |
| Domen | Već postoji | 0 USD |

**Ukupno: 0 USD/mesečno**

---

## Struktura projekta

```
lawn-care-ai/
├── android/
├── ios/
├── lib/
│   ├── main.dart
│   ├── models/
│   │   ├── grass_type.dart
│   │   ├── climate_zone.dart
│   │   ├── lawn_problem.dart
│   │   └── planting_step.dart
│   ├── services/
│   │   ├── location_service.dart
│   │   ├── image_classifier.dart
│   │   ├── grass_database_service.dart
│   │   └── firebase_service.dart
│   ├── screens/
│   │   ├── home_screen.dart
│   │   ├── location_screen.dart
│   │   ├── grass_recommendation_screen.dart
│   │   ├── planting_guide_screen.dart
│   │   ├── camera_screen.dart
│   │   ├── diagnosis_result_screen.dart
│   │   └── history_screen.dart
│   ├── widgets/
│   │   ├── grass_card.dart
│   │   ├── step_tile.dart
│   │   ├── problem_card.dart
│   │   └── image_analysis_widget.dart
│   └── data/
│       ├── grass_database.json
│       └── problems_database.json
├── pubspec.yaml
└── README.md
```

---

## Funkcije aplikacije

### 1. Preporuka trave na osnovu lokacije

**Kako radi:**
- Korisnik dozvoljava pristup lokaciji
- Aplikacija uzima koordinate (latitude, longitude)
- Google Geocoding API pretvara koordinate u državu/region
- Na osnovu države se određuje klimatska zona
- Iz baze podataka se izvlače odgovarajući tipovi trave

**Klimatske zone (globalno):**

| Zona | Klima | Regioni | Tipovi trave |
|------|-------|---------|--------------|
| Tropska | Vruće i vlažno | Jugoistočna Azija, Afrika | Bermuda, Zoysia, St. Augustine |
| Mediteranska | Blage zime, suša leto | Španija, Italija, Grčka | Lolium, Festuca, Cynodon |
| Kontinentalna | Hladne zime, topla leta | Srbija, Nemačka, Poljska | Lolium perenne, Poa pratensis |
| Hladna | Duge hladne zime | Skandinavija, Kanada | Festuca, Agrostis |
| Suva | Malo padavina | Bliski istok, Australija | Bermuda, Buffalo |
| Tropska vlažna | Toplo i vlažno cele godine | Brazil, Indonezija | St. Augustine, Bermuda |

**Podaci po zoni:**

```json
{
  "zone_id": "continental",
  "climate_name": "Kontinentalna",
  "countries": ["SRB", "HRV", "BIH", "MNE", "HUN", "CZE", "SVK", "POL", "DEU"],
  "grass_types": [
    {
      "id": "lolium_perenne",
      "name": "Engleski rajgras (Lolium perenne)",
      "description": "Brzo klija, toleriše gaženje, idealno za dvorišta",
      "planting_season": ["mart-april", "septembar-oktobar"],
      "temperature_range": {"min": 5, "max": 25},
      "water_needs": "srednje",
      "sun_requirements": "puno sunca ili polusenka",
      "growth_rate": "brzo",
      "maintenance_level": "srednje",
      "seed_rate": "30-40g/m²",
      "mowing_height": "5-7 cm",
      "image": "assets/images/lolium.png"
    },
    {
      "id": "poa_pratensis",
      "name": "Listavac (Poa pratensis)",
      "description": "Formira gusti tepih, sporije klija, dugovečan",
      "planting_season": ["mart-april", "septembar-oktobar"],
      "temperature_range": {"min": 3, "max": 22},
      "water_needs": "srednje do visoko",
      "sun_requirements": "puno sunca",
      "growth_rate": "sporo",
      "maintenance_level": "srednje do visoko",
      "seed_rate": "20-30g/m²",
      "mowing_height": "4-6 cm",
      "image": "assets/images/poa.png"
    }
  ]
}
```

### 2. Uputstvo za sadnju trave

**Koraci po fazama:**

```json
{
  "planting_guide": {
    "preparation": [
      {
        "step": 1,
        "title": "Uklonite staru travu i korov",
        "description": "Freziranje ili ručno uklanjanje do 15cm dubine",
        "tools": ["Frezija", "Motika", "Lopata"],
        "duration": "2-4 sata",
        "tip": "Ako imate korov, sačekajte 2 nedelje pre sledećeg koraka"
      },
      {
        "step": 2,
        "title": "Izravnajte teren",
        "description": "Dodajte zemlju na udubljenja, uklonite višak",
        "tools": ["Grabilica", "Daska za izravnavanje"],
        "duration": "1-2 sata",
        "tip": "Teren treba da bude blago nagnut od kuće za odvod vode"
      },
      {
        "step": 3,
        "title": "Dodajte kompost ili đubrivo",
        "description": "Raspršite 3-5cm organskog komposta po celoj površini",
        "tools": ["Kosa za kompost", "Grablje"],
        "duration": "1-2 sata",
        "tip": "Izbegavajte sveže đubrivo - može spržiti seme"
      },
      {
        "step": 4,
        "title": "Provucite zemljište",
        "description": "Zemlja treba da bude rastresita do 10cm dubine",
        "tools": ["Frezija", "Grablje"],
        "duration": "1-2 sata",
        "tip": "Test: zabodite prst - treba lako da uđe"
      }
    ],
    "seeding": [
      {
        "step": 5,
        "title": "Podelite seme na dva dela",
        "description": "Pola posejte horizontalno, pola vertikalno za ravnomernu pokrivenost",
        "tools": ["Sejalica za travu ili raspršivač"],
        "duration": "30 minuta",
        "tip": "Dodajte pesak u seme za lakše raspršivanje"
      },
      {
        "step": 6,
        "title": "Pokrijte seme",
        "description": "Lagano provucite grablje preko semena ili dodajte 0.5cm zemlje",
        "tools": ["Grablje"],
        "duration": "30 minuta",
        "tip": "Ne pokrivajte previše - seme treba svetlost za klijanje"
      },
      {
        "step": 7,
        "title": "Pritisnite zemljište",
        "description": "Valjkom ili gazećim po terenu obezbedite kontakt semena sa zemljom",
        "tools": ["Valjak za travu"],
        "duration": "30 minuta",
        "tip": "Valjak treba da bude težak (50-70kg)"
      }
    ],
    "aftercare": [
      {
        "step": 8,
        "title": "Zalijte odmah",
        "description": "Fino prskanje - zemlja treba da bude vlažna ali ne blatnjava",
        "tools": ["Cretno za zalivanje sa finim mlazom"],
        "duration": "15-20 minuta",
        "tip": "Zalivajte ujutru ili uveče, nikad pod suncem"
      },
      {
        "step": 9,
        "title": "Zalivajte svakodnevno 2 nedelje",
        "description": "Održavajte zemlju konstantno vlažnom dok trava ne nikne",
        "tools": ["Cretno za zalivanje"],
        "duration": "10 minuta dnevno",
        "tip": "Prvi izdanci se pojavljuju za 7-14 dana"
      },
      {
        "step": 10,
        "title": "Prvo košenje",
        "description": "Kosite kad trava dostigne 8-10cm, ostavljate 5-7cm",
        "tools": ["Kosa za travu"],
        "duration": "30-60 minuta",
        "tip": "Kosilica treba da bude oštra - tupu oštrica izvlači koren"
      }
    ]
  }
}
```

### 3. Provera slike (Before/After)

**Kako radi:**
- Korisnik slika travnjak PRE sadnje
- Aplikacija čuva sliku u Firebase Storage
- Korisnik slika travnjak POSLE 2-4 nedelje
- ML Kit klasifikuje obe slike i poredi rezultate

**Šta ML Kit prepoznaje:**

| Klasa | Opis | Šta znači |
|-------|------|-----------|
| Green grass | Zelena trava | Zdrav travnjak |
| Brown grass | Smeđa trava | Nedostatak vode ili bolest |
| Bare soil | Gola zemlja | Trava nije nikla ili je uginula |
| Weeds | Korov | Potrebno uklanjanje |
| Patchy | Mrlje | Neravnomeran rast |

**Poređenje:**

```dart
class ImageComparison {
  final double greenBefore;
  final double greenAfter;
  final double weedsBefore;
  final double weedsAfter;
  
  String get result {
    if (greenAfter > greenBefore && weedsAfter < weedsBefore) {
      return 'Travnjak se oporavlja - nastavite sa zalivanjem';
    } else if (greenAfter < greenBefore) {
      return 'Travnjak propada - proverite zalivanje i đubrenje';
    } else {
      return 'Nema značajnih promena - budite strpljivi';
    }
  }
}
```

### 4. Dijagnostika problema

**ML Kit klasifikacija + baza rešenja:**

```json
{
  "problems": [
    {
      "id": "yellow_patches",
      "name": "Žute mrlje",
      "visual_indicators": ["žuta boja", "mrlje nepravilnog oblika", "trava se lako čupa"],
      "possible_causes": [
        {
          "cause": "Nedostatak azota",
          "probability": 0.4,
          "solution": "Đubrenje NPK 20-10-10, 30g/m²",
          "prevention": "Đubrenje svaka 6-8 nedelja u sezoni"
        },
        {
          "cause": "Gljivična bolest (Rhizoctonia)",
          "probability": 0.3,
          "solution": "Fungicid + smanjiti zalivanje",
          "prevention": "Ne zalivati uveče, kositi na 5cm"
        },
        {
          "cause": "Nedostatak vode",
          "probability": 0.2,
          "solution": "Povećati zalivanje na 2cm nedeljno",
          "prevention": "Zalivati ujutru, duboko ali retko"
        },
        {
          "cause": "Štetočine (lavlji Scarabej)",
          "probability": 0.1,
          "solution": "Insekticid tretman",
          "prevention": "Redovna kontrola zemljišta"
        }
      ]
    },
    {
      "id": "bare_spots",
      "name": "Gole mrlje",
      "visual_indicators": ["gola zemlja", "trava nedostaje u krugovima"],
      "possible_causes": [
        {
          "cause": "Preterano gaženje",
          "probability": 0.35,
          "solution": "Posijati novo seme, zaštititi od gaženja",
          "prevention": "Postaviti staze za kretanje"
        },
        {
          "cause": "Kućni ljubimci (urin)",
          "probability": 0.3,
          "solution": "Operite mesto vodom, posijati novo seme",
          "prevention": "Navikavati ljubimce na jedno mesto"
        },
        {
          "cause": "Bolest",
          "probability": 0.25,
          "solution": "Tretman fungicidom + posejati novo seme",
          "prevention": "Redovna aeracija zemljišta"
        },
        {
          "cause": "Nedostatak svetlosti",
          "probability": 0.1,
          "solution": "Orezati grane koje prave senku",
          "prevention": "Izabrati travu tolerantnu na senku"
        }
      ]
    },
    {
      "id": "moss_growth",
      "name": "Mahovina",
      "visual_indicators": ["zelena mekana masa", "kompaktno zemljište", "loša drenaža"],
      "possible_causes": [
        {
          "cause": "Kiselo zemljište",
          "probability": 0.4,
          "solution": "Dodati kreč (200-400g/m²)",
          "prevention": "Testirati pH svake godine"
        },
        {
          "cause": "Loša drenaža",
          "probability": 0.35,
          "solution": "Aeracija + peskovanje",
          "prevention": "Redovna aeracija 1-2x godišnje"
        },
        {
          "cause": "Premalo svetlosti",
          "probability": 0.25,
          "solution": "Orezati grmove i drveće",
          "prevention": "Izabrati senovitu travu"
        }
      ]
    },
    {
      "id": "fungal_disease",
      "name": "Gljivična bolest",
      "visual_indicators": ["prstenaste mrlje", "pahuljice na travi", "smeđe pege"],
      "possible_causes": [
        {
          "cause": "Preterano zalivanje",
          "probability": 0.45,
          "solution": "Smanjiti zalivanje, poboljšati drenažu",
          "prevention": "Zalivati ujutru, ne uveče"
        },
        {
          "cause": "Visoka vlažnost",
          "probability": 0.35,
          "solution": "Fungicid + povećati košenje",
          "prevention": "Kositi redovno, ne ostavljati pokošenu travu"
        },
        {
          "cause": "Nedostatak hranljivih materija",
          "probability": 0.2,
          "solution": "Đubrenje sa kalijumom",
          "prevention": "Redovno đubrenje u sezoni"
        }
      ]
    },
    {
      "id": "weeds",
      "name": "Korov",
      "visual_indicators": ["različiti oblici listova", "različita boja od trave", "brz rast"],
      "possible_causes": [
        {
          "cause": "Trava ne pokriva dovoljno",
          "probability": 0.4,
          "solution": "Posejati gusto, redovno kositi",
          "prevention": "Održavati gustinu trave"
        },
        {
          "cause": "Oštećen travnjak",
          "probability": 0.35,
          "solution": "Popraviti oštećenja + herbicid",
          "prevention": "Redovno održavanje"
        },
        {
          "cause": "Seme korova iz okoline",
          "probability": 0.25,
          "solution": "Redovno uklanjanje pre cvetanja",
          "prevention": "Kositi pre nego što korov procveta"
        }
      ]
    }
  ]
}
```

---

## Firebase podešavanje

### 1. Kreiranje projekta

```bash
# Instaliraj Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Kreiraj projekt
firebase projects:create lawn-care-ai

# Inicijaliziraj u projektu
firebase init
```

### 2. Firestore pravila

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Korisnici - samo svoje podatke
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
      
      // Istorija analiza
      match /analyses/{analysisId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
    }
    
    // Javne baze podataka
    match /grass_types/{document} {
      allow read: if true;
    }
    
    match /problems/{document} {
      allow read: if true;
    }
  }
}
```

### 3. Storage pravila

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /users/{userId}/{allPaths=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

---

## ML Kit implementacija

### Dodavanje zavisnosti

```yaml
# pubspec.yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.0
  firebase_auth: ^4.16.0
  cloud_firestore: ^4.14.0
  firebase_storage: ^11.6.0
  google_mlkit_image_labeling: ^0.11.0
  geolocator: ^10.1.0
  geocoding: ^2.1.1
  image_picker: ^1.0.7
  permission_handler: ^11.1.0
```

### Image Classifier Service

```dart
import 'package:google_mlkit_image_labeling/google_mlkit_image_labeling.dart';
import 'dart:io';

class ImageClassifierService {
  late ImageLabeler _labeler;
  
  ImageClassifierService() {
    _labeler = ImageLabeler(
      options: ImageLabelerOptions(
        confidenceThreshold: 0.7,
      ),
    );
  }
  
  Future<Map<String, double>> classifyImage(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final labels = await _labeler.processImage(inputImage);
    
    Map<String, double> results = {};
    for (final label in labels) {
      results[label.label] = label.confidence;
    }
    return results;
  }
  
  Future<LawnDiagnosis> diagnoseLawn(File imageFile) async {
    final results = await classifyImage(imageFile);
    
    double greenScore = results['Green grass'] ?? 0;
    double brownScore = results['Brown grass'] ?? 0;
    double bareScore = results['Bare soil'] ?? 0;
    double weedScore = results['Weeds'] ?? 0;
    
    if (weedScore > 0.5) {
      return LawnDiagnosis(
        problem: 'Korov',
        severity: weedScore,
        solutions: ['Herbicid', 'Redovno kositi', 'Posejati gusto'],
      );
    } else if (brownScore > 0.5) {
      return LawnDiagnosis(
        problem: 'Smeđa trava',
        severity: brownScore,
        solutions: ['Povećati zalivanje', 'Đubrenje azotom', 'Proveriti bolest'],
      );
    } else if (bareScore > 0.3) {
      return LawnDiagnosis(
        problem: 'Gole mrlje',
        severity: bareScore,
        solutions: ['Posejati novo seme', 'Zaštititi od gaženja'],
      );
    } else if (greenScore > 0.7) {
      return LawnDiagnosis(
        problem: 'Zdrav travnjak',
        severity: 0,
        solutions: ['Nastavite sa redovnim održavanjem'],
      );
    }
    
    return LawnDiagnosis(
      problem: 'Nepoznat problem',
      severity: 0,
      solutions: ['Slikajte bolje osvetljenu fotografiju'],
    );
  }
  
  void dispose() {
    _labeler.close();
  }
}
```

---

## Google Geocoding implementacija

```dart
import 'package:geolocator/geolocator.dart';
import 'package:geocoding/geocoding.dart';

class LocationService {
  Future<Position> getCurrentLocation() async {
    bool serviceEnabled = await Geolocator.isLocationServiceEnabled();
    if (!serviceEnabled) {
      throw Exception('Servis lokacije nije dostupan');
    }
    
    LocationPermission permission = await Geolocator.checkPermission();
    if (permission == LocationPermission.denied) {
      permission = await Geolocator.requestPermission();
      if (permission == LocationPermission.denied) {
        throw Exception('Dozvola lokacije odbijena');
      }
    }
    
    return await Geolocator.getCurrentPosition();
  }
  
  Future<ClimateZone> getClimateZone(Position position) async {
    List<Placemark> placemarks = await placemarkFromCoordinates(
      position.latitude,
      position.longitude,
    );
    
    if (placemarks.isEmpty) {
      throw Exception('Nije moguće odrediti lokaciju');
    }
    
    Placemark place = placemarks[0];
    String countryCode = place.isoCountryCode ?? '';
    
    // Mapiranje država na klimatske zone
    Map<String, String> countryToZone = {
      'SRB': 'continental',
      'HRV': 'continental',
      'BIH': 'continental',
      'MNE': 'continental',
      'ESP': 'mediterranean',
      'ITA': 'mediterranean',
      'GRC': 'mediterranean',
      'DEU': 'continental',
      'POL': 'continental',
      'SWE': 'cold',
      'NOR': 'cold',
      'FIN': 'cold',
      'USA': 'variable',
      'AUS': 'variable',
    };
    
    String zoneId = countryToZone[countryCode] ?? 'continental';
    
    return ClimateZone(
      id: zoneId,
      country: countryCode,
      city: place.locality ?? '',
    );
  }
}
```

---

## Podešavanje okruženja

### Flutter projekat

```bash
# Kreiraj novi projekt
flutter create lawn_care_ai

# Uđi u direktorijum
cd lawn_care_ai

# Dodaj Firebase
flutterfire configure

# Instaliraj dependencies
flutter pub get
```

### Firebase CLI

```bash
# Instaliraj Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Inicijaliziraj
firebase init

# Deploy pravila
firebase deploy --only firestore:rules
firebase deploy --only storage
```

---

## Redosled razvoja

### Faza 1: Postavljanje (2-3 dana)
- [ ] Kreirati Flutter projekat
- [ ] Povezati Firebase (Auth, Firestore, Storage)
- [ ] Postaviti osnovne pravila sigurnosti
- [ ] Testirati konekciju

### Faza 2: Baza podataka (3-5 dana)
- [ ] Napraviti grass_database.json
- [ ] Napraviti problems_database.json
- [ ] Uneti podatke za sve klimatske zone
- [ ] Testirati učitavanje iz baze

### Faza 3: Lokacija + preporuka (2-3 dana)
- [ ] Implementirati LocationService
- [ ] Implementirati Geocoding
- [ ] Kreirati ekran za preporuku trave
- [ ] Povezati sa bazom podataka

### Faza 4: Uputstvo za sadnju (1-2 dana)
- [ ] Napraviti ekran sa koracima
- [ ] Dodati slike i ilustracije
- [ ] Dodati mogućnost čuvanja favorita

### Faza 5: ML Kit dijagnostika (5-7 dana)
- [ ] Implementirati ImageClassifierService
- [ ] Napraviti ekran za kameru
- [ ] Implementirati dijagnozu
- [ ] Povezati sa bazom rešenja

### Faza 6: Before/After (3-5 dana)
- [ ] Dodati čuvanje slika u Firebase Storage
- [ ] Implementirati poređenje slika
- [ ] Prikazati rezultat poređenja

### Faza 7: UI/UX (3-5 dana)
- [ ] Dizajnirati sve ekrane
- [ ] Dodati animacije
- [ ] Prilagoditi za različite veličine ekrana

### Faza 8: Testiranje (3-5 dana)
- [ ] Unit testovi za servise
- [ ] Widget testovi za ekrane
- [ ] Integration testovi
- [ ] Testiranje na različitim uređajima

---

## Dodatak: OpenAI (opciono)

Ako želiš detaljniju analizu slika, možeš dodati OpenAI:

```dart
class OpenAIService {
  final String apiKey;
  
  OpenAIService({required this.apiKey});
  
  Future<String> analyzeLawnImage(File imageFile) async {
    final bytes = await imageFile.readAsBytes();
    final base64Image = base64Encode(bytes);
    
    final response = await http.post(
      Uri.parse('https://api.openai.com/v1/chat/completions'),
      headers: {
        'Authorization': 'Bearer $apiKey',
        'Content-Type': 'application/json',
      },
      body: jsonEncode({
        'model': 'gpt-4o-mini',
        'messages': [
          {
            'role': 'user',
            'content': [
              {
                'type': 'text',
                'text': 'Analiziraj sliku travnjaka. Identifikuj probleme (bolesti, korov, žute mrlje, gole površine). Predloži rešenja na srpskom jeziku.'
              },
              {
                'type': 'image_url',
                'image_url': {
                  'url': 'data:image/jpeg;base64,$base64Image'
                }
              }
            ]
          }
        ],
        'max_tokens': 1000,
      }),
    );
    
    if (response.statusCode == 200) {
      final data = jsonDecode(response.body);
      return data['choices'][0]['message']['content'];
    } else {
      throw Exception('Greška pri analizi slike');
    }
  }
}
```

### Troškovi OpenAI (mesečno)

| Količina slika | Cena |
|----------------|------|
| 100 slika | ~0.01 USD |
| 1,000 slika | ~0.10 USD |
| 10,000 slika | ~1.00 USD |

---

## Zaključak

| Stavka | Cena |
|--------|------|
| Flutter | Besplatno |
| Firebase | 0 USD (free tier) |
| ML Kit | 0 USD |
| Geocoding | 0 USD |
| OpenAI (opciono) | 0-5 USD |
| Domen | Već postoji |
| **Ukupno** | **0 USD/mes** |

**Vreme razvoja:** ~3-4 nedelje

**Rezultat:** Funkcionalna aplikacija za preporuku trave, dijagnostiku problema i praćenje napretka sadnje.
