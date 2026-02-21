# Requirements Document: 要件定義書Word形式変換スキル

## Metadata

| Field | Value |
|-------|-------|
| Document ID | REQ-DOC-20260221-001-requirements-docx |
| Version | 0.1.0 |
| Status | Draft |
| Author | — |
| Created | 2026-02-21 |
| Last Updated | 2026-02-21 |

## Ticket References

| Source | ID | Title |
|--------|-----|-------|
| GitHub Issue | #3 | 要件定義書のWord形式(.docx)変換スキルの追加 |

## Stakeholders

| Role | Name / Team | Concern |
|------|------------|---------|
| Requirements Author | Skill User | Markdown要件定義書からWord文書を迅速に生成したい |
| Client | Document Recipient | 整形されたWord文書で要件定義書を受領したい |
| Skill Developer | CaldiaWorks | 既存スキル（docx, USDM, EARS）との一貫した連携を維持したい |

## Glossary

| Term | Definition |
|------|-----------|
| USDM | Universal Specification Describing Manner — 要件をRequirement → Reason → Description → Specification階層で構造化する手法 |
| EARS | Easy Approach to Requirements Syntax — WHEN/WHILE/IF/THEN/WHEREパターンによる曖昧性排除記法 |
| docx skill | Claude Codeの既存スキル。Word文書（.docx）の作成・編集機能を提供する |
| Traceability Matrix | 要件（REQ）と仕様（SPEC）の対応関係を示す追跡表 |

---

## Requirements

### REQ-001: 要件定義書のWord形式変換

**Reason**: クライアントへの要件定義書提出にはWord形式が求められるが、現在のUSDM/EARSスキルはMarkdown形式のみ出力する。手動でのフォーマット変換作業を自動化することで、要件定義プロセスの提出工程を効率化する必要がある。

**Description**: USDMスキルおよびEARSスキルが出力するMarkdown形式の要件定義書を、クライアント提出用のWord文書（.docx）に変換するClaude Codeスキル。対象入力はUSDMテンプレート（`skills/usdm/templates/usdm-requirements.md`）に準拠したMarkdownファイルとする。

---

#### REQ-001-1: 入力Markdownの受け付け

**Reason**: Word変換を行うには、USDM/EARS形式のMarkdownファイルを入力として受け付ける必要がある。

**Description**: USDMスキルが出力するテンプレート構造に準拠したMarkdownファイルを入力として受け付ける。EARS記法を含む仕様も入力対象とする。

##### SPEC-001: 入力ファイルパスの指定

WHEN the user specifies a Markdown file path, the skill shall read the file content and proceed with conversion.

##### SPEC-002: USDMテンプレート準拠ファイルの受け付け

The skill shall accept Markdown files that follow the USDM template structure defined in `skills/usdm/templates/usdm-requirements.md`.

##### SPEC-003: EARS記法を含むファイルの受け付け

The skill shall accept Markdown files containing EARS-formatted specifications with WHEN, WHILE, IF, THEN, and WHERE keywords.

---

#### REQ-001-2: USDM階層構造の解析

**Reason**: Markdownの見出しレベルやテキストパターンからUSDM階層（REQ → Reason → Description → SPEC）を正確に識別し、Word文書に変換可能な構造データとして保持する必要がある。

**Description**: Markdown見出しレベル、太字プレフィックス、テーブル構造からUSDM要素を解析する。

##### SPEC-004: 要件要素の識別

The skill shall identify requirement elements from Markdown headings matching the pattern `### REQ-{NNN}: {Title}` for top-level requirements and `#### REQ-{NNN}-{N}: {Title}` for sub-requirements.

##### SPEC-005: 仕様要素の識別

The skill shall identify specification elements from Markdown headings matching the pattern `#### SPEC-{NNN}: {Title}` or `##### SPEC-{NNN}: {Title}`.

##### SPEC-006: Reason/Descriptionフィールドの抽出

