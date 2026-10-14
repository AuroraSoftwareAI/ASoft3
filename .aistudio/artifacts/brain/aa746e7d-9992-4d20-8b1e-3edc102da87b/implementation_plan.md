# Aurora Terminal: Zaawansowany Terminal Android z Obsługą SSH, Bezpieczeństwem Kluczy i Asystentem Gemini AI

Aplikacja Aurora Terminal to mobilne środowisko terminalowe łączące bezpośrednią lokalną powłokę Androida (`/system/bin/sh`), w pełni funkcjonalnego klienta SSH z generatorem kluczy RSA/importem plików oraz wielomodułowego asystenta Gemini AI z podziałem na tryb konwersacyjny **Zapytaj**, automatyczny planista celów **Wykonuj** (krok po kroku z autoryzacją poleceń) oraz pasywnego doradcę **Live Helper** (audio/tekst bez możliwości samowolnej edycji kodu).

---

## User Review & Critical Decisions

> [!IMPORTANT]
> Na podstawie odpowiedzi udzielonych w Fazie 1 zatwierdzono kluczowe założenia projektowe:

- **Potwierdzona powłoka lokalna**: Bezpośrednie uruchamianie procesów systemowych Androida (`/system/bin/sh`) z wbudowanym zestawem narzędzi uniksowych (`ls`, `cat`, `grep`, `ps`, `top`, `df`, `ping`, `chmod` itp.) oraz komendami wbudowanymi (`cd`, `clear`, `export`, `history`, `ssh`).
- **Potwierdzony tryb Wykonuj**: Krok po kroku z czytelną kartą podglądu każdej komendy, wyjaśnieniem jej działania i wyraźnym przyciskiem zatwierdzenia przed uruchomieniem w powłoce.
- **Potwierdzone zarządzanie kluczami SSH**: Wbudowany generator par kluczy RSA (2048/4096 bit) z eksportem klucza publicznego do formatu OpenSSH (`ssh-rsa ...`) oraz bezpieczny import/wklejanie istniejących kluczy prywatnych.
- **Potwierdzona estetyka**: Głęboka czerń OLED (`#000000` / `#0A0A0C`) z kontrastowym białym tekstem terminala (`#E6EDF3`), bursztynowo-złotymi akcentami (`#F59E0B` / `#D97706`), podświetleniem składni i responsywnym paskiem klawiszy funkcyjnych (`Esc`, `Tab`, `Ctrl`, `Alt`, strzałki, potoki `|`).

---

## 1. Overview & Core Concept

- **Co robi aplikacja**: Oferuje programistom, administratorom systemów i entuzjastom Linuksa natywny terminal mobilny w stylu Termux. Pozwala na pracę w lokalnym środowisku Androida oraz nawiązywanie szyfrowanych sesji SSH z zewnętrznymi serwerami (VPS, Raspberry Pi, serwery chmurowe) z uwierzytelnianiem hasłem lub kluczem prywatnym RSA.
- **Wbudowane opcje AI**:
  1. **Tryb Zapytaj (Ask)**: Dedykowane okno czatu/panel wysuwany, w którym użytkownik rozmawia z Gemini o komendach, skryptach, architekturze czy diagnostyce błędów, bez zanieczyszczania bufora terminala.
  2. **Tryb Wykonuj (Execute)**: Użytkownik podaje cel (np. *"Skonfiguruj kopię katalogu projektu i zbadaj zajętość pamięci"*). Model `gemini-4-argon` (z trybem głębokiego myślenia) generuje plan działań i proponuje kolejne polecenia, które użytkownik zatwierdza jednym dotknięciem.
  3. **Tryb Live (Pomocnik czasu rzeczywistego)**: Audytor i doradca sesji terminala (wykorzystujący `gemini-3.8-live` i `gemini-3.8-flash-tts`). Analizuje logi i ostrzeżenia w tle, wyjaśnia komunikaty błędów głosem i tekstem, z bezwzględną regułą: **brak możliwości bezpośredniego wpisywania/ingerencji w kod**.
- **Wartość użytkowa**: Bezpieczeństwo (lokalne przechowywanie kluczy w Room), wygoda mobilnej pracy terminalowej z paskiem skrótów oraz potężne wsparcie AI eliminujące konieczność ręcznego wertowania dokumentacji na małym ekranie.

---

## 2. User Experience & Visual Design

