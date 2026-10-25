---
id: ADR-0003
title: "Cross-Repo-Traceability über repo@sha-Referenzen"
status: Draft
version: 0.1.0
date: 2026-10-25
---

# Context

Die Verteilung in vier GitHub-Repos (Main: esp32-mesh-framework, Gateway, Nodes,
Node-RED) mit parallelen Arbeitsverzeichnissen erfordert, dass TASK- und
IMP-Aussagen über mehrere Git-Historien hinweg eindeutig zur Code-Historie
zurückverfolgbar bleiben. Ein einzelnes Repo, Submodule oder ein gemeinsamer
Monorepo-Zweig stehen nicht mehr zur Verfügung.

# Decision

**Cross-Repo-Referenzen geschehen ausschließlich über die Notation `repo@sha`.**

1. **Normatives bleibt ausschließlich im Main-Repo** (`docs/requirements/`,
   `docs/functional_specs/`, `docs/adr/`); TASK- und
   IMP-Dokumente entstehen im Main und verweisen auf Code.
2. **Implementierungs- und Toolchain-Doku lebt im jeweiligen Code-Repo**
   (README, Build-Anmerkungen); sie unterliegt derselben Revision.
3. **Referenznotation `repo@sha`:** Ein TASK verweist seinen Integrations-Hinweis
   auf `repo@sha`; ein IMP-Eintrag listet `repo@sha` je betroffenem Repo.
   `repo` ist der GitHub-Name, `sha` der volle oder gekürzte Commit-Sha.
4. **Traceability-Ende:** Die bestehenden REQ → FSPEC → TASK → evidence-Kanten
   ([`../process/document-lifecycle.md`](../process/document-lifecycle.md) §11)
   bleiben unverändert und werden nicht durch diese Notation ersetzt. §11
   beschreibt das *Wie* der Kanten, diese Entscheidung nur das *Wo* der
   Code-Ebene.
5. **Keine Submodule** zwischen Main und Code-Repos, damit Worker-Worktrees pro
   Repo erhalten bleiben ([`../process/multi-agent-orchestration.md`](../process/multi-agent-orchestration.md) §3).

# Alternatives

| Option | Ergebnis | Begründung |
|---|---|---|
| **repo@sha (gewählt)** | ✅ | Explizit, git-sicher, keine Tool-Bindung. |
| GitHub-Links allein | ❌ | Ohne SHA Kontext-Drift; nicht als Beweis eines definierten Standes. |
| Monorepo | ❌ | Verliert saubere Toolchain-Grenzen und Worker-Isolation pro Domain. |
| Submodule im Main | ❌ | Arbeitet gegen die geforderte Worktree-Isolation pro Repo. |

# Consequences

**Positiv:**

- Ein TASK ist ohne Ratschlag bis auf den Code zurückverfolgbar: `git -C ../<repo>/ log <sha>`.
- Main bleibt die einzige Quelle für Normatives; keine doppelte Ablage.
- Neue Repos (Lib) sind ohne Regeländerung nach derselben Notation nachführbar.

**Zu managen:**

- `repo@sha` lebt nicht als Kante im Frontmatter eines einzigen Dokuments, sondern im
  TASK-Textplan; das Regenerationsschema muss die Repo-Zuordnung explizit anzeigen.
- Ein Force-Push auf Code-Repos kann frühere Sha-Aussagen obsolet machen — deshalb nur
  reguläre Branch-/Merge-Integration, kein Force-Push (wie AGENTS/Prozess).
- Der Coordinator muss bei der Ready-to-Start- und Abschlussprüfung die
  `repo@sha`-Existenz je betroffenen Repo verifizieren (fehlend = Trace-Lücke, nicht
  stillschweigend melden).

# Verification / Open questions

- [ ] TASK-Plan-Vorlage um `repo`-Feld erweitern.
- [ ] Nach erstem echten Code-Commit eines TASK die Kanten-Erreichbarkeit an einem Beispiel nachprüfen.

# Human approval

Approver: pending  
Approved version/date: pending
