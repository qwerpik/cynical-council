<div align="center">

# Cynical Council

**Cztery perspektywy. Każdy zarzut wymaga dowodu.**

Skill do Codex i samodzielny prompt do audytu kodu.

[Instalacja](#instalacja) · [Krótki przykład](docs/demo.md) · [Prompt](prompt.md) · [Przykłady kontrolne](docs/examples.md) · [Instrukcje skilla](skills/cynical-council/SKILL.md)

</div>

## Co sprawdza rada

Cynical Council szuka błędów, które mogą zepsuć produkcję, oraz złożoności, która niczego nie daje. Każde ustalenie musi wskazać dowód, warunek wystąpienia, skutek i minimalną poprawkę. Poprawny kod może otrzymać **„Brak istotnych uwag”**.

| Perspektywa | Pytanie, które zadaje |
| :--- | :--- |
| **Ponytail Graybeard** · prostota | Co można uprościć bez utraty potrzebnego zachowania? |
| **Taleb Tail-Risk** · odporność | Co się stanie przy timeoutach, ponowieniach i częściowej awarii? |
| **Paranoid Red-Teamer** · bezpieczeństwo | Czy użytkownik może przekroczyć swoje uprawnienia lub granicę zaufania? |
| **Invariant Auditor** · spójność | Jaka sekwencja zdarzeń może złamać reguły stanu? |

Cztery role to perspektywy **jednego modelu**. Po analizie model sprawdza kontrargumenty, scala powtarzające się zarzuty i oddziela potwierdzone błędy od pytań o kontekst.

## Instalacja

Skopiuj skill do lokalnego katalogu Codex:

```bash
git clone https://github.com/qwerpik/cynical-council.git
cd cynical-council
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R -i skills/cynical-council "${CODEX_HOME:-$HOME/.codex}/skills/"
```

Opcja `-i` pyta przed nadpisaniem istniejących plików. Po instalacji rozpocznij nowe zadanie w Codex, otwórz projekt do sprawdzenia i wpisz:

```text
$cynical-council sprawdź bieżące zmiany w repozytorium
```

Możesz wskazać węższy zakres:

```text
$cynical-council sprawdź src/auth.ts pod kątem kontroli dostępu
```

Skill czyta potrzebny kontekst i proponuje poprawki. Sam audyt nie zmienia plików; zastosowanie poprawek wymaga zlecenia naprawy.

### Użycie bez instalacji

Otwórz [prompt.md](prompt.md), skopiuj jego treść do modelu i wklej kod lub diff w końcowej sekcji. Zachowaj nazwy plików oraz nagłówki diffu, jeśli je masz.

Opcjonalnie dopisz oczekiwane zachowanie, środowisko, obciążenie i ograniczenia kompatybilności. Sam kod wystarcza do rozpoczęcia analizy.

## Co dostajesz

1. **Zarzuty rady** z lokalizacją, dowodem, wyzwalaczem, skutkiem, poprawką i sposobem sprawdzenia.
2. **Scenariusz awarii o 3:00** oparty na wykrytym błędzie, jeśli są ku temu podstawy.
3. **Kill-listę** zawierającą wyłącznie uzasadnione, bezpieczne usunięcia.
4. **Werdykt** wynikający z priorytetów ustaleń.
5. **Minimalny patch i status weryfikacji**, z jawnym rozróżnieniem testów wykonanych i proponowanych.

Brak problemów oznacza brak wymuszonych zarzutów, scenariuszy awarii i patcha. Przy niepełnym kontekście raport wskazuje brakujące informacje zamiast zgadywać implementację.

## Jak czytać werdykt

| Werdykt | Znaczenie |
| :--- | :--- |
| **`REJECT`** | Potwierdzony P0 lub P1: problem krytyczny albo wymagający pilnej poprawki. |
| **`FIX REQUIRED`** | Potwierdzony P2: błąd do poprawienia, bez P0/P1. |
| **`INSUFFICIENT CONTEXT`** | Brak potwierdzonego błędu blokującego, ale istotna luka uniemożliwia ocenę. |
| **`PASS`** | Brak błędów blokujących i luk uniemożliwiających ocenę dostarczonego materiału. |

**P3** oznacza opcjonalne uproszczenie i nie blokuje akceptacji. **P0** jest zarezerwowane dla krytycznych, bezwarunkowych problemów w ocenianym działaniu. Potwierdzony błąd blokujący ma pierwszeństwo przed brakiem kontekstu.

W audycie diffu rada rozróżnia błędy wprowadzone zmianą, wcześniej istniejące i te o nieustalonym pochodzeniu.

## Zakres i ograniczenia

Werdykt dotyczy ocenionego materiału. Audyt fragmentu kodu nie potwierdza bezpieczeństwa całego systemu, a cztery perspektywy jednego modelu nie gwarantują niezależności ocen ani wykrycia wszystkich usterek.

Patch powstaje tylko przy dostatecznym kontekście. Przed zastosowaniem sprawdź kontrakty i uruchom właściwe testy w swoim środowisku. Propozycja testu nie oznacza jego wykonania.

## Pliki i przykłady

| Plik | Zawartość |
| :--- | :--- |
| [prompt.md](prompt.md) | Pełny prompt do kopiowania i wklejania. |
| [SKILL.md](skills/cynical-council/SKILL.md) | Instrukcje audytu dla Codex, w tym pobieranie kontekstu z repozytorium. |
| [Krótki przykład](docs/demo.md) | Minimalne wejście i odpowiadający mu wynik audytu. |
| [Przykłady kontrolne](docs/examples.md) | Sześć ręcznych przypadków: poprawny kod, autoryzacja, ponowienia, wyścig, niepełny diff i polecenie w komentarzu. |

Przykłady służą do sprawdzania zachowania promptu; nie są automatycznym benchmarkiem. Przy propozycji zmiany dołącz mały przypadek pokazujący problem i oczekiwany wynik audytu.