### Kluczowe Przepływy Użytkownika (User Flows)
1. **Lokalna Sesja Terminala**:
   - Uruchomienie aplikacji natychmiast otwiera prompt terminala: `u0_a123@localhost:~$ `
   - Dostępny dolny pasek szybkiego wprowadzania (`ESC`, `TAB`, `CTRL`, `ALT`, `|`, `/`, `-`, `~`, `↑`, `↓`).
   - Wprowadzanie komend z automatycznym przewijaniem bufora i historią (strzałki góra/dół).
2. **Nawiązywanie Połączenia SSH**:
   - Kliknięcie w menu / zakładki sesji -> "Menedżer SSH" lub komenda `ssh user@host`.
   - Formularz profilu: Etykieta, Host/IP, Port (domyślnie 22), Użytkownik, Metoda uwierzytelniania:
     - *Hasło* (z opcją zapamiętania w bezpiecznym magazynie).
     - *Klucz prywatny* (wybór z zapisanych kluczy lub wygenerowanie nowego klucza RSA).
   - Generowanie klucza: Użytkownik klika "Generuj nowy klucz RSA", wpisuje nazwę (np. `vps-key`), aplikacja generuje parę kluczy i udostępnia klucz publiczny w formacie OpenSSH do skopiowania jednym kliknięciem do schowka.
   - Po połączeniu: Wyświetlenie zdalnego prompta serwera w terminalu.
3. **Aktywacja Trybu "Zapytaj"**:
   - Przycisk szybkiego wyboru AI w pasku wejścia terminala: [Zapytaj] / [Wykonuj].
   - Wybór "Zapytaj" wysuwa dedykowany panel dialogowy.
   - Czat z Gemini z podglądem kontekstu (możliwość dołączenia ostatnich linii bufora terminala jednym kliknięciem).
4. **Aktywacja Trybu "Wykonuj"**:
   - Wybór "Wykonuj" otwiera kreator celu: Użytkownik wpisuje zamierzone zadanie.
   - AI analizuje kontekst (lokalny shell lub zdalny serwer SSH) i zwraca sekwencję kroków.
   - Karta interaktywna: Krok X/Y, polecenie (np. `tar -czvf backup.tar.gz ./data`), wyjaśnienie, przyciski `[Uruchom w terminalu]`, `[Edytuj]`, `[Pomiń]`.
   - Wynik wykonania wraca do AI, która weryfikuje kod wyjścia i przechodzi do kolejnego kroku.
5. **Aktywacja Pomocnika "Live Helper"**:
   - Pływający wskaźnik statusu (Amber Pulse). Użytkownik może włączyć asystenta głosowego Live (`gemini-3.8-flash-tts`).
   - Asystent komentuje status operacji (np. *"Polecenie zakończyło się kodem 127: nie znaleziono programu htop. Czy chcesz go zainstalować?"*), wyłącznie w trybie doradczym.

### Visual Identity & Theme (OLED Black & Amber)
- **Tło Główne**: Czysta czerń `#000000` i ultra-ciemny grafit `#090A0F`.
- **Kolor Terminala**: Jasnoszary/biały `#E6EDF3`, zielony prompt `#10B981`, błękit ścieżki `#38BDF8`.
- **Akcent Wiodący (Amber/Gold)**: `#F59E0B` (bursztyn) oraz `#D97706` (głęboki bursztyn).
- **Powierzchnie Kart i Dialogów**: `#12131A` z subtelnym obramowaniem `#27273A`.
- **Typografia**:
  - Kod i terminal: Monospace (`Roboto Mono` lub systemowy monospace zoptymalizowany pod znaki ANSI i kody powłoki).
  - UI i etykiety: Czysty sans-serif o wysokiej czytelności.

---

## 3. Key Product Decisions & Trade-Offs

### Decyzja 1: Architektura Shell Lokalnego
- **Wybrane podejście**: Hybrydowy silnik terminalowy wykorzystujący natywny `ProcessBuilder` do uruchamiania `/system/bin/sh` wraz z bogatą warstwą wbudowanych poleceń (`BuiltinCommands`: nawigacja `cd`, `pwd`, zarządzanie aliasami, czyszczenie `clear`, wbudowany pomocnik `help`, wywoływanie `ssh`).
- **Dlaczego**: Daje pełny dostęp do prawdziwych binarek Androida (toybox: `ls`, `cat`, `grep`, `ps`, `df`, `ping`, `top`), a jednocześnie zapewnia płynność, obsługę katalogu roboczego i odporność na ograniczenia sandboksowe Androida.

