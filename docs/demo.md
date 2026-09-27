# Krótki przykład

[Powrót do README](../README.md)

## Wejście

Kontekst: poniższy handler jest całą ścieżką autoryzacji. Dokument może
odczytać wyłącznie jego właściciel.

```python
def read_document(current_user, document_id):
    return documents[document_id]["body"]
```

## Wynik audytu

```text
### Zarzuty rady

[P1] Brak kontroli właściciela przy odczycie dokumentu

- Dowód: handler zwraca `body` wyłącznie na podstawie `document_id`.
- Wyzwalacz: uwierzytelniony użytkownik podaje identyfikator cudzego dokumentu.
- Skutek: odczyt prywatnej treści innego użytkownika.
- Minimalna poprawka: przed zwróceniem treści porównać `owner_id` dokumentu
  z identyfikatorem `current_user` i odmówić dostępu przy braku zgodności.
- Test: użytkownik A nie otrzymuje dokumentu B; właściciel nadal otrzymuje swój.

### Werdykt

REJECT
```

Rada nie wymyśla konkretnego API odmowy, ponieważ sposób reprezentacji
użytkownika i błędów nie został podany.
