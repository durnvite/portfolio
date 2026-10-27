# Kontrakt Współpracy Wieloagentowej (Hermes Master Contract)

## 1. Zasada Jednego Źródła Prawdy (Single Source of Truth)

Aby wyeliminować halucynacje i niespójności pomiędzy różnymi instancjami modeli AI pracującymi nad systemem, wprowadzono rygorystyczny podział odpowiedzialności:

| Warstwa | Kanoniczne Źródło (SoT) | Zakres odpowiedzialności |
|---|---|---|
| **VPS Runtime** | `~/AGENTS.md` | Stan faktyczny usług systemd, cronów, kontenerów. |
| **Baza Wiedzy (Vault)** | `wiki/VPS/AGENTS.md` | Standardy formatowania notatek, rejestry decyzji architektonicznych. |
| **Projekt** | Lokalne `_INSTRUKCJE.md` / `AGENTS.md` | Wytyczne i wyjątki proceduralne dla danego repozytorium. |
| **Kod Źródłowy** | Pliki repozytorium | Jedyna prawna implementacja kodu. |
| **Decyzje Architektoniczne** | Notatki audytowe | "Dlaczego", "od kiedy" oraz "warunki rollbacku". |

---

## 2. Standard Procedury Wyjścia (Stop-Check)

Przed zakończeniem każdej iteracji agent ma obowiązek wykonać:
1. Sprawdzenie, czy zmiana architektury ma odzwierciedlenie w planie i rejestrze `CHANGELOG.md`.
2. Zapisanie komendy, wyniku, daty i procedury wycofania (rollback) dla każdej operacji na serwerze.
3. Weryfikację, czy żaden sekret ani hasło nie zostały zapisane do publicznej lub współdzielonej notatki.
4. Wygenerowanie zwięzłego podsumowania sesji w dedykowanym katalogu audytowym.
