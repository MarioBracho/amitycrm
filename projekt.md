# Amity Drinks CRM - Projektová dokumentace

## Přehled projektu

**Název:** Amity Drinks CRM
**URL:** http://amitycrm.cz
**Založeno:** Leden 2025
**Základ:** [Atomic CRM](https://github.com/marmelab/atomic-crm) od Marmelab
**GitHub záloha:** https://github.com/MarioBracho/amitycrm

---

## Přístupové údaje

### Supabase (Backend/Databáze)

- **Dashboard:** https://supabase.com/dashboard
- **Project ID:** `ooemubaafwevtuayasij`
- **URL:** `https://ooemubaafwevtuayasij.supabase.co`
- **Anon Key:** `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Im9vZW11YmFhZndldnR1YXlhc2lqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Njc2NjczMjksImV4cCI6MjA4MzI0MzMyOX0.HWwdc416dj3lnq8pSZNvK5WCNLKjVOS8OG_QDXfJ1KU`

### WEDOS Hosting (FTP)

- **Server:** `390014.w14.wedos.net`
- **Uživatel:** `w390014`
- **Heslo:** `x4dW4Jw`
- **Port:** `21`
- **Webový adresář:** `/www/`

### Supabase Auth - Callback URLs

V Supabase Dashboard → Authentication → URL Configuration:
- **Site URL:** `http://amitycrm.cz`
- **Redirect URLs:** `http://amitycrm.cz/auth-callback.html`

---

## Technologie

- **Frontend:** React 19 + TypeScript + Vite
- **UI:** Shadcn UI + Tailwind CSS v4
- **Backend:** Supabase (PostgreSQL + REST API + Auth)
- **Routing:** React Router v7
- **Data Fetching:** TanStack Query (React Query)
- **Jazyk:** Čeština (ra-language-czech)

---

## Struktura projektu

```
atomic-crm/
├── src/
│   ├── App.tsx                    # Hlavní komponenta s brandingem
│   ├── components/
│   │   ├── atomic-crm/            # CRM aplikační kód
│   │   ├── admin/                 # Shadcn Admin Kit (lze upravovat)
│   │   └── ui/                    # Shadcn UI komponenty (lze upravovat)
│   └── ...
├── public/
│   ├── amity-logo.png             # Logo pro favicon
│   └── logos/
│       └── amity-logo.png         # Logo pro aplikaci
├── supabase/
│   ├── functions/                 # Edge funkce
│   └── migrations/                # Databázové migrace
├── dist/                          # Build výstup (nahrát na server)
├── .env.production.local          # Supabase credentials pro build
└── projekt.md                     # Tato dokumentace
```

---

## Příkazy pro vývoj

```bash
# Instalace závislostí
make install

# Spuštění lokálně (s lokální Supabase)
make start

# Spuštění demo verze (bez databáze)
make start-demo

# Zastavení
make stop

# Testy
make test

# Type checking
make typecheck

# Lint
make lint
```

---

## Nasazení aktualizací

### 1. Provedení změn v kódu

Uprav soubory v `src/` podle potřeby.

### 2. Build produkční verze

```bash
export VITE_SUPABASE_URL="https://ooemubaafwevtuayasij.supabase.co"
export VITE_SUPABASE_ANON_KEY="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Im9vZW11YmFhZndldnR1YXlhc2lqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Njc2NjczMjksImV4cCI6MjA4MzI0MzMyOX0.HWwdc416dj3lnq8pSZNvK5WCNLKjVOS8OG_QDXfJ1KU"
export VITE_IS_DEMO="false"
make build
```

**DŮLEŽITÉ:** Proměnné prostředí MUSÍ být exportovány před buildem!

### 3. Nahrání na server

Použij FileZilla:
- **Host:** `390014.w14.wedos.net`
- **User:** `w390014`
- **Password:** `x4dW4Jw`

Nahraj obsah složky `dist/` do `/www/` na serveru.

**Hlavní soubory:**
- `dist/index.html` → `/www/index.html`
- `dist/assets/*` → `/www/assets/`
- `dist/.htaccess` → `/www/.htaccess` (důležité pro React Router!)

### 4. Změny v databázi

```bash
# Vytvoření nové migrace
npx supabase migration new nazev_migrace

# Aplikování na cloud
npx supabase db push
```

---

## Správa uživatelů

### Přidání nového uživatele

1. Přihlaš se jako admin na http://amitycrm.cz
2. Klikni na avatar → **Uživatelé** (nebo jdi na `/sales`)
3. Klikni **Pozvat uživatele**
4. Vyplň email, jméno, příjmení
5. Zaškrtni **Administrator** pouze pro další adminy
6. Uživatel dostane email s odkazem na nastavení hesla

### Smazání uživatele (přes SQL)

V Supabase Dashboard → SQL Editor:

```sql
-- Najdi ID uživatele
SELECT id FROM auth.users WHERE email = 'email@example.com';

-- Smaž (nahraď ID)
DELETE FROM sales WHERE user_id = 'USER-ID-HERE';
DELETE FROM auth.users WHERE id = 'USER-ID-HERE';
```

### Role uživatelů

- **Admin** (`administrator=true`): Může spravovat uživatele
- **Běžný uživatel** (`administrator=false`): Vidí všechna data, nemůže spravovat uživatele

---

## Branding

### Logo

- **Soubor:** `public/logos/amity-logo.png` a `public/amity-logo.png`
- **Konfigurace:** `src/App.tsx`

```typescript
const App = () => (
  <CRM
    lightModeLogo="/logos/amity-logo.png"
    darkModeLogo="/logos/amity-logo.png"
    title="Amity"
  />
);
```

### Název v záložce prohlížeče

**Soubor:** `index.html` (řádek 14)
```html
<title>Amity Drinks CRM</title>
```

---

## Troubleshooting

### Chyba "supabaseUrl is required"

Proměnné prostředí nebyly exportovány před buildem. Řešení:
```bash
export VITE_SUPABASE_URL="..."
export VITE_SUPABASE_ANON_KEY="..."
export VITE_IS_DEMO="false"
make build
```

### 404 při obnovení stránky

Chybí `.htaccess` soubor. Nahraj `dist/.htaccess` do `/www/` na serveru.

### Logo se nezobrazuje

1. Zkontroluj cestu v `src/App.tsx`
2. Ověř, že soubor existuje v `dist/logos/`
3. Po buildu nahraj celou složku `dist/logos/` na server

### Nelze smazat uživatele v Supabase Dashboard

Použij SQL Editor - viz sekce "Smazání uživatele" výše.

---

## Zálohování

### Kód

GitHub repozitář: https://github.com/MarioBracho/amitycrm

```bash
git add .
git commit -m "Popis změn"
git push
```

### Databáze

V Supabase Dashboard → Database → Backups

Nebo export dat přes CRM:
- Kontakty → Export CSV

---

## Kontakty a podpora

- **Atomic CRM dokumentace:** https://marmelab.com/atomic-crm/
- **Supabase dokumentace:** https://supabase.com/docs
- **GitHub issues:** https://github.com/marmelab/atomic-crm/issues

---

## Historie změn

### Leden 2025 - Počáteční nasazení

- Naklonován Atomic CRM z GitHub
- Vytvořen Supabase cloud projekt
- Nasazeny databázové migrace a edge funkce
- Nakonfigurován branding Amity Drinks (logo, název)
- Nasazeno na WEDOS hosting (amitycrm.cz)
- Konfigurována autentizace uživatelů
- Vytvořen první admin účet

---

## Důležité soubory

| Soubor | Účel |
|--------|------|
| `.env.production.local` | Supabase přihlašovací údaje pro build |
| `src/App.tsx` | Branding (logo, název) |
| `index.html` | HTML titulek, favicon |
| `public/logos/amity-logo.png` | Logo aplikace |
| `dist/` | Build výstup k nahrání na server |
| `dist/.htaccess` | Apache URL rewriting pro React Router |
| `projekt.md` | Tato dokumentace |
