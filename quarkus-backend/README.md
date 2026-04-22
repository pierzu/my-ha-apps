# Quarkus Backend

Ta appka uruchamia kontener Quarkus w Home Assistant.

## Wymagania

- obraz `ghcr.io/YOUR_GITHUB_LOGIN/my-quarkus-backend:0.0.9` musi istnieć
- `version` w `config.yaml` musi zgadzać się z tagiem obrazu

## Testy

- Web UI prowadzi na `/ha/health`
- endpoint `/ha/ping` sprawdza połączenie z Home Assistant Core API przez Supervisor proxy
