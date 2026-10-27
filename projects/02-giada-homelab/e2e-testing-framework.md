# Framework Testowy E2E dla Homelab Docker Compose

## 1. Koncepcja i Cel
Aby zapobiec wdrażaniu błędnych konfiguracji na fizyczny, słaby serwer Giada F105, powstał dedykowany zestaw testów End-to-End (`tests/test_stack_e2e.py`) uruchamiany lokalnie na maszynie deweloperskiej w terminalu IDE (VS Code / Cursor).

---

## 2. Architektura Testów

Zestaw dzieli się na dwie niezależne warstwy:

### A. Testy Statyczne (`@pytest.mark.static` — czas wykonania: ~2s)
Weryfikuje 36 asercji reguł jakościowych bez uruchamiania ciężkich kontenerów:
1. `test_docker_compose_config_valid`: parsowanie składni plików i interpolacja zmiennych z `.env`.
2. `test_no_latest_tag_pinning`: rygorystyczny zakaz używania tagów `:latest` w obrazach.
3. `test_restart_policy_unless_stopped`: wymuszenie restartu `unless-stopped`.
4. `test_security_opt_no_new_privileges`: wymuszenie izolacji jądra Linux.
5. `test_static_admin_ports_bound_to_localhost_only`: detekcja braku prefiksu `127.0.0.1:` na portach panelu.
6. `test_resource_protection_and_kiss`: weryfikacja czy Paperless ma wyłączony OCR i pojedynczego workera, a Redis limit 64 MB.

### B. Testy Dynamiczne E2E (`@pytest.mark.e2e`)
Zarządza pełnym cyklem życia kontenera w wydzielonym projekcie Docker Compose:
- **Pre-flight check:** Weryfikacja czy port hosta nie jest zajęty na PC dewelopera (`is_port_in_use`).
- **Rozruch:** `docker compose -f <plik>.yml -p giada_test_<service> up -d`.
- **Runtime Port Inspection:** Inspekcja socketu przez `docker inspect` (upewnienie się, że daemon Dockera nie wystawił portu na `0.0.0.0`).
- **HTTP Readiness:** Odpytanie endpointu HTTP z obsługą samopodpisanych certyfikatów SSL (Portainer 9443).
- **SQLite Database Check:** Inspekcja logów i wolumenów pod kątem czystego startu SQLite.
- **Teardown & Cleanup:** Gwarantowane polecenie `down -v --remove-orphans` usuwające kontenery i wolumeny testowe.

---

## 3. Komendy Uruchomieniowe

W katalogu `G:\giada-mieszkanie`:
```powershell
# 1. Błyskawiczna weryfikacja 36 asercji przed commitem
.\run_tests.ps1 -Mode static

# 2. Test dynamiczny wybranego stacku (np. budget)
python -m pytest -m e2e -k "budget" -s

# 3. Pełny zestaw testów
.\run_tests.ps1 -Mode all
```
