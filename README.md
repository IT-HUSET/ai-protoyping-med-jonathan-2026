# Kom igång med Claude Code för prototyping and beyond

## Förutsättningar

- Ett GitHub-konto (gratis fungerar – du får 120 timmar Codespaces/månad)
- En webbläsare (inget behöver installeras lokalt på din dator)

## Steg 1 – Repo för template-projekt

Gå repot på GitHub: [ai-protoyping-med-jonathan-2026](https://github.com/IT-HUSET/ai-protoyping-med-jonathan-2026).

## Steg 2 – Forka repot

1. Klicka på **Fork**-knappen uppe till höger på reposidan.
2. Under *Owner* – välj ditt eget GitHub-konto.
3. Du kan ändra reponamnet om du vill, annars behåll originalet.
4. Lämna *Copy the main branch only* ikryssad (standard).
5. Klicka **Create fork**.

GitHub skapar nu en kopia av repot under ditt konto: `https://github.com/ditt-användarnamn/repo-namn`.

## Steg 3 – Starta en Codespace på din fork

1. Gå till **din fork** (inte originalrepot).
2. Klicka på den gröna **Code**-knappen.
3. Välj fliken **Codespaces**.
4. Klicka **Create codespace on main** (eller den branch du vill arbeta i).

GitHub öppnar nu en VS Code-miljö direkt i webbläsaren med all kod redo att redigera.

## Steg 4 – Verifiera att allt fungerar

Terminalen i Codespace är redan öppen längst ner. Kör ett snabbt test:

```bash
# Kolla att du är i rätt repo
git remote -v

# Förväntad output:
# origin  https://github.com/ditt-användarnamn/repo-namn.git (fetch)
# origin  https://github.com/ditt-användarnamn/repo-namn.git (push)
```

## Steg 5 – Installera Claude Code i Codespace

Codespaces kör en Linux-container, så du kan installera Claude Code direkt i terminalen.

Native installer

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Starta om terminalen eller kör:

```bash
source ~/.bashrc
```

**Logga in och börja använda:**

```bash
claude
```

Första gången du kör `claude` loggar du in via OAuth med ditt Anthropic-konto (eller Claude Max/Pro-prenumeration). Därefter är Claude Code redo att använda direkt i din Codespace.


