# Budget Backend

Backend budżetowy pod Home Assistant Green.

Aplikacja:
- zapisuje dane w SQLite w `/data/budget.db`
- obsługuje ręczne dodawanie transakcji
- obsługuje pendingi do zatwierdzania i odrzucania
- importuje maile z IMAP
- parsuje wybrane maile bankowe PKO
- dopasowuje reguły autokategoryzacji
- publikuje dashboard i pendingi do MQTT dla Home Assistant

## Konfiguracja

Najważniejsze ustawienia:
- `budget.mqtt.enabled`
- `budget.mqtt.host`
- `budget.mqtt.port`
- `budget.mqtt.username`
- `budget.mqtt.password`
- `budget.mail-import.enabled`
- `budget.mail-import.schedule`
- `budget.mail-import.imap.host`
- `budget.mail-import.imap.port`
- `budget.mail-import.imap.username`
- `budget.mail-import.imap.folder`
- env `BUDGET_MAIL_IMPORT_IMAP_PASSWORD`

Hasło IMAP jest pobierane z env vara, nie z pliku properties.

## MQTT i Home Assistant

Aplikacja publikuje retained state do MQTT:
- dashboard bieżącego okresu
- listę pendingów
- discovery dla encji Home Assistant

Po starcie publikowany jest też świeży stan, żeby nadpisać stare retained payloady po reinstalacji albo wyczyszczeniu bazy.

## Reguły autokategoryzacji

Po wyliczeniu docelowego `description` z parsera maili aplikacja może dopasować regułę importu i:
- zasugerować kategorię
- albo automatycznie zatwierdzić transakcję

Reguła zawiera:
- `pattern`
- `category_code`
- `match_mode`
- `action`
- `priority`
- `active`

Obsługiwane `match_mode`:
- `CONTAINS`
- `EXACT`
- `STARTS_WITH`
- `ENDS_WITH`

Obsługiwane `action`:
- `SUGGEST`
- `AUTO_CONFIRM`

Dopasowanie ignoruje wielkość liter.

## Resources

- `GET /ha/health`
  Prosty healthcheck backendu.

- `GET /ha/ping`
  Test połączenia z Home Assistant Supervisor API.

- `GET /dashboard/current-period`
  Zwraca dashboard bieżącego okresu budżetowego.

- `GET /transactions?limit=20`
  Zwraca ostatnie transakcje.

- `POST /transactions`
  Dodaje nową transakcję i odświeża dashboard MQTT dla bieżącego okresu.

- `PUT /transactions/{id}`
  Aktualizuje istniejącą transakcję i odświeża dashboard MQTT dla bieżącego okresu.

- `DELETE /transactions/{id}`
  Usuwa transakcję i odświeża dashboard MQTT dla bieżącego okresu.

- `GET /pending-transactions?status=PENDING&limit=20`
  Zwraca pendingi o wskazanym statusie.

- `POST /pending-transactions/import`
  Tworzy pojedynczy pending z importu.

- `PUT /pending-transactions/{id}`
  Edytuje pending.

- `POST /pending-transactions/{id}/confirm`
  Zatwierdza pending i tworzy finalną transakcję.

- `POST /pending-transactions/{id}/reject`
  Odrzuca pending.

- `POST /mail-import/run`
  Ręcznie uruchamia jednorazowy import maili.

- `GET /import-category-rules`
  Lista reguł autokategoryzacji.

- `POST /import-category-rules`
  Dodaje nową regułę.

- `PUT /import-category-rules/{id}`
  Aktualizuje istniejącą regułę.

- `DELETE /import-category-rules/{id}`
  Usuwa regułę.
