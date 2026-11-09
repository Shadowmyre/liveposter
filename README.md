# liveposter
Live Poster – postere interactive cu realitate augmentată
Documentație de prezentare a aplicației  |  Cod sursă: github.com/Shadowmyre/liveposter  |  Aplicație web publicată prin Vercel
1. Funcționalitatea aplicației
Live Poster este o aplicație web care transformă un poster static (tipărit sau afișat pe ecran) într-o experiență animată. Utilizatorul deschide linkul aplicației în browserul telefonului, apasă START, acordă accesul la cameră și îndreaptă telefonul spre poster. Aplicația recunoaște imaginea, iar peste poster, în poziția și la scara lui, se redă un videoclip asociat. Când posterul iese din cadru, redarea se oprește. Nu este necesară instalarea niciunei aplicații.
•	Recunoaștere multiplă: în configurația curentă sunt definite 5 ținte (Avastar, car, coffee, food, method), fiecare cu propriul videoclip.
•	Încărcare ghidată: ecran cu progresul resurselor (motor AR, cameră, date țintă, videoclipuri, imagini de referință), mesaje de eroare în limba română și detectarea dispozitivelor mobile.
•	Configurare prin manifest: perechile imagine–video sunt descrise în fișierul assets/manifest.json, generat automat cu scriptul tools/generate-manifest.py; scripturile de optimizare comprimă imaginile și videoclipurile.
2. Inteligența ambientală și realitatea augmentată
Inteligența ambientală (Ambient Intelligence – AmI) descrie medii în care tehnologia este integrată discret în spațiul fizic și este sensibilă la context și la prezența utilizatorului, oferind servicii în mod proactiv, fără interacțiune explicită (meniuri, comenzi). Elementele definitorii sunt: omniprezența, percepția contextului prin senzori, adaptarea răspunsului și interacțiunea naturală.
Realitatea augmentată (AR) suprapune în timp real conținut digital (video, obiecte 3D, text) peste imaginea lumii reale, păstrând legătura geometrică dintre ele; spre deosebire de realitatea virtuală, utilizatorul rămâne în mediul real. Aplicația folosește AR bazată pe markere de tip imagine (image tracking), iar procesarea rulează în browser (WebAR).
Legătura cu aplicația: posterul devine o interfață pasivă din mediul înconjurător, iar camera telefonului este senzorul de context. Aplicația detectează automat ce obiect se află în fața utilizatorului și răspunde cu informația potrivită, fără nicio comandă. Este o aplicație AmI la scară mică; într-o expoziție sau la o conferință, mai multe postere formează un spațiu inteligent în care fiecare vizitator primește conținut în funcție de ceea ce privește.
3. Modul în care a fost gândită aplicația
Aplicația pornește de la proiectul open-source arspace (emb-ai), construit pe MindAR, și a fost adaptată: interfață și mesaje în română, încărcare cu progres, tratarea erorilor de cameră și HTTPS. Fluxul de funcționare este: (1) se citește manifestul; (2) se descarcă imaginile de referință și se pregătesc videoclipurile (mute, în buclă, playsinline, necesar pe iOS); (3) se pornește MindAR cu fișierul .mind compilat din imaginile țintă; (4) pentru fiecare țintă se creează o ancoră 3D cu un plan (PlaneGeometry) pe care se aplică videoclipul ca textură (VideoTexture); (5) evenimentele onTargetFound / onTargetLost pornesc, respectiv opresc videoclipul. Raportul de aspect al planului este preluat din imaginea de referință, pentru ca videoclipul să acopere corect posterul. Pe mobil, pornirea camerei se face la atingerea butonului START, cerință a browserelor privind redarea video și accesul la cameră.
4. Mediul de dezvoltare și librării folosite
Componentă	Rol
HTML5, CSS3, JavaScript (module ES)	Interfața și logica aplicației (js/app.js, css/styles.css); fără pași de build, dependențele sunt încărcate prin import map
MindAR 1.2.5 (mind-ar-js)	Recunoașterea și urmărirea imaginilor în timp real; fișierul targets.mind este generat cu MindAR Compiler
Three.js 0.160.0	Randarea 3D peste fluxul camerei: scenă, ancore, plan, VideoTexture
WebRTC / getUserMedia, WebGL	Acces la cameră și randare accelerată hardware în browser
Python 3, Pillow, FFmpeg	Scripturi auxiliare: optimizare imagini și videoclipuri, generare manifest
Git / GitHub, Vercel	Versionarea codului și publicarea automată (HTTPS, obligatoriu pentru accesul la cameră)

5. Avantaje și dezavantaje
Avantaje	Dezavantaje
• Rulează în browser, fără instalare, pe orice telefon modern
• Costuri reduse: site static, hosting gratuit pe Vercel
• Experiență imersivă, cu mesaje clare în română
• Cod simplu, fără build, ușor de extins
• Recunoaștere pe mai multe postere în aceeași sesiune	• Adăugarea unui poster cere modificări în cod și recompilarea fișierului .mind
• Necesită internet, HTTPS și cameră; dependențe încărcate de pe CDN
• Videoclipul Avastar are ~16 MB, încărcare lentă pe date mobile
• Calitatea recunoașterii depinde de lumină și de imaginea țintă
• Fără statistici, autentificare sau sunet implicit (videoclipuri mute)

6. Posibile dezvoltări ulterioare
Modul de administrare pentru adăugarea posterelor din aplicație (nu din cod). Pe Vercel sistemul de fișiere este doar pentru citire la rulare, deci posterele noi nu pot fi scrise în folderul assets; modulul trebuie să folosească stocare externă:
•	Pagină /admin protejată prin autentificare, cu formular de încărcare a imaginii poster (JPG) și a videoclipului (MP4), titlu și opțiuni de afișare, plus listare, înlocuire și ștergere de postere.
•	Stocare și bază de date: fișierele merg în Vercel Blob (sau Supabase / Cloudflare R2), iar metadatele (titlu, URL, dimensiuni, ordine) într-o bază de date (Vercel Postgres / KV).
•	Compilare automată a țintelor: compilatorul MindAR rulează în browser, deci fișierul .mind se regenerează în pagina de administrare după fiecare modificare și se încarcă în stocare; ordinea imaginilor din baza de date trebuie să coincidă cu indexul ancorelor.
•	API (funcții serverless Vercel): un endpoint /api/manifest returnează dinamic lista posterelor, înlocuind fișierul static manifest.json; aplicația AR rămâne neschimbată.
•	Alte îmbunătățiri: compresie automată a videoclipurilor la încărcare, memorare în cache (service worker/PWA), conținut 3D, audio sau linkuri interactive, statistici de vizualizare, suport pentru mai multe limbi.
