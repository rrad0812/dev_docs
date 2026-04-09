
# Transport jam.py release to PyPi

[Nazad](./README.md)

## 1. Priprema strukture projekta

U korenu repozitorijuma moraš imati barem:

```sh
jam-py-radosav/
├── jam/
│   ├── __init__.py
│   ├── wsgi.py
│   └── ...
├── setup.py
├── pyproject.toml
├── README.md
├── LICENSE
└── MANIFEST.in   (opciono)
```

Ako koristiš `setuptools`, **setup.py** može izgledati ovako:

```python
from setuptools import setup, find_packages

setup(
    name="jam-py-radosav",
    version="1.0.0",
    author="Radosav",
    author_email="tvoj.email@example.com",
    description="Fork and improved version of Jam.py application builder",
    long_description=open("README.md").read(),
    long_description_content_type="text/markdown",
    url="https://github.com/tvoje-ime/jam-py-radosav",
    packages=find_packages(),
    include_package_data=True,
    install_requires=[
        "flask>=2.0",
        "sqlalchemy>=1.4",
        # dodaj šta Jam.py koristi
    ],
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires=">=3.8",
)
```

Ako već koristiš `pyproject.toml` (moderniji pristup), možeš i ovako:

```py
[build-system]
requires = ["setuptools", "wheel"]
build-backend = "setuptools.build_meta"
```

## 2. Napravi lokalni build

Instaliraj alatke (ako već nisu):

```bash
pip install setuptools wheel twine
```

Zatim napravi pakete:

```bash
python setup.py sdist bdist_wheel
```

Ovo će u `dist/` folderu napraviti npr.:

```sh
dist/jam-py-radosav-1.0.0.tar.gz
dist/jam_py_radosav-1.0.0-py3-none-any.whl
```

## 3. Kreiraj nalog na PyPI