### Decyzja 2: Obsługa Klienta SSH i Kluczy Prywatnych
- **Wybrane podejście**: Wykorzystanie biblioteki `com.github.mwiede:jsch` (nowoczesny, aktywnie rozwijany fork JSch wspierający OpenSSH, RSA-SHA2-512, ED25519) w połączeniu z `java.security.KeyPairGenerator` dla generowania par kluczy RSA na urządzeniu.
- **Dlaczego**: Pozwala na stabilne połączenia wewnątrz maszyny wirtualnej Androida bez konfliktów kryptograficznych, obsługuje formaty kluczy PEM i OpenSSH oraz pozwala na bezpieczne przechowywanie profili i kluczy w bazie Room.

### Decyzja 3: Tryb "Wykonuj" jako Krok po Kroku z Autoryzacją
- **Wybrane podejście**: AI działa w trybie asystenta nadzorowanego. Zamiast niebezpiecznego, samowolnego wykonywania komend, każde polecenie jest prezentowane użytkownikowi w wyspecjalizowanej karcie "Execution Step" z przyciskiem zatwierdzenia.
- **Dlaczego**: Zapewnia stuprocentowe bezpieczeństwo użytkownika na serwerach produkcyjnych i w systemie lokalnym, eliminując ryzyko przypadkowego usunięcia plików czy zerwania połączenia.

### Decyzja 4: Gemini AI i Live Helper
- **Wybrane podejście**: Bezpośrednie wywołania REST do modeli Google Gemini z kluczem wstrzykiwanym przez `BuildConfig.GEMINI_API_KEY`:
  - `gemini-4-argon` z `thinkingLevel = "high"` dla trybu "Wykonuj" oraz trudnych zagadnień debugowania.
  - `gemini-3.8-flash` dla szybkiego czatu w trybie "Zapytaj".
  - `gemini-3.8-flash-tts` / Live Helper dla syntezy mowy i audio-komentarzy o stanie terminala.
- **Dlaczego**: Zgodność z wytycznymi środowiska, najwyższa jakość rozumowania przy analizie logów serwerowych i zerowe opóźnienia.

---

## 4. Technical Architecture & Data Strategy

### Diagram Architektury Systemu

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                             AURORA TERMINAL UI                              │
│  ┌────────────────────────────────┐  ┌───────────────────────────────────┐  │
│  │     Terminal Display Area      │  │        AI Floating Sheets         │  │
│  │   • ANSI Colors / Monospace    │  │   • [Zapytaj] Split Chat          │  │
│  │   • Output Buffer & History    │  │   • [Wykonuj] Step-by-Step Card   │  │
│  │   • Active Session Indicator   │  │   • [Live Helper] Audio & Hints   │  │
│  └────────────────────────────────┘  └───────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │       Interactive Input Bar: Prompt + Command Input + AI Selector     │  │
│  │       Keyboard Accessory Bar: [ESC] [TAB] [CTRL] [ALT] [|] [~] [↑] [↓]│  │
│  └───────────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
                    ┌──────────────────┴──────────────────┐
                    ▼                                     ▼
     ┌─────────────────────────────┐       ┌─────────────────────────────┐
     │      TerminalViewModel      │       │        AiAssistantViewModel │
     │  • Active Session State     │       │  • Mode (Zapytaj/Wykonuj)   │
     │  • Command History Index    │       │  • Execution Step State     │
     │  • Process I/O Coroutines   │       │  • High-Thinking Planner    │
     └──────────────┬──────────────┘       └──────────────┬──────────────┘
                    │                                     │
       ┌────────────┴────────────┐                        │
       ▼                         ▼                        ▼
┌──────────────┐          ┌──────────────┐         ┌──────────────┐
│ Local Shell  │          │  SSH Client  │         │  Gemini API  │
│ Engine (sh)  │          │ (mwiede jsch)│         │ (REST/Audio) │
└──────┬───────┘          └──────┬───────┘          └──────┬───────┘
       │                         │                        │
       └────────────┬────────────┘                        │
                    ▼                                     ▼
       ┌─────────────────────────┐         ┌──────────────────────────────┐
       │   Room SQLite Database  │         │ Android Speech / Media Play  │
       │ • SshProfileEntity      │         │ • gemini-3.8-flash-tts       │
       │ • SshKeyEntity (RSA)    │         │ • Text-to-speech feedback    │
       │ • CommandHistoryEntity  │         └──────────────────────────────┘
       └─────────────────────────┘