The skill shall extract Reason and Description fields from bold-prefixed paragraphs (`**Reason**:` and `**Description**:`).

##### SPEC-007: メタデータの抽出

The skill shall extract metadata fields (Document ID, Version, Status, Author, Created, Last Updated) from the Metadata table in the Markdown document.

##### SPEC-008: トレーサビリティマトリクスの抽出

The skill shall extract the Traceability Matrix from the Markdown table containing columns: Source, REQ, SPEC, and Verification Method.

---

#### REQ-001-3: Word文書の構造生成

**Reason**: クライアントが読みやすく、文書管理に適した書式で要件定義書を受領できるようにするため、USDM階層をWord文書の見出し・段落・表に変換する必要がある。

**Description**: 解析したUSDM構造をWord文書の各要素（表紙、目次、見出し、段落、表）に変換する。既存のdocxスキルを使用して文書を生成する。

---

##### REQ-001-3-1: 表紙の生成

**Reason**: クライアント提出用文書には、文書の識別と管理のために表紙が必要である。

**Description**: Markdownメタデータセクションの情報を使用して表紙ページを生成する。

###### SPEC-009: 表紙の内容

The skill shall generate a cover page containing: document title, Document ID, Version, Status, Author, Created date, and Last Updated date.

###### SPEC-010: 表紙後の改ページ

The skill shall insert a page break after the cover page.

---

##### REQ-001-3-2: 目次の生成

**Reason**: 文書内のナビゲーションを容易にするため、見出しに基づく目次が必要である。

**Description**: Word文書の見出しスタイルに基づく目次を表紙の次に挿入する。

###### SPEC-011: 目次の挿入

The skill shall insert a table of contents after the cover page, referencing Heading 1 through Heading 4 styles.

###### SPEC-012: 目次後の改ページ

The skill shall insert a page break after the table of contents.

---

##### REQ-001-3-3: 見出しレベルのマッピング

**Reason**: USDM階層の各レベルを視覚的に区別するため、Word見出しレベルへの一貫した対応付けが必要である。

**Description**: USDM要素とWord見出しスタイルの対応関係を定義する。

###### SPEC-013: トップレベル要件の見出し

The skill shall map top-level requirements (REQ-NNN) to Heading 2 style.

###### SPEC-014: サブ要件の見出し

The skill shall map sub-requirements (REQ-NNN-N) to Heading 3 style.

###### SPEC-015: 仕様の見出し

The skill shall map specifications (SPEC-NNN) to Heading 4 style.

###### SPEC-016: Reasonフィールドの表示

The skill shall render Reason fields as paragraphs with bold "Reason:" prefix.

###### SPEC-017: Descriptionフィールドの表示

The skill shall render Description fields as paragraphs with bold "Description:" prefix.

---

##### REQ-001-3-4: EARSキーワードの視覚的強調

**Reason**: EARS記法のキーワードは仕様の構造的意味を持つ重要な要素であり、文書内で視覚的に識別できる必要がある。

**Description**: EARS記法のキーワード（WHEN, WHILE, IF, THEN, WHERE）を仕様テキスト内で太字表示する。

###### SPEC-018: EARSキーワードの太字表示

The skill shall render EARS keywords (WHEN, WHILE, IF, THEN, WHERE) in bold within specification text.

---

##### REQ-001-3-5: トレーサビリティマトリクスの出力

**Reason**: 要件と仕様の追跡可能性を文書内で一覧として確認できるようにするため、マトリクスを表形式で出力する必要がある。

**Description**: 抽出したトレーサビリティマトリクスをWord表として生成する。

###### SPEC-019: マトリクス表の生成

The skill shall render the Traceability Matrix as a Word table with columns: Source, REQ, SPEC, and Verification Method.

###### SPEC-020: ヘッダー行の書式

The skill shall apply bold text and shaded background to the header row of the Traceability Matrix table.

---

#### REQ-001-4: ファイル出力