Idi na [https://pypi.org/account/register/](https://pypi.org/account/register/)
i napravi nalog ako nemaš.

Zatim (po izboru) napravi i **test nalog** na [https://test.pypi.org/](https://test.pypi.org/)
da probaš upload bez rizika.

## 4. Objavi na PyPI (ili TestPyPI)

### Test upload

```bash
twine upload --repository testpypi dist/*
```

Ako je sve ok, pravi upload:

```bash
twine upload dist/*
```

Zatražiće ti PyPI korisničko ime i lozinku (ili token ako koristiš API key).

## 5. Provera instalacije

Kad se objavi, proveri da li se može instalirati:

```bash
pip install jam-py-radosav
```

Ako si testirao na testpypi, koristi:

```bash
pip install -i https://test.pypi.org/simple jam-py-radosav
```

## Saveti iz prakse

- Pre objave, proveri paket:

  ```bash
  twine check dist/*
  ```

  da vidiš da li ima problema s metapodacima.

- Uvek koristi **semver verzije** (1.0.0, 1.0.1...)
  jer PyPI ne dozvoljava ponovni upload istog broja verzije.

- Ako praviš veći fork, u README odmah objasni da je to nezavisna grana
  (npr. “This is a maintained fork of Jam.py for Python 3.12+ support.”)

Odlično, brate — idemo da složimo pravi **`setup.py`** za tvoj Jam.py fork, spreman za upload na PyPI.
Ovo je čist, praktičan template koji pokriva sve što ti treba (uključujući entrypoint za pokretanje servera).

## `setup.py` za tvoj Jam.py fork

```python
from setuptools import setup, find_packages

setup(
    name="jam-py-radosav",  # jedinstveno ime na PyPI
    version="1.0.0",  # sledeću verziju uvek povećaj (PyPI ne dozvoljava overwrite)
    author="Radosav",
    author_email="tvoj.email@example.com",
    description="Independent fork of the Jam.py low-code framework.",
    long_description=open("README.md", encoding="utf-8").read(),
    long_description_content_type="text/markdown",
    url="https://github.com/tvoje-ime/jam-py-radosav",
    project_urls={
        "Documentation": "https://jampy-docs-v7.readthedocs.io/",
        "Source": "https://github.com/tvoje-ime/jam-py-radosav",
        "Bug Tracker": "https://github.com/tvoje-ime/jam-py-radosav/issues",
    },
    license="MIT",
    packages=find_packages(exclude=["tests*", "examples*"]),
    include_package_data=True,
    install_requires=[
        "flask>=2.0",
        "sqlalchemy>=1.4",
        "wtforms>=3.0",
        "pytz",
        "werkzeug>=2.0",
        "jinja2>=3.0",
    ],
    entry_points={
        "console_scripts": [
            # korisnici mogu pokrenuti kao `jam-run` iz terminala
            "jam-run=jam.wsgi_server:run",
        ],
    },
    classifiers=[
        "Programming Language :: Python :: 3",
        "Framework :: Flask",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
        "Development Status :: 5 - Production/Stable",
        "Intended Audience :: Developers",
        "Topic :: Software Development :: Build Tools",
    ],
    python_requires=">=3.8",
)
```

---

## Ostale datoteke koje idu uz `setup.py`

### `pyproject.toml`

(obavezno za moderne build alate)

```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"
```

### `MANIFEST.in`

(obavezno ako imaš statičke fajlove, HTML, JS itd.)

```text
recursive-include jam/static *
recursive-include jam/templates *
recursive-include jam/translations *
include README.md LICENSE
```

## Testiraj build

Iz root-a projekta:

```sh
python -m build
```

Ako nemaš `build` modul:

```sh
pip install build
```

Rezultat:

```sh
dist/
├── jam_py_radosav-1.0.0-py3-none-any.whl
└── jam-py-radosav-1.0.0.tar.gz
```

Zatim:

```sh
twine check dist/*
```

## Upload na PyPI

Test:

```sh
twine upload --repository testpypi dist/*
```

Pravi upload:

```sh
twine upload dist/*
```

## `README.md`

````markdown
### Jam.py Radosav — Independent Fork of Jam.py Framework

> Lightweight, low-code, web application builder based on Flask and SQLAlchemy.  
> This is an independent fork of the original [Jam.py](https://github.com/jam-py/jam-py),  
> maintained and adapted for modern Python versions (3.10–3.12) and practical development workflows.

### Features

- Declarative low-code app builder using Python and Flask  
- Built-in model layer, forms, CRUD and event hooks  
- Works with any SQLAlchemy-supported database  
- Full logging and debug support (integrated via Python `logging`)  
- Ready for VS Code debugging and remote attach via `debugpy`  
- Modular and extensible structure — perfect for rapid internal tools  

### Installation

From [PyPI](https://pypi.org/project/jam-py-radosav/):

```sh
pip install jam-py-radosav
````

or for local development:

```sh
git clone https://github.com/tvoje-ime/jam-py-radosav.git
cd jam-py-radosav
pip install -e .
```

### Quick Start

Create and run your Jam.py app:

```bash
jam-run
```

This command runs the built-in WSGI server.
You can then open your browser and go to:

```sh
http://127.0.0.1:8080/
```

### Project Structure

```sh
jam-py-radosav/
├── jam/
│   ├── __init__.py
│   ├── wsgi.py
│   ├── wsgi_server.py
│   ├── static/
│   └── templates/
├── setup.py
├── pyproject.toml
├── README.md
└── LICENSE
```

### Debugging and Development

You can enable remote debugging by setting an environment variable:

```sh
export JAM_REMOTE_DEBUG=1
export JAM_DEBUG_WAIT=1
python run_jam.py
```

Then attach from VS Code using the “Attach to JamPy remote” configuration.
All logs are written to `logs/jam_server.log` by default.

### Why This Fork

This fork was created to:

- Simplify server startup and logging
- Support modern Python versions and IDE debugging
- Provide a stable base for lightweight low-code projects without Docker overhead

## License

MIT License © Radosav

### Links

- Original project: [Jam.py on GitHub](https://github.com/jam-py/jam-py)
- Documentation: [Jam.py v7 Docs](https://jampy-docs-v7.readthedocs.io/en/latest/)
- This fork: [jam-py-radosav on GitHub](https://github.com/tvoje-ime/jam-py-radosav)

**Saveti:**

- Ako imaš logo ili sliku, samo stavi na vrh:

  ```markdown
  ![Jam.py Logo](https://yourdomain.com/logo.png)
  ```

- Ako planiraš više verzija, u README možeš dodati i changelog sekciju.

Savršeno, brate  

Evo ti **MIT licenca** potpuno spremna za PyPI, GitHub i sve ostalo — s tvojim imenom i godinom.

Samo kopiraj u fajl **`LICENSE`** (bez ekstenzije, velikim slovima, tako PyPI i GitHub automatski prepoznaju tip licence).

## LICENSE

```sh
MIT License

Copyright (c) 2025 Radosav

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

**Saveti:**

- GitHub će automatski prepoznati “MIT License” i prikazati ga u zaglavlju repozitorijuma.
- PyPI će isto prikazati tip licence u desnoj info koloni.
- Ako u `setup.py` već imaš `license="MIT"` (kao što smo stavili), sve će se lepo upariti.

Sada imaš:

- `setup.py`
- `pyproject.toml`
- `MANIFEST.in`
- `README.md`
- `LICENSE`

To je **potpuno spreman PyPI paket** — samo

```sh
python -m build
twine upload
```

## **Developer Recipe — How to Build and Publish jam-py-radosav on PyPI**

### 1. Priprema okruženja

Uvek koristi čist virtualni environment:

```bash
python -m venv venv
source venv/bin/activate  # (Linux/macOS)
# .\venv\Scripts\activate  # (Windows)
```

Zatim instaliraj potrebne alatke:

```bash
pip install --upgrade pip setuptools wheel twine build
```

### 2️. Proveri strukturu projekta

U root direktorijumu moraš imati:

```sh
jam-py-radosav/
├── jam/
│   ├── __init__.py
│   ├── wsgi.py
│   └── ...
├── setup.py
├── pyproject.toml
├── MANIFEST.in
├── README.md
└── LICENSE
```

### 3️. Napravi build pakete

```bash
python -m build
```

Rezultat će biti u folderu `dist/`:

```sh
dist/
├── jam_py_radosav-1.0.0-py3-none-any.whl
└── jam-py-radosav-1.0.0.tar.gz
```

### 4️. Proveri paket pre slanja

```bash
twine check dist/*
```

Ako vidiš:

```sh
Checking dist/jam_py_radosav-1.0.0-py3-none-any.whl: PASSED
Checking dist/jam-py-radosav-1.0.0.tar.gz: PASSED
```

sve je spremno.

### 5️. (Opcionalno) Testiraj upload na TestPyPI

Pre nego što objaviš javno:

```bash
twine upload --repository testpypi dist/*
```

Zatim testiraj instalaciju:

```bash
pip install -i https://test.pypi.org/simple jam-py-radosav
```

### 6️. Objavi na zvanični PyPI

Kada si siguran da je sve ok:

```bash
twine upload dist/*
```

Prijavi se svojim **PyPI korisničkim imenom i lozinkom** ili koristi **API token** (preporučeno).

Token možeš dodati u `.pypirc` fajl:

```ini
[pypi]
  username = __token__
  password = pypi-AgENdGVzdC5weXBpLm9yZwIk...
```

### 7️. Proveri svoj paket online

Idi na: [https://pypi.org/project/jam-py-radosav/](https://pypi.org/project/jam-py-radosav/)

I videćeš svoj README, verziju i sve metapodatke automatski prikazane.

### 8️. Sledeće verzije (update)

Kad napraviš izmene:

1. U `setup.py` promeni verziju (npr. `1.0.1`)
2. Očisti stare buildove:

   ```bash
   rm -rf dist build *.egg-info
   ```

3. Ponovi korake 3–6.

### Brzi rezime

| Korak        | Komanda                                     |
| ------------ | ------------------------------------------- |
| Build        | `python -m build`                           |
| Provera      | `twine check dist/*`                        |
| Test upload  | `twine upload --repository testpypi dist/*` |
| Pravi upload | `twine upload dist/*`                       |
| Instalacija  | `pip install jam-py-radosav`                |

**Tip iz prakse:**
Ako često objavljuješ, napravi skript `publish.sh` s ovim komandama — samo pokreneš i gotovo.

## `publish.sh`

```bash
#!/usr/bin/env bash
# -----------------------------------------------
# Jam.py Radosav – Build & Publish Script for PyPI
# -----------------------------------------------

set -e  # prekini ako bilo šta pukne
VERSION=$(grep "version=" setup.py | sed -E "s/.*version=['\"]([^'\"]+)['\"].*/\1/")

echo "==============================================="
echo "   🚀 Publishing jam-py-radosav v$VERSION"
echo "==============================================="

# 1️⃣ Očisti stare buildove
echo "🧹 Cleaning old builds..."
rm -rf build dist *.egg-info

# 2️⃣ Proveri da li je venv aktivan
if [[ -z "$VIRTUAL_ENV" ]]; then
    echo "⚠️  Virtual environment not active!"
    echo "   Run: source venv/bin/activate"
    exit 1
fi

# 3️⃣ Napravi novi build
echo "📦 Building package..."
python -m build

# 4️⃣ Proveri paket
echo "🔍 Checking package..."
twine check dist/*

# 5️⃣ Pitaj korisnika gde da šalje
echo ""
read -p "Upload to TestPyPI (t) or real PyPI (p)? [t/p]: " TARGET

if [[ "$TARGET" == "t" ]]; then
    echo "🧪 Uploading to TestPyPI..."
    twine upload --repository testpypi dist/*
elif [[ "$TARGET" == "p" ]]; then
    echo "🚀 Uploading to PyPI..."
    twine upload dist/*
else
    echo "❌ Invalid choice. Exiting."
    exit 1
fi

echo ""
echo "✅ Done! Version $VERSION published successfully."
echo "==============================================="
```

## Kako ga koristiš

1. Stavi ga u root folder svog projekta (`jam-py-radosav/publish.sh`)

2. Daj mu izvršna prava:

   ```sh
   chmod +x publish.sh
   ```

3. Aktiviraj svoj virtualni environment:

   ```sh
   source venv/bin/activate
   ```

4. Pokreni:

   ```sh
   ./publish.sh
   ```

5. Kad te pita:

   ```sh
   Upload to TestPyPI (t) or real PyPI (p)? [t/p]:
   ```

  izaberi šta hoćeš, i gotovo.

**Dodatno:**

- Ako koristiš API token (što je preporučeno), možeš ga ubaciti u `~/.pypirc` fajl pa te neće svaki put pitati za lozinku.
- Ako voliš “tiho” objavljivanje bez pitanja, možeš hardkodovati:

  ```bash
  twine upload dist/*
  ```

  umesto onog `read` dela.

## `publish_dev.sh`**

koja sama:

- pročita trenutnu verziju iz `setup.py`,
- poveća “patch” broj (npr. 1.0.0 → 1.0.1),
- upiše ga nazad u `setup.py`,
- napravi build, proveri i pošalje na PyPI ili TestPyPI.

Radi bez greške i bez da ti moraš ručno dirati verzije svaki put.

```bash
#!/usr/bin/env bash
# ----------------------------------------------------
# Jam.py Radosav – Auto version bump + build + publish
# ----------------------------------------------------

set -e

SETUP_FILE="setup.py"

# 1️. Uhvati trenutnu verziju
CURRENT_VERSION=$(grep "version=" "$SETUP_FILE" | sed -E "s/.*version=['\"]([^'\"]+)['\"].*/\1/")
IFS='.' read -r MAJ MIN PATCH <<< "$CURRENT_VERSION"

# 2️. Povećaj PATCH broj
NEW_VERSION="$MAJ.$MIN.$((PATCH + 1))"

echo "==============================================="
echo "📦 Current version: $CURRENT_VERSION"
echo "🆙 New version:     $NEW_VERSION"
echo "==============================================="

# 3️. Zameni verziju u setup.py
sed -i "s/version=['\"]$CURRENT_VERSION['\"]/version=\"$NEW_VERSION\"/" "$SETUP_FILE"

# 4️. Očisti stari build
echo "🧹 Cleaning old builds..."
rm -rf build dist *.egg-info

# 5️. Proveri venv
if [[ -z "$VIRTUAL_ENV" ]]; then
    echo "⚠️  Virtual environment not active!"
    echo "   Run: source venv/bin/activate"
    exit 1
fi

# 6️. Napravi novi build
echo "⚙️  Building version $NEW_VERSION..."
python -m build

# 7️. Proveri paket
echo "🔍 Checking package..."
twine check dist/*

# 8️. Pitanje: PyPI ili TestPyPI
echo ""
read -p "Upload to TestPyPI (t) or real PyPI (p)? [t/p]: " TARGET

if [[ "$TARGET" == "t" ]]; then
    echo "🧪 Uploading to TestPyPI..."
    twine upload --repository testpypi dist/*
elif [[ "$TARGET" == "p" ]]; then
    echo "🚀 Uploading to PyPI..."
    twine upload dist/*
else
    echo "❌ Invalid choice. Exiting."
    exit 1
fi

echo ""
echo "✅ Done! jam-py-radosav $NEW_VERSION published successfully."
echo "==============================================="
```

---

## Kako koristiš

1. Sačuvaj kao `publish_dev.sh` u root projekta

2. Daj mu prava za izvršavanje:

   ```sh
   chmod +x publish_dev.sh
   ```

3. Aktiviraj virtualno okruženje:

   ```sh
   source venv/bin/activate
   ```

4. Pokreni:

   ```sh
   ./publish_dev.sh
   ```

Skript će:

1. Pročitati trenutnu verziju iz `setup.py`
2. Automatski je povećati (1.0.0 → 1.0.1)
3. Napraviti novi build
4. Proveriti paket (`twine check`)
5. Pitati te gde da ga pošalje
6. I po završetku ispisati novi broj verzije.

**Napomena:**
Ako hoćeš da poveća *minor* verziju (npr. `1.1.0`), samo ručno promeni red u `setup.py` jednom —
posle toga skripta opet sama radi patch inkrement.

E tako se radi, brate moj 😎 — idemo full DevOps style, ali po naški: *jedan klik, sve završi pos’o*.
Evo ti **`publish_dev.sh`** u finalnoj, “deluxe” verziji —
sad posle objave automatski uradi i **Git commit + Git push** nove verzije na tvoj GitHub repo.

---

## ✅ `publish_dev.sh` (finalna verzija – auto commit & push)

```sh
#!/usr/bin/env bash
# ----------------------------------------------------
# Jam.py Radosav – Auto version bump + build + publish + git push
# ----------------------------------------------------

set -e

SETUP_FILE="setup.py"

# 1️. Uhvati trenutnu verziju
CURRENT_VERSION=$(grep "version=" "$SETUP_FILE" | sed -E "s/.*version=['\"]([^'\"]+)['\"].*/\1/")
IFS='.' read -r MAJ MIN PATCH <<< "$CURRENT_VERSION"

# 2️. Povećaj PATCH broj
NEW_VERSION="$MAJ.$MIN.$((PATCH + 1))"

echo "==============================================="
echo "📦 Current version: $CURRENT_VERSION"
echo "🆙 New version:     $NEW_VERSION"
echo "==============================================="

# 3️. Zameni verziju u setup.py
sed -i "s/version=['\"]$CURRENT_VERSION['\"]/version=\"$NEW_VERSION\"/" "$SETUP_FILE"

# 4️. Očisti stari build
echo "🧹 Cleaning old builds..."
rm -rf build dist *.egg-info

# 5️. Proveri da li je venv aktivan
if [[ -z "$VIRTUAL_ENV" ]]; then
    echo "⚠️  Virtual environment not active!"
    echo "   Run: source venv/bin/activate"
    exit 1
fi

# 6️. Napravi novi build
echo "⚙️  Building version $NEW_VERSION..."
python -m build

# 7️. Proveri paket
echo "🔍 Checking package..."
twine check dist/*

# 8️. Pitaj korisnika gde da pošalje
echo ""
read -p "Upload to TestPyPI (t) or real PyPI (p)? [t/p]: " TARGET

if [[ "$TARGET" == "t" ]]; then
    echo "🧪 Uploading to TestPyPI..."
    twine upload --repository testpypi dist/*
elif [[ "$TARGET" == "p" ]]; then
    echo "🚀 Uploading to PyPI..."
    twine upload dist/*
else
    echo "❌ Invalid choice. Exiting."
    exit 1
fi

# 9️. Git commit + push
echo ""
echo "📘 Committing new version to Git..."
git add "$SETUP_FILE"
git commit -m "Release v$NEW_VERSION"
git tag -a "v$NEW_VERSION" -m "Version $NEW_VERSION"
git push origin main --tags

echo ""
echo "✅ Done!"
echo "✨ jam-py-radosav v$NEW_VERSION published and pushed to GitHub ✨"
echo "==============================================="
```

## Kako koristiš to

1. Stavi fajl u root projekta:
   `jam-py-radosav/publish_dev.sh`

2. Daj mu izvršna prava:

   ```sh
   chmod +x publish_dev.sh
   ```

3. Aktiviraj virtualno okruženje:

   ```sh
   source venv/bin/activate
   ```

4. Pokreni:

   ```sh
   ./publish_dev.sh
   ```

## Šta tačno radi (redom)

1. Nađe trenutnu verziju u `setup.py`
2. Poveća patch broj (npr. `1.0.5 → 1.0.6`)
3. Zameni verziju u fajlu
4. Očisti stari build (`dist/`, `build/`, `*.egg-info`)
5. Napravi novi build i proveri ga
6. Pošalje ga na **TestPyPI** ili pravi **PyPI**
7. Napravi **git commit**, doda **git tag** i **push**-uje na GitHub

**Saveti:**

- Ako koristiš granu drugačijeg imena (npr. `master` umesto `main`), samo promeni:

  ```bash
  git push origin main --tags
  ```

  u

  ```bash
  git push origin master --tags
  ```

- Možeš dodati i automatski `git push` u novi branch, ako želiš “release/x.y.z” workflow.

- Ako koristiš **SSH umesto HTTPS** za GitHub, skripta radi bez lozinke (ako imaš SSH ključ).

[Nazad](./README.md)