```

### Struktura Danych i Baza Room
1. **`SshProfileEntity`**:
   - `id`: Int (PrimaryKey autoGenerate)
   - `name`: String (np. "Production Web", "Home Lab")
   - `host`: String, `port`: Int (domyślnie 22)
   - `username`: String
   - `authType`: Enum (`PASSWORD`, `PRIVATE_KEY`)
   - `privateKeyId`: Int? (relacja do klucza)
   - `password`: String?
   - `lastConnected`: Long
2. **`SshKeyEntity`**:
   - `id`: Int (PrimaryKey autoGenerate)
   - `alias`: String (np. "id_rsa_aurora")
   - `keyType`: String ("RSA-2048", "RSA-4096")
   - `publicKeyOpenssh`: String (`ssh-rsa AAAAB3NzaC1yc2E... user@device`)
   - `privateKeyPem`: String (zaszyfrowany/bezpieczny format PEM/PKCS#8)
   - `createdAt`: Long
3. **`CommandHistoryEntity`**:
   - `id`: Int (PrimaryKey autoGenerate)
   - `sessionType`: String ("LOCAL", "SSH")
   - `command`: String
   - `timestamp`: Long
4. **`ExecutionStep`** (Model Pamięciowy trybu Wykonuj):
   - `stepIndex`: Int
   - `totalSteps`: Int
   - `command`: String
   - `rationale`: String
   - `expectedOutcome`: String
   - `status`: Enum (`PENDING`, `RUNNING`, `COMPLETED`, `FAILED`, `SKIPPED`)
   - `actualOutput`: String?

---

## 5. Implementation Roadmap

1. **Konfiguracja Środowiska i Zależności**:
   - Dodanie biblioteki `com.github.mwiede:jsch:0.2.20` do `libs.versions.toml` i `app/build.gradle.kts`.
   - Odkomentowanie `GEMINI_API_KEY` w `.env.example`.
   - Dodanie uprawnień `INTERNET` i `RECORD_AUDIO` do `AndroidManifest.xml`.
   - Synchronizacja `metadata.json` i `res/values/strings.xml` z nazwą "Aurora Terminal".
2. **Baza Danych Room (Magazyn Profili i Kluczy SSH)**:
   - Encje: `SshProfileEntity`, `SshKeyEntity`, `CommandHistoryEntity`.
   - DAO i Repozytorium z reaktywnymi strumieniami `Flow`.
   - Narzędzie `SshKeyManager` do generowania par kluczy RSA (2048/4096) i konwersji do standardu OpenSSH.
3. **Silnik Terminala (Lokalny Shell & Klient SSH)**:
   - Klasa `LocalShellSession`: Zarządzanie procesem `/system/bin/sh`, strumieniowanie wyjścia, obsługa katalogu roboczego (`cd`), komendy wbudowane.
   - Klasa `SshTerminalSession`: Zarządzanie sesją JSch, uwierzytelnianie kluczem RSA/hasłem, obsługa kanału interaktywnego i przesyłanie komend.
   - Parser ANSI i formatowanie wyjścia terminala.
4. **Warstwa Gemini AI**:
   - Klient Retrofit i serializatory dla `gemini-4-argon` (z `thinkingLevel = "high"` dla trybu Wykonuj), `gemini-3.8-flash` (dla trybu Zapytaj) oraz `gemini-3.8-flash-tts` dla dźwięku pomocnika Live.
   - Serwis analizy kontekstu terminala i automatycznego generowania kroków realizacji celów.
5. **Interfejs Użytkownika Jetpack Compose**:
   - Motyw OLED Black & Amber (`Color.kt`, `Theme.kt`, `Type.kt`).
   - Ekran główny `TerminalScreen`: Ekran terminala z buforem tekstu i kursorem.
   - Pasek wejściowy `TerminalInputBar` ze zintegrowanym selektorem `[Normalny]`, `[Zapytaj]`, `[Wykonuj]` oraz pływającym przełącznikiem `Live Helper`.
   - Pasek akcesoriów klawiatury (ESC, TAB, CTRL, strzałki itp.).
   - Panel dialogowy `AskAiDialog` (czat boczny/dolny).
   - Karta realizacji zadania `ExecuteGoalCard` (sterowanie krok po kroku z podglądem).
   - Dialog menedżera SSH i generatora kluczy RSA (`SshManagerDialog`, `KeyGenDialog`).
6. **Weryfikacja i Kompilacja**:
   - Uruchomienie `compile_applet`, testy działania i weryfikacja stabilności.
