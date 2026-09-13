# AI u mobilnim aplikacijama

## Sadržaj
- [ ] [1. Prepoznavanje slika (Computer Vision)](#1-prepoznavanje-slika-computer-vision)
- [ ] [2. Glasovni asistenti i NLP](#2-glasovni-asistenti-i-nlp)
- [ ] [3. Preporuke (Recommendation Systems)](#3-preporuke-recommendation-systems)
- [ ] [4. Chatboti i LLM](#4-chatboti-i-llm)
- [ ] [5. Augmented Reality (AR)](#5-augmented-reality-ar)
- [ ] [6. Prevod u realnom vremenu](#6-prevod-u-realnom-vremenu)
- [ ] [7. Predviđanje (Predictive Analytics)](#7-predviđanje-predictive-analytics)
- [ ] [8. Biometrija i bezbednost](#8-biometrija-i-bezbednost)
- [ ] [9. Detekcija i praćenje](#9-detekcija-i-praćenje)
- [ ] [10. Optimizacija performansi](#10-optimizacija-performansi)
- [ ] [11. Primeri implementacije](#11-primeri-implementacije)
- [ ] [12. Uporedni pregled](#12-uporedni-pregled)
- [ ] [13. Preporuke za početak](#13-preporuke-za-početak)
- [ ] [14. ML Kit za Flutter](#14-ml-kit-za-flutter)

---

## 1. Prepoznavanje slika (Computer Vision)

- [ ] Razumeti koncept

### Šta može:
- Klasifikacija slika (psi, mačke, hrana, itd.)
- Detekcija objekata (bounding box)
- Prepoznavanje lica (filteri, bezbednost)
- OCR (čitanje teksta sa slika)
- Segmentacija (izdvajanje pozadine)

### Primeri aplikacija:
- **Google Lens** - pretraga slika, prevod kamerom
- **Snapchat/Instagram** - AR filteri
- **PlantNet** - prepoznavanje biljaka

### Implementacija:
```dart
// ML Kit - klasifikacija
final classifier = ImageLabeler(
  options: ImageLabelerOptions(confidenceThreshold: 0.5),
);
final labels = await classifier.processImage(inputImage);

// Detekcija lica
final faceDetector = FaceDetector(
  options: FaceDetectorOptions(
    enableContours: true,
    enableLandmarks: true,
  ),
);
final faces = await faceDetector.processImage(inputImage);
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | 85-95% |
| Cena | 0 USD (ML Kit) |
| Latency | 50-200ms (on-device) |

---

## 2. Glasovni asistenti i NLP

- [ ] Razumeti koncept

### Šta može:
- Pretvaranje govora u tekst (STT)
- Pretvaranje teksta u govor (TTS)
- Razumevanje namera (NLU)
- Prevođenje jezika
- Sentiment analiza

### Primeri aplikacija:
- **Siri/Google Assistant** - glasovne komande
- **Alexa** - pametna kuća
- **Otter.ai** - transkripcija sastanaka

### Implementacija:
```dart
// Speech to Text
import 'package:speech_to_text/speech_to_text.dart';

final stt = SpeechToText();
await stt.initialize();
stt.listen(
  onResult: (result) {
    print(result.recognizedWords);
  },
);

// Text to Speech
import 'package:flutter_tts/flutter_tts.dart';

final tts = FlutterTts();
await tts.speak("Zdravo, kako mogu pomoći?");
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | 90-98% |
| Cena | 0 USD (on-device) ili API |
| Latency | 100-500ms |

---

## 3. Preporuke (Recommendation Systems)

- [ ] Razumeti koncept

### Šta može:
- Preporuke proizvoda
- Sadržaj koji se korisniku sviđa
- Predviđanje ponašanja
- Personalizovani feed

### Algoritmi:
- **Collaborative Filtering** - "korisnici koji su kupili X, kupili su i Y"
- **Content-Based** - "slično onome što ste gledali"
- **Hybrid** - kombinacija oba

### Primeri aplikacija:
- **Netflix** - preporuke filmova
- **Spotify** - Discover Weekly
- **TikTok** - For You Page
- **Amazon** - "Customers who bought..."

### Implementacija:
```dart
class RecommendationService {
  Future<List<Product>> getRecommendations(String userId) async {
    final similarUsers = await findSimilarUsers(userId);
    final purchases = await getPurchases(similarUsers);
    final userPurchases = await getUserPurchases(userId);
    
    return purchases
      .where((p) => !userPurchases.contains(p.id))
      .take(10)
      .toList();
  }
}
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | 70-90% |
| Cena | 0 USD (lokalno) ili cloud |
| Složenost | Srednja do visoka |

---

## 4. Chatboti i LLM

- [ ] Razumeti koncept

### Šta može:
- Prirodni jezinterpretacija
- Generisanje teksta
- Odgovori na pitanja
- Customer support
- Kreativno pisanje

### Modeli:
- **GPT-4o** - najprecizniji, skuplji
- **GPT-4o-mini** - brži, jeftiniji
- **Claude** - Anthropic
- **Llama** - open source

### Primeri aplikacija:
- **ChatGPT** - opšti chatbot
- **Copilot** - programiranje
- **Character.AI** - likovi iz filmova

### Implementacija:
```dart
class ChatService {
  final String apiKey;
  
  ChatService({required this.apiKey});
  
  Future<String> sendMessage(String message, List<ChatMessage> history) async {
    final messages = history.map((m) => {
      'role': m.isUser ? 'user' : 'assistant',
      'content': m.text,
    }).toList();
    
    messages.add({'role': 'user', 'content': message});
    
    final response = await http.post(
      Uri.parse('https://api.openai.com/v1/chat/completions'),
      headers: {
        'Authorization': 'Bearer $apiKey',
        'Content-Type': 'application/json',
      },
      body: jsonEncode({
        'model': 'gpt-4o-mini',
        'messages': messages,
        'max_tokens': 1000,
      }),
    );
    
    final data = jsonDecode(response.body);
    return data['choices'][0]['message']['content'];
  }
}
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | 85-95% |
| Cena | 0.01-0.10 USD po zahtevu |
| Latency | 500-2000ms |

---

## 5. Augmented Reality (AR)

- [ ] Razumeti koncept

### Šta može:
- Praćenje lica (filteri)
- Praćenje objekata
- Plane detection (stavljanje objekata na sto)
- SLAM (mapiranje okruženja)

### Primeri aplikacija:
- **Snapchat** - AR filteri
- **Pokemon Go** - gameplay
- **IKEA Place** - nameštaj u sobi
- **Google Maps** - AR navigacija

### Implementacija:
```dart
import 'package:ar_flutter_plugin/ar_flutter_plugin.dart';

final arSession = ARSession();
final arObject = ARNode(
  type: NodeType.localWeb3DModel,
  uri: "https://example.com/model.glb",
  transform: Matrix4.translationValues(0, 0, -1),
);
await arSession.add(arObject);
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | Zavisi od uređaja |
| Cena | 0 USD |
| Latency | Real-time (60fps) |

---

## 6. Prevod u realnom vremenu

- [ ] Razumeti koncept

### Šta može:
- Prevod teksta
- Prevod glasa
- Prevod kamerom (OCR + prevod)

### Primeri aplikacija:
- **Google Translate** - kamera u realnom vremenu
- **DeepL** - precizniji prevodi
- **iTranslate** - glasovni prevod

### Implementacija:
```dart
final response = await http.post(
  Uri.parse('https://translation.googleapis.com/language/translate/v2'),
  headers: {'Authorization': 'Bearer $apiKey'},
  body: {
    'q': 'Hello world',
    'source': 'en',
    'target': 'sr',
  },
);
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | 85-95% |
| Cena | 20 USD/mes (1M karaktera) |

---

## 7. Predviđanje (Predictive Analytics)

- [ ] Razumeti koncept

### Šta može:
- Predviđanje prodaje
- Predviđanje ponašanja korisnika
- Detekcija anomalija
- Risk scoring

### Primeri aplikacija:
- **Finansije** - detekcija prevara
- **Zdravlje** - predviđanje bolesti
- **Marketing** - churn prediction

### Implementacija:
```dart
import 'package:tensorflow_lite_flutter/tensorflow_lite_flutter.dart';

final interpreter = await Interpreter.fromAsset('model.tflite');

var input = [35, 50000, 720]; // godine, prihod, kreditni score
var output = List.filled(1, 0).reshape([1, 1]);

interpreter.run(input, output);
// Output: verovatnoća odobravanja kredita
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | 70-90% |
| Cena | 0 USD (on-device) |

---

## 8. Biometrija i bezbednost

- [ ] Razumeti koncept

### Šta može:
- Face ID / Face Unlock
- Otisak prsta
- Prepoznavanje glasa
- Behavioral biometrics (kako kucaš)

### Primeri aplikacija:
- **Face ID** - otključavanje telefona
- **Banking apps** - verifikacija identiteta
- **PayPal** - plaćanje licem

### Implementacija:
```dart
import 'package:local_auth/local_auth.dart';

final localAuth = LocalAuthentication();
final isAuthenticated = await localAuth.authenticate(
  localizedReason: 'Potvrdite identitet',
  options: AuthenticationOptions(
    stickyAuth: true,
    biometricOnly: true,
  ),
);
```

### Karakteristike:
| Parametar | Vrednost |
|-----------|----------|
| Preciznost | 99%+ |
| Cena | 0 USD |
| Latency | <100ms |

---

## 9. Detekcija i praćenje

- [ ] Razumeti koncept

### Šta može:
- Detekcija lica
- Praćenje lica (za filtere)
- Detekcija objekata
- Praćenje pokreta

### Primeri aplikacija:
- **TikTok** - praćenje lica za efekte
- **Zoom** - virtualne pozadine
- **Fitness apps** - praćenje vežbi

### Implementacija:
```dart
final faceDetector = FaceDetector(
  options: FaceDetectorOptions(
    enableTracking: true,
  ),
);

// U kontinuiranoj petlji (kamera)
final faces = await faceDetector.processImage(inputImage);
for (final face in faces) {
  final rect = face.boundingBox;
  // Primeni filter na osnovu pozicije lica
}
```

---

## 10. Optimizacija performansi

- [ ] Razumeti koncept

### Šta može:
- Predviđanje optimalnog vremena
- Automatsko skaliranje
- Caching predviđanjem
- Battery optimization

### Primeri:
- **Uber** - predviđanje vremena dolaska
- **Google Maps** - predviđanje saobraćaja
- **Spotify** - preloading pesama

---

## 11. Primeri implementacije

- [ ] ML Kit - prepoznavanje slika
- [ ] Speech to Text
- [ ] OpenAI Chatbot
- [ ] Firebase + AI integracija

---

## 12. Uporedni pregled

- [ ] Analizirati sve opcije

| AI upotreba | Kompleksnost | Cena | Latency | Vrednost |
|-------------|-------------|------|---------|----------|
| Prepoznavanje slika | Niska | 0 USD | 50-200ms | Visoka |
| Glasovni asistent | Niska | 0 USD | 100-500ms | Visoka |
| Chatbot/LLM | Niska | 0.01-0.10 USD | 500-2000ms | Visoka |
| Preporuke | Srednja | 0 USD | 100-500ms | Visoka |
| Prevod | Niska | 0-20 USD | 200-1000ms | Srednja |
| AR | Srednja | 0 USD | Real-time | Srednja |
| Predviđanje | Srednja | 0 USD | 50-200ms | Srednja |
| Biometrija | Niska | 0 USD | <100ms | Visoka |

---

## 13. Preporuke za početak

- [ ] Odabrati AI upotrebu

### Najlakše za implementaciju:
1. ML Kit (prepoznavanje slika)
2. Speech to Text
3. Chatbot (OpenAI API)

### Najveća vrednost:
1. Preporuke (povećava engagement)
2. Chatbot (smanjuje troškove supporta)
3. Biometrija (povećava bezbednost)

### Najmanji troškovi:
1. ML Kit (besplatno)
2. Core ML (besplatno)
3. TensorFlow Lite (besplatno)

---

## 14. ML Kit za Flutter - Detaljan vodič

- [ ] Instalirati ML Kit pakete
- [ ] Implementirati Image Labeling
- [ ] Implementirati Face Detection
- [ ] Implementirati Text Recognition
- [ ] Implementirati Barcode Scanning
- [ ] Implementirati Object Detection
- [ ] Implementirati Pose Detection
- [ ] Napraviti kameru u realnom vremenu

### Šta je ML Kit?

Google-ov SDK za mašinsko učenje na mobilnim uređajima. Radi **on-device** (lokalno) ili **cloud** (preko interneta).

### Dve vrste ML Kit-a

| Vrsta | Radi | Cena | Latency |
|-------|------|------|---------|
| **On-device** | Lokalno na telefonu | Besplatno | Brzo (50-200ms) |
| **Cloud** | Preko Google servera | Plaća se | Sporije (200-1000ms) |

### Dostupni paketi za Flutter

```yaml
dependencies:
  # Osnovno
  google_mlkit_image_labeling: ^0.11.0      # Klasifikacija slika
  google_mlkit_object_detection: ^0.11.0    # Detekcija objekata
  google_mlkit_face_detection: ^0.11.0      # Prepoznavanje lica
  google_mlkit_text_recognition: ^0.11.0    # OCR (čitanje teksta)
  google_mlkit_barcode_scanning: ^0.11.0    # Bar kodovi
  google_mlkit_pose_detection: ^0.11.0      # Detekcija poze
  google_mlkit_segmentation: ^0.11.0        # Segmentacija pozadine
```

---

### 1. Image Labeling (Klasifikacija slika)

**Šta radi:** Identifikuje šta je na slici (pas, mačka, hrana, trava...)

**Implementacija:**
```dart
import 'package:google_mlkit_image_labeling/google_mlkit_image_labeling.dart';
import 'dart:io';

class ImageLabelingService {
  late ImageLabeler _labeler;
  
  ImageLabelingService() {
    // On-device model (besplatno)
    _labeler = ImageLabeler(
      options: ImageLabelerOptions(
        confidenceThreshold: 0.5, // Minimalna pouzdanost 50%
      ),
    );
  }
  
  Future<List<ImageLabel>> classifyImage(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final labels = await _labeler.processImage(inputImage);
    return labels;
  }
  
  void dispose() {
    _labeler.close();
  }
}
```

**Korišćenje:**
```dart
final service = ImageLabelingService();
final labels = await service.classifyImage(File('slika.jpg'));

for (final label in labels) {
  print('${label.label}: ${(label.confidence * 100).toStringAsFixed(1)}%');
}
// Output:
// Grass: 87.3%
// Plant: 82.1%
// Green: 75.6%
```

---

### 2. Face Detection (Prepoznavanje lica)

**Šta radi:** Detektuje lica, prepoznaje konture, landmark-ove, osmeh

**Implementacija:**
```dart
import 'package:google_mlkit_face_detection/google_mlkit_face_detection.dart';

class FaceDetectionService {
  late FaceDetector _faceDetector;
  
  FaceDetectionService() {
    _faceDetector = FaceDetector(
      options: FaceDetectorOptions(
        enableContours: true,        // Konture lica
        enableLandmarks: true,       // Oči, nos, usta
        enableClassification: true,  // Osmeh, sleva nadesno
        enableTracking: true,        // Praćenje lica
        minFaceSize: 0.1,            // Minimalna veličina lica
        performanceMode: FaceDetectorMode.accurate,
      ),
    );
  }
  
  Future<List<Face>> detectFaces(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final faces = await _faceDetector.processImage(inputImage);
    return faces;
  }
  
  void dispose() {
    _faceDetector.close();
  }
}
```

**Korišćenje:**
```dart
final service = FaceDetectionService();
final faces = await service.detectFaces(File('slika.jpg'));

for (final face in faces) {
  print('Lice pronađeno:');
  print('  Pozicija: ${face.boundingBox}');
  print('  Osmeh: ${face.smilingProbability}');
  print('  Levo oko otvoreno: ${face.leftEyeOpenProbability}');
  print('  Desno oko otvoreno: ${face.rightEyeOpenProbability}');
  print('  Euler X: ${face.headEulerAngleX}');
  print('  Euler Y: ${face.headEulerAngleY}');
}
```

---

### 3. Text Recognition (OCR)

**Šta radi:** Čita tekst sa slika

**Implementacija:**
```dart
import 'package:google_mlkit_text_recognition/google_mlkit_text_recognition.dart';

class TextRecognitionService {
  late TextRecognizer _recognizer;
  
  TextRecognitionService() {
    // Latinica
    _recognizer = TextRecognizer(script: TextRecognitionScript.latin);
    
    // Ili ćirilica
    // _recognizer = TextRecognizer(script: TextRecognitionScript.cyrillic);
  }
  
  Future<String> recognizeText(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final recognizedText = await _recognizer.processImage(inputImage);
    return recognizedText.text;
  }
  
  Future<List<TextBlock>> getBlocks(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final recognizedText = await _recognizer.processImage(inputImage);
    return recognizedText.blocks;
  }
  
  void dispose() {
    _recognizer.close();
  }
}
```

**Korišćenje:**
```dart
final service = TextRecognitionService();
final text = await service.recognizeText(File('dokument.jpg'));
print('Prepoznat tekst: $text');

final blocks = await service.getBlocks(File('dokument.jpg'));
for (final block in blocks) {
  print('Blok: ${block.text}');
  print('  Pozicija: ${block.boundingBox}');
  for (final line in block.lines) {
    print('  Linija: ${line.text}');
  }
}
```

---

### 4. Barcode Scanning

**Šta radi:** Čita bar kodove i QR kodove

**Implementacija:**
```dart
import 'package:google_mlkit_barcode_scanning/google_mlkit_barcode_scanning.dart';

class BarcodeScanningService {
  late BarcodeScanner _scanner;
  
  BarcodeScanningService() {
    _scanner = BarcodeScanner(
      formats: [
        BarcodeFormat.qrCode,
        BarcodeFormat.code128,
        BarcodeFormat.ean13,
      ],
    );
  }
  
  Future<List<Barcode>> scanBarcodes(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final barcodes = await _scanner.processImage(inputImage);
    return barcodes;
  }
  
  void dispose() {
    _scanner.close();
  }
}
```

**Korišćenje:**
```dart
final service = BarcodeScanningService();
final barcodes = await service.scanBarcodes(File('qr_kod.jpg'));

for (final barcode in barcodes) {
  print('Tip: ${barcode.format}');
  print('Vrednost: ${barcode.rawValue}');
  print('Pozicija: ${barcode.boundingBox}');
}
```

---

### 5. Object Detection (Detekcija objekata)

**Šta radi:** Detektuje i prati objekte u realnom vremenu

**Implementacija:**
```dart
import 'package:google_mlkit_object_detection/google_mlkit_object_detection.dart';

class ObjectDetectionService {
  late ObjectDetector _detector;
  
  ObjectDetectionService() {
    _detector = ObjectDetector(
      options: ObjectDetectorOptions(
        classifyObjects: true,    // Klasifikuj objekte
        multipleObjects: true,    // Više objekata
        mode: DetectorMode.stream, // Za kameru u realnom vremenu
      ),
    );
  }
  
  Future<List<DetectedObject>> detectObjects(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final objects = await _detector.processImage(inputImage);
    return objects;
  }
  
  void dispose() {
    _detector.close();
  }
}
```

---

### 6. Pose Detection (Detekcija poze)

**Šta radi:** Detektuje pozu tela (ruke, noge, glava)

**Implementacija:**
```dart
import 'package:google_mlkit_pose_detection/google_mlkit_pose_detection.dart';

class PoseDetectionService {
  late PoseDetector _detector;
  
  PoseDetectionService() {
    _detector = PoseDetector(
      options: PoseDetectorOptions(
        mode: PoseDetectionMode.stream,
        model: PoseDetectionModel.accurate,
      ),
    );
  }
  
  Future<List<Pose>> detectPose(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final poses = await _detector.processImage(inputImage);
    return poses;
  }
  
  void dispose() {
    _detector.close();
  }
}
```

**Korišćenje:**
```dart
final service = PoseDetectionService();
final poses = await service.detectPose(File('vežba.jpg'));

for (final pose in poses) {
  // Landmark-ovi: nos, levo oko, desno oko, leva ruka, desna ruka...
  final nose = pose.landmarks[PoseLandmarkType.nose];
  final leftShoulder = pose.landmarks[PoseLandmarkType.leftShoulder];
  final rightShoulder = pose.landmarks[PoseLandmarkType.rightShoulder];
  
  print('Nos: ${nose?.position}');
  print('Levo rame: ${leftShoulder?.position}');
  print('Desno rame: ${rightShoulder?.position}');
}
```

---

### 7. Kamera u realnom vremenu

**Primer sa kamerom:**
```dart
import 'package:camera/camera.dart';
import 'package:google_mlkit_image_labeling/google_mlkit_image_labeling.dart';

class CameraMLScreen extends StatefulWidget {
  @override
  _CameraMLScreenState createState() => _CameraMLScreenState();
}

class _CameraMLScreenState extends State<CameraMLScreen> {
  CameraController? _controller;
  ImageLabeler? _labeler;
  List<ImageLabel> _labels = [];
  bool _isProcessing = false;
  
  @override
  void initState() {
    super.initState();
    _initCamera();
    _labeler = ImageLabeler(
      options: ImageLabelerOptions(confidenceThreshold: 0.5),
    );
  }
  
  Future<void> _initCamera() async {
    final cameras = await availableCameras();
    _controller = CameraController(cameras[0], ResolutionPreset.medium);
    await _controller!.initialize();
    _controller!.startImageStream((image) {
      if (!_isProcessing) {
        _processImage(image);
      }
    });
    setState(() {});
  }
  
  Future<void> _processImage(CameraImage image) async {
    _isProcessing = true;
    
    final inputImage = InputImage.fromCameraImage(image);
    if (inputImage != null) {
      final labels = await _labeler!.processImage(inputImage);
      setState(() {
        _labels = labels;
      });
    }
    
    _isProcessing = false;
  }
  
  @override
  Widget build(BuildContext context) {
    if (_controller == null || !_controller!.value.isInitialized) {
      return Center(child: CircularProgressIndicator());
    }
    
    return Stack(
      children: [
        CameraPreview(_controller!),
        Positioned(
          bottom: 20,
          left: 20,
          child: Container(
            padding: EdgeInsets.all(10),
            color: Colors.black54,
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: _labels.map((label) {
                return Text(
                  '${label.label}: ${(label.confidence * 100).toStringAsFixed(1)}%',
                  style: TextStyle(color: Colors.white, fontSize: 16),
                );
              }).toList(),
            ),
          ),
        ),
      ],
    );
  }
  
  @override
  void dispose() {
    _controller?.dispose();
    _labeler?.close();
    super.dispose();
  }
}
```

---

### 8. Primer: Aplikacija za travnjak

```dart
class LawnDiagnosisService {
  late ImageLabeler _labeler;
  
  LawnDiagnosisService() {
    _labeler = ImageLabeler(
      options: ImageLabelerOptions(confidenceThreshold: 0.5),
    );
  }
  
  Future<LawnDiagnosis> diagnose(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final labels = await _labeler.processImage(inputImage);
    
    double greenScore = 0;
    double brownScore = 0;
    double bareScore = 0;
    
    for (final label in labels) {
      switch (label.label.toLowerCase()) {
        case 'grass':
        case 'green':
        case 'lawn':
          greenScore = label.confidence;
          break;
        case 'brown':
        case 'dead grass':
        case 'dry':
          brownScore = label.confidence;
          break;
        case 'soil':
        case 'dirt':
        case 'bare':
          bareScore = label.confidence;
          break;
      }
    }
    
    if (greenScore > 0.7) {
      return LawnDiagnosis(
        status: LawnStatus.healthy,
        message: 'Trava je zdrava!',
      );
    } else if (brownScore > 0.5) {
      return LawnDiagnosis(
        status: LawnStatus.dry,
        message: 'Trava je suva - povećajte zalivanje',
      );
    } else if (bareScore > 0.3) {
      return LawnDiagnosis(
        status: LawnStatus.bareSpots,
        message: 'Gole mrlje - posejte novo seme',
      );
    }
    
    return LawnDiagnosis(
      status: LawnStatus.unknown,
      message: 'Nije moguće dijagnostikovati',
    );
  }
}
```

---

### Instalacija

```bash
# Android
# android/app/build.gradle
android {
    defaultConfig {
        minSdkVersion 21  // ML Kit zahteva minSdk 21
    }
}

# iOS
# ios/Podfile
platform :ios, '13.0'  // ML Kit zahteva iOS 13+
```

### Resursi

- [ML Kit dokumentacija](https://developers.google.com/ml-kit)
- [Flutter ML Kit paketi](https://pub.dev/packages?q=mlkit)
- [GitHub primeri](https://github.com/googlesamples/mlkit)

---

## 14. ML Kit za Flutter

- [ ] Instalirati ML Kit pakete
- [ ] Konfigurisati Android (minSdk 21)
- [ ] Konfigurisati iOS (iOS 13+)
- [ ] Testirati Image Labeling
- [ ] Testirati Face Detection
- [ ] Testirati Text Recognition
- [ ] Testirati Barcode Scanning

### Uvod

ML Kit je Google-ov SDK za mašinsko učenje na mobilnim uređajima. Za Flutter koristiš `google_mlkit_*` pakete.

### Vrste ML Kit-a

| Vrsta | Radi | Cena | Latency |
|-------|------|------|---------|
| **On-device** | Lokalno na telefonu | Besplatno | Brzo (50-200ms) |
| **Cloud** | Preko Google servera | Plaća se | Sporije (200-1000ms) |

### Dostupni paketi

```yaml
dependencies:
  google_mlkit_image_labeling: ^0.11.0      # Klasifikacija slika
  google_mlkit_object_detection: ^0.11.0    # Detekcija objekata
  google_mlkit_face_detection: ^0.11.0      # Prepoznavanje lica
  google_mlkit_text_recognition: ^0.11.0    # OCR (čitanje teksta)
  google_mlkit_barcode_scanning: ^0.11.0    # Bar kodovi
  google_mlkit_pose_detection: ^0.11.0      # Detekcija poze
  google_mlkit_segmentation: ^0.11.0        # Segmentacija pozadine
```

### Konfiguracija

**Android (android/app/build.gradle):**
```gradle
android {
    defaultConfig {
        minSdkVersion 21  // ML Kit zahteva minSdk 21
    }
}
```

**iOS (ios/Podfile):**
```ruby
platform :ios, '13.0'  // ML Kit zahteva iOS 13+
```

---

### Image Labeling (Klasifikacija slika)

**Šta radi:** Identifikuje šta je na slici

**Implementacija:**
```dart
import 'package:google_mlkit_image_labeling/google_mlkit_image_labeling.dart';
import 'dart:io';

class ImageLabelingService {
  late ImageLabeler _labeler;
  
  ImageLabelingService() {
    _labeler = ImageLabeler(
      options: ImageLabelerOptions(
        confidenceThreshold: 0.5,
      ),
    );
  }
  
  Future<List<ImageLabel>> classifyImage(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final labels = await _labeler.processImage(inputImage);
    return labels;
  }
  
  void dispose() {
    _labeler.close();
  }
}
```

**Korišćenje:**
```dart
final service = ImageLabelingService();
final labels = await service.classifyImage(File('slika.jpg'));

for (final label in labels) {
  print('${label.label}: ${(label.confidence * 100).toStringAsFixed(1)}%');
}
// Output:
// Grass: 87.3%
// Plant: 82.1%
// Green: 75.6%
```

---

### Face Detection (Prepoznavanje lica)

**Šta radi:** Detektuje lica, konture, landmark-ove, osmeh

**Implementacija:**
```dart
import 'package:google_mlkit_face_detection/google_mlkit_face_detection.dart';

class FaceDetectionService {
  late FaceDetector _faceDetector;
  
  FaceDetectionService() {
    _faceDetector = FaceDetector(
      options: FaceDetectorOptions(
        enableContours: true,
        enableLandmarks: true,
        enableClassification: true,
        enableTracking: true,
        minFaceSize: 0.1,
        performanceMode: FaceDetectorMode.accurate,
      ),
    );
  }
  
  Future<List<Face>> detectFaces(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final faces = await _faceDetector.processImage(inputImage);
    return faces;
  }
  
  void dispose() {
    _faceDetector.close();
  }
}
```

**Korišćenje:**
```dart
final service = FaceDetectionService();
final faces = await service.detectFaces(File('slika.jpg'));

for (final face in faces) {
  print('Lice pronađeno:');
  print('  Pozicija: ${face.boundingBox}');
  print('  Osmeh: ${face.smilingProbability}');
  print('  Levo oko: ${face.leftEyeOpenProbability}');
  print('  Desno oko: ${face.rightEyeOpenProbability}');
}
```

---

### Text Recognition (OCR)

**Šta radi:** Čita tekst sa slika

**Implementacija:**
```dart
import 'package:google_mlkit_text_recognition/google_mlkit_text_recognition.dart';

class TextRecognitionService {
  late TextRecognizer _recognizer;
  
  TextRecognitionService() {
    _recognizer = TextRecognizer(script: TextRecognitionScript.latin);
    // Ili ćirilica:
    // _recognizer = TextRecognizer(script: TextRecognitionScript.cyrillic);
  }
  
  Future<String> recognizeText(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final recognizedText = await _recognizer.processImage(inputImage);
    return recognizedText.text;
  }
  
  void dispose() {
    _recognizer.close();
  }
}
```

---

### Barcode Scanning

**Šta radi:** Čita bar kodove i QR kodove

**Implementacija:**
```dart
import 'package:google_mlkit_barcode_scanning/google_mlkit_barcode_scanning.dart';

class BarcodeScanningService {
  late BarcodeScanner _scanner;
  
  BarcodeScanningService() {
    _scanner = BarcodeScanner(
      formats: [
        BarcodeFormat.qrCode,
        BarcodeFormat.code128,
        BarcodeFormat.ean13,
      ],
    );
  }
  
  Future<List<Barcode>> scanBarcodes(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final barcodes = await _scanner.processImage(inputImage);
    return barcodes;
  }
  
  void dispose() {
    _scanner.close();
  }
}
```

---

### Kamera u realnom vremenu

```dart
import 'package:camera/camera.dart';
import 'package:google_mlkit_image_labeling/google_mlkit_image_labeling.dart';

class CameraMLScreen extends StatefulWidget {
  @override
  _CameraMLScreenState createState() => _CameraMLScreenState();
}

class _CameraMLScreenState extends State<CameraMLScreen> {
  CameraController? _controller;
  ImageLabeler? _labeler;
  List<ImageLabel> _labels = [];
  bool _isProcessing = false;
  
  @override
  void initState() {
    super.initState();
    _initCamera();
    _labeler = ImageLabeler(
      options: ImageLabelerOptions(confidenceThreshold: 0.5),
    );
  }
  
  Future<void> _initCamera() async {
    final cameras = await availableCameras();
    _controller = CameraController(cameras[0], ResolutionPreset.medium);
    await _controller!.initialize();
    _controller!.startImageStream((image) {
      if (!_isProcessing) {
        _processImage(image);
      }
    });
    setState(() {});
  }
  
  Future<void> _processImage(CameraImage image) async {
    _isProcessing = true;
    final inputImage = InputImage.fromCameraImage(image);
    if (inputImage != null) {
      final labels = await _labeler!.processImage(inputImage);
      setState(() {
        _labels = labels;
      });
    }
    _isProcessing = false;
  }
  
  @override
  Widget build(BuildContext context) {
    if (_controller == null || !_controller!.value.isInitialized) {
      return Center(child: CircularProgressIndicator());
    }
    
    return Stack(
      children: [
        CameraPreview(_controller!),
        Positioned(
          bottom: 20,
          left: 20,
          child: Container(
            padding: EdgeInsets.all(10),
            color: Colors.black54,
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: _labels.map((label) {
                return Text(
                  '${label.label}: ${(label.confidence * 100).toStringAsFixed(1)}%',
                  style: TextStyle(color: Colors.white, fontSize: 16),
                );
              }).toList(),
            ),
          ),
        ),
      ],
    );
  }
  
  @override
  void dispose() {
    _controller?.dispose();
    _labeler?.close();
    super.dispose();
  }
}
```

---

### Primer: Dijagnostika travnjaka

```dart
class LawnDiagnosisService {
  late ImageLabeler _labeler;
  
  LawnDiagnosisService() {
    _labeler = ImageLabeler(
      options: ImageLabelerOptions(confidenceThreshold: 0.5),
    );
  }
  
  Future<LawnDiagnosis> diagnose(File imageFile) async {
    final inputImage = InputImage.fromFile(imageFile);
    final labels = await _labeler.processImage(inputImage);
    
    double greenScore = 0;
    double brownScore = 0;
    double bareScore = 0;
    
    for (final label in labels) {
      switch (label.label.toLowerCase()) {
        case 'grass':
        case 'green':
        case 'lawn':
          greenScore = label.confidence;
          break;
        case 'brown':
        case 'dead grass':
        case 'dry':
          brownScore = label.confidence;
          break;
        case 'soil':
        case 'dirt':
        case 'bare':
          bareScore = label.confidence;
          break;
      }
    }
    
    if (greenScore > 0.7) {
      return LawnDiagnosis(
        status: LawnStatus.healthy,
        message: 'Trava je zdrava!',
      );
    } else if (brownScore > 0.5) {
      return LawnDiagnosis(
        status: LawnStatus.dry,
        message: 'Trava je suva - povećajte zalivanje',
      );
    } else if (bareScore > 0.3) {
      return LawnDiagnosis(
        status: LawnStatus.bareSpots,
        message: 'Gole mrlje - posejte novo seme',
      );
    }
    
    return LawnDiagnosis(
      status: LawnStatus.unknown,
      message: 'Nije moguće dijagnostikovati',
    );
  }
}
```

---

### Resursi

- [ML Kit dokumentacija](https://developers.google.com/ml-kit)
- [Flutter ML Kit paketi](https://pub.dev/packages?q=mlkit)
- [GitHub primeri](https://github.com/googlesamples/mlkit)

---

## Beleške
- Dodati nove teme po potrebi
- Ažurirati cene kada se promene
- Dodavati primere iz prakse
