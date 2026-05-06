# Jemima Learning Hub

A lifelong learning hub built with MkDocs and Material for MkDocs.

## Local Setup

```bash
python -m venv .venv
```

Activate the virtual environment.

Windows PowerShell:

```bash
.venv\Scripts\Activate.ps1
```

Mac/Linux:

```bash
source .venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run locally:

```bash
mkdocs serve
```

Open:

```text
http://127.0.0.1:8000/
```

## GitHub Setup

```bash
git init
git add .
git commit -m "Initial Jemima Learning Hub documentation site"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/jemima-learning-hub.git
git push -u origin main
```

## Read the Docs Setup

1. Sign in to Read the Docs with GitHub.
2. Import the `jemima-learning-hub` repository.
3. Build the project.
4. Read the Docs will use `.readthedocs.yaml` and `mkdocs.yml`.