**Reason**: 生成した文書を.docx形式で保存し、クライアントに提出可能な状態にする必要がある。

**Description**: 生成したWord文書を.docxファイルとしてカレントディレクトリに保存する。

##### SPEC-021: 出力先

The skill shall save the generated document as a .docx file in the current working directory.

##### SPEC-022: ファイル命名規則

The skill shall name the output file using the Document ID from the metadata (e.g., `REQ-DOC-20260221-001-requirements-docx.docx`).

---

### REQ-002: docxスキルとの連携

**Reason**: Word文書の生成機能をゼロから実装するのではなく、既存のdocxスキルが提供する文書作成機能を活用することで、開発効率を確保し重複実装を回避する必要がある。

**Description**: Claude Codeの既存スキルであるdocxスキルを使用して、Word文書の生成・書式設定を行う。スキルのSKILL.md内でdocxスキルの呼び出し手順を定義する。

#### SPEC-023: docxスキルへの委譲

The skill shall delegate Word document creation and formatting to the existing `docx` Claude Code skill.

#### SPEC-024: 文書構造の受け渡し

The skill shall pass document structure (headings, paragraphs, tables, page breaks) to the docx skill in the format accepted by that skill.

---

### REQ-003: Markdown入力の不備への対応

**Reason**: 入力Markdownが必ずしもUSDMテンプレートに完全準拠しているとは限らないため、不備がある場合にユーザーに通知して対応を求める必要がある。

**Description**: 入力MarkdownのUSDMテンプレート準拠性を検証し、不足項目をユーザーに報告する。

#### SPEC-025: メタデータ欠落時の通知

IF the input Markdown is missing required metadata fields (Document ID, Version, Author), THEN the skill shall notify the user of the missing fields and prompt for input before proceeding.

#### SPEC-026: USDM構造不備時の通知

IF the input Markdown contains requirements without at least one specification, THEN the skill shall warn the user and list the affected requirement IDs.

---

## Traceability Matrix

| Source | REQ | SPEC | Verification Method |
|--------|-----|------|-------------------|
| Issue #3 — 入力 | REQ-001-1 | SPEC-001, SPEC-002, SPEC-003 | Test |
| Issue #3 — 入力 | REQ-001-2 | SPEC-004, SPEC-005, SPEC-006, SPEC-007, SPEC-008 | Test |
| Issue #3 — 表紙 | REQ-001-3-1 | SPEC-009, SPEC-010 | Inspection |
| Issue #3 — 目次 | REQ-001-3-2 | SPEC-011, SPEC-012 | Inspection |
| Issue #3 — 見出しマッピング | REQ-001-3-3 | SPEC-013, SPEC-014, SPEC-015, SPEC-016, SPEC-017 | Inspection |
| Issue #3 — EARSキーワード | REQ-001-3-4 | SPEC-018 | Inspection |
| Issue #3 — トレーサビリティ | REQ-001-3-5 | SPEC-019, SPEC-020 | Inspection |
| Issue #3 — 出力 | REQ-001-4 | SPEC-021, SPEC-022 | Test |
| Issue #3 — 非機能要件 | REQ-002 | SPEC-023, SPEC-024 | Test |
| Hidden Verb Discovery | REQ-003 | SPEC-025, SPEC-026 | Test |

## Open Questions

| # | Question | Raised By | Status |
|---|----------|-----------|--------|
| 1 | 表紙のレイアウト（ロゴ配置、フォント指定等）の詳細仕様は別途定義が必要か？ | Analysis | Open |
| 2 | Stakeholders/Glossary等のメタデータセクションもWord文書に含めるか？ | Analysis | Open |
| 3 | テンプレートカスタマイズ機能（Issue #3で将来対応とされた）のスコープと優先度は？ | Issue #3 | Open |
| 4 | 変更履歴（Change History）セクションもWord文書に含めるか？ | Analysis | Open |

## Change History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 0.1.0 | 2026-02-21 | — | Initial draft from Issue #3 |
