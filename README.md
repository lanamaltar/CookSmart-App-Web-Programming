# CookSmart App

Ovo je jednostavna Angular aplikacija za pretraživanje, filtriranje i pregled recepata.

Što radi aplikacija
- Omogućuje prijavu/registraciju korisnika.
- Filtriranje recepata prema dostupnim namirnicama.
- Prikazuje listu recepata u responzivnom gridu.
- Omogućava filtriranje po kategorijama i pretraživanje po imenu recepta.
- Omogućava izradu liste za kupovinu, njezino uređivanje i preuzimanje.
- Pruža stranicu sa detaljima za svaki recept.

Kako pokrenuti lokalno
```powershell
cd recipe-app
npm install
ng serve --proxy-config proxy.conf.json
```

```powershell
cd server
npm install
npm run dev
```

## DETALJI:

FRONTEND
Framework 
- Angular 20.x (TypeScript, Angular CLI) — glavna aplikacija i komponente
- Angular Router (navigacija, RouterLink)
  
Jezici i stilovi
- TypeScript (TS)
- HTML5
- SCSS / Sass za stilove (primjeri: src/app/home/home.scss, src/app/layout/page-shell.scss)
  
Angular feature set
- Standalone Components (imports u @Component)
- Reactive Forms (ReactiveFormsModule) i Template/FormsModule (form validation — login komponenta)
- Services (npr. AuthService, ShoppingListService)

Layout / responzivnost
- CSS Grid + Flexbox za responsive layout (benefits, featured, cards)
- CSS varijable za dinamicke vrijednosti (npr. --sidebar-width, --accent, --accent-border, --side-pad za teme / boje)
- Media queries za breakpoint (npr. overlay sidebar <= 880px)
- Boxicons CDN 

State / asinkronost
- RxJS (observables preko Angular servisa)




BACKEND:
index.js
- Postavljanje Express app
- Pozivanje connectDB() za DB konekciju
- Autentikacija
    - Rute: POST /api/auth/register, POST /api/auth/log-in
    - JWT autentikacija preko authMiddleware
- Pokretanje servera
- Favorites modul
    - POST /api/favorites → dodaj recept u favorite
    - GET /api/favorites → dohvati favorite (uz minimalne recipe info)
    - DELETE /api/favorites/:id ili /by-recipe/:recipeId
 
TheMealDB (vanjski API)
- https://www.themealdb.com/
- Backend radi kao proxy prema TheMealDB
- Normalizacija podataka: svi sastojci i mjere standardizirani za frontend
- HTTP zahtjevi prema TheMealDB
- Funkcionalnosti:
    - Pretraga jela po sastojku
      - Endpoint: GET /api/external/mealdb?ingredient=…&full=&limit=
    - Dohvat svih kategorija jela
      - Endpoint: GET /api/external/mealdb/categories
    - Dohvat jela po kategoriji
      - Endpoint: GET /api/external/mealdb/by-category?c=…
    - Pretraga jela po nazivu
      - Endpoint: GET /api/external/mealdb/search?q=…
    - Dohvat detalja jela po ID-u
      - Endpoint: GET /api/external/mealdb/:id

db.js 
- Povezuje se na MongoDB
- connectDB():
- Exporti helper funkcija:
    - usersCollection() — vraca referencu na kolekciju users 
    - recipesCollection() — vraca referencu na kolekciju recipes
    - ObjectId iz mongodb se takoder eksportira za kreiranje/parsirane id-eva
- Ukratko: centralno mjesto za inicijalizaciju DB veze, indeksiranje i dohvat kolekcija 


auth.js 
- Sadrži sve funkcije vezane uz autentikaciju: hashanje lozinki, registraciju, login i middleware za provjeru JWT tokena
- Koristi crypto.randomBytes za generiranje salt-a i crypto.pbkdf2 za hashiranje lozinke.
- JWT sign/verify koristi jsonwebtoken
- register(req,res):
    - Validira tipove (string), email format (regex) i minimalnu duljinu lozinke 
    - Provjerava postoji li vec korisnik (preko usersCollection())
    - Hashira lozinku (hash + salt) i umece novog korisnika u users kolekciju (spremi passwordHash i passwordSalt)
    - Vraca odmah JWT token + osnovne podatke korisnika (tako da frontend može tretirati registraciju kao login)
- login(req,res):
    - Dohvati korisnika po emailu, hashira poslan password s pohranjenim salt-om te usporeduje hashove. Ako je ok — vraca token i user objekt.
- credentialsFine(email,password) i create_token(user) helperi za provjere i izradu tokena
- authMiddleware(req,res,next):
    - Cita Authorization header (podržava format Bearer <token>), verificira token i postavlja req.user = payload ako je token ok; inace vraca 401
      
shopping List modul 
- Glavne funkcionalnosti (REST API):
      - GET /api/shopping-list → dohvati sve stavke korisnika
      - POST /api/shopping-list → dodaj novu stavku (ime, kolicina)
      - POST /api/shopping-list/bulk-from-recipe → dodaj više stavki odjednom iz recepta
      - PATCH /api/shopping-list/:id → ažuriraj stavku (naziv, kolicinu, status checked)
      - DELETE /api/shopping-list/:id → obriši jednu stavku
      - DELETE /api/shopping-list → obriši sve stavke korisnika
      - GET /api/shopping-list/export → izvoz liste u TXT format
Autentikacija:
- Sve rute koriste authMiddleware (JWT) → samo prijavljeni korisnici mogu upravljati svojom listom





