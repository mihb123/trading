# Wiki Schema

## Domain
Forex & Trading - Chiến lược giao dịch ngoại hối, phân tích kỹ thuật, Price Action, và quản lý rủi ro.

## Conventions
- File names: lowercase, hyphens, no spaces (e.g., `transformer-architecture.md`)
- Every wiki page starts with YAML frontmatter (see below)
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- **Provenance markers:** On pages that synthesize 3+ sources, append `^[trading/raw/articles/source-file.md]`
  at the end of paragraphs whose claims come from a specific source. This lets a reader trace each
  claim back without re-reading the whole raw file. Optional on single-source pages where the
  `sources:` frontmatter is enough.
- **Language:** Pages viết bằng tiếng Việt, thuật ngữ chuyên môn giữ nguyên tiếng Anh trong ngoặc đơn.

## Frontmatter
```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [from taxonomy below]
sources: [trading/raw/articles/source-name.md]
# Optional quality signals:
confidence: high | medium | low        # how well-supported the claims are
contested: true                        # set when the page has unresolved contradictions
contradictions: [other-page-slug]      # pages this one conflicts with
---
```

`confidence` and `contested` are optional but recommended for opinion-heavy or fast-moving
topics. Lint surfaces `contested: true` and `confidence: low` pages for review so weak claims
don't silently harden into accepted wiki fact.

### raw/ Frontmatter

Raw sources ALSO get a small frontmatter block so re-ingests can detect drift:

```yaml
---
source_url: https://example.com/article   # original URL, if applicable
ingested: YYYY-MM-DD
sha256: <hex digest of the raw content below the frontmatter>
---
```

The `sha256:` lets a future re-ingest of the same URL skip processing when content is unchanged,
and flag drift when it has changed. Compute over the body only (everything after the closing
`---`), not the frontmatter itself.

## Tag Taxonomy
- **Phân tích kỹ thuật:** technical-analysis, price-action, candlesticks, chart-patterns, support-resistance, trend-analysis, market-structure, volume
- **Chiến lược:** trading-strategy, multi-timeframe, scalping, swing-trading, day-trading, position-trading, breakout-trading, trend-following, mean-reversion
- **Thị trường:** forex, crypto, stocks, commodities, indices
- **Quản lý rủi ro:** risk-management, position-sizing, psychology, trading-plan
- **Meta:** comparison, reference, checklist, beginner, advanced, controversy

Rule: every tag on a page must appear in this taxonomy. If a new tag is needed,
add it here first, then use it. This prevents tag sprawl.

## Page Thresholds
- **Create a page** when an entity/concept appears in 2+ sources OR is central to one source
- **Add to existing page** when a source mentions something already covered
- **DON'T create a page** for passing mentions, minor details, or things outside the domain
- **Split a page** when it exceeds ~200 lines — break into sub-topics with cross-links
- **Archive a page** when its content is fully superseded — move to `_archive/`, remove from index

## Entity Pages
One page per notable entity. Include:
- Overview / what it is
- Key facts and dates
- Relationships to other entities ([[wikilinks]])
- Source references

## Concept Pages
One page per concept or topic. Include:
- Definition / explanation
- Current state of knowledge
- Open questions or debates
- Related concepts ([[wikilinks]])

## Comparison Pages
Side-by-side analyses. Include:
- What is being compared and why
- Dimensions of comparison (table format preferred)
- Verdict or synthesis
- Sources

## Update Policy
When new information conflicts with existing content:
1. Check the dates — newer sources generally supersede older ones
2. If genuinely contradictory, note both positions with dates and sources
3. Mark the contradiction in frontmatter: `contradictions: [page-name]`
4. Flag for user review in the lint report

## Core Operations

### 1. Ingest

Khi người dùng cung cấp một source (URL, file, paste), tích hợp nó vào wiki:

① **Capture raw source:**
   - URL → dùng `web_extract` để lấy markdown, lưu vào `trading/raw/articles/`
   - PDF → dùng `web_extract` (xử lý PDF), lưu vào `raw/papers/`
   - Text dán → lưu vào thư mục `raw/` tương ứng
   - Đặt tên file mô tả: `trading/raw/articles/karpathy-llm-wiki-2026.md`
   - **Thêm frontmatter raw** (`source_url`, `ingested`, `sha256` của body).
     Khi re-ingest cùng URL: recompute sha256, so sánh với giá trị lưu — skip nếu giống,
     flag drift nếu khác. Điều này đủ rẻ để làm mỗi lần re-ingest và bắt sự thay đổi âm thầm của source.

② **Thảo luận takeaways** với người dùng — điều gì thú vị, điều gì quan trọng cho domain.
   (Skip trong context tự động/cron — đi thẳng đến bước ③)

③ **Check existing content** — search index.md và dùng `search_files` để tìm
   các pages đã có cho các trading/entities/concepts được nhắc. Đây là sự khác biệt
   giữa một wiki đang phát triển và một đống nội dung trùng lặp.

④ **Viết hoặc update wiki pages:**
   - **Entities/concepts mới:** Chỉ tạo pages nếu đáp ứng Page Thresholds
     trong SCHEMA.md (2+ source mentions, hoặc trung tâm với 1 source)
   - **Pages đã có:** Thêm thông tin mới, update facts, bump ngày `updated`.
     Khi thông tin mới mâu thuẫn với content hiện tại, theo Update Policy.
   - **Cross-reference:** Mỗi page mới hoặc update phải có ít nhất 2 wikilinks
     đến các pages khác. Kiểm tra các pages hiện có có link back hay không.
   - **Tags:** Chỉ dùng tags từ taxonomy trong SCHEMA.md
   - **Provenance:** Trên pages tổng hợp 3+ sources, thêm `^[trading/raw/articles/source.md]`
     markers vào cuối các đoạn mà claims đến từ source cụ thể.
   - **Confidence:** Đối với claims nhiều ý kiến, nhanh thay đổi, hoặc single-source,
     set `confidence: medium` hoặc `low`. Đừng đánh dấu `high` trừ khi claim được
     hỗ trợ tốt qua nhiều sources.

⑤ **Update navigation:**
   - Thêm pages mới vào `index.md` ở section đúng, theo thứ tự bảng chữ cái
   - Update tổng số trang và ngày "Last updated" trong header index
   - Ghi vào `log.md`: `## [YYYY-MM-DD] ingest | Source Title`
   - Liệt kê mọi file được tạo hoặc cập nhật trong log entry

⑥ **Report thay đổi** — liệt kê mọi file được tạo hoặc cập nhật cho người dùng.

Một source có thể kích hoạt updates qua 5-15 wiki pages. Điều này bình thường
và mong muốn — hiệu ứng tích lũy.

### 2. Query

Khi người dùng hỏi một câu hỏi về domain của wiki:

① **Đọc `index.md`** để xác định các pages liên quan.
② **Với wiki 100+ pages**, cũng dùng `search_files` trên tất cả `.md` files
   cho các keywords — index riêng có thể bỏ lỡ content liên quan.
③ **Đọc các pages liên quan** dùng `read_file`.
④ **Tổng hợp câu trả lời** từ kiến thức đã biên dịch. Trích dẫn các wiki pages
   bạn đã dùng: "Dựa trên [[page-a]] và [[page-b]]..."
⑤ **Lưu các câu trả lời có giá trị** — nếu câu trả lời là một phân tích so sánh sâu,
   một deep dive, hoặc một synthesis mới, tạo một page trong `queries/` hoặc `trading/comparisons/`.
   Đừng lưu các lookups tầm thường — chỉ lưu các câu trả lời tốn công tái-derive.
⑥ **Update log.md** với query và việc nó có được lưu hay không.

### 3. Lint

Khi người dùng yêu cầu lint, health-check, hoặc audit wiki:

① **Orphan pages:** Tìm các pages không có `[[wikilinks]]` inbound từ các pages khác.
```python
# Dùng execute_code để scan — programmatic qua tất cả wiki pages
import os, re
wiki = "<WIKI_PATH>"
# Scan tất cả .md files trong trading/entities/, trading/concepts/, trading/comparisons/, queries/
# Extract tất cả [[wikilinks]] — build inbound link map
# Pages với zero inbound links là orphans
```

② **Broken wikilinks:** Tìm `[[links]]` trỏ đến pages không tồn tại.

③ **Index completeness:** Mỗi wiki page phải xuất hiện trong `index.md`. So sánh
   filesystem với các entries trong index.

④ **Frontmatter validation:** Mỗi wiki page phải có đủ các trường bắt buộc
   (title, created, updated, type, tags, sources). Tags phải nằm trong taxonomy.

⑤ **Stale content:** Pages có ngày `updated` >90 ngày so với source mới nhất nhắc cùng entities.

⑥ **Contradictions:** Pages cùng topic với các claims mâu thuẫn. Tìm các pages
   có chung tags/entities nhưng phát biểu các facts khác nhau. Surface tất cả pages
   với `contested: true` hoặc `contradictions:` frontmatter để user review.

⑦ **Quality signals:** Liệt kê các pages với `confidence: low` và bất kỳ page nào
   chỉ trích dẫn một source duy nhất mà không có confidence field — các ứng cử viên
   để tìm corroboration hoặc hạ xuống `confidence: medium`.

⑧ **Source drift:** Với mỗi file trong `raw/` có `sha256:` frontmatter, recompute hash
   và flag không khớp. Không khớp cho biết raw file đã được edit (không nên xảy ra)
   hoặc ingested từ một URL đã thay đổi kể từ lần đầu. Không phải lỗi nghiêm trọng,
   nhưng đáng báo cáo.

⑨ **Page size:** Flag các pages vượt 200 lines — ứng cử viên để tách (split).

⑩ **Tag audit:** Liệt kê tất cả tags đang dùng, flag bất kỳ tag nào không có trong
    SCHEMA.md taxonomy.

⑪ **Log rotation:** Nếu log.md vượt 500 entries, rotate nó.

⑫ **Report findings** với các file paths cụ thể và suggested actions, nhóm theo
   severity (broken links > orphans > source drift > contested pages > stale content > style issues).

⑬ **Ghi vào log.md:** `## [YYYY-MM-DD] lint | N issues found`

## Working with the Wiki

### Multi-Topic Structure

```
wiki/                          # WIKI_PATH
├── README.md                  # Topic catalog
├── trading/                   # Current topic
│   ├── SCHEMA.md / index.md / log.md
│   ├── raw/ / concepts/ / entities/ / comparisons/
└── future-topic/              # Next topic (same structure)
```

Mỗi topic có SCHEMA.md, index.md, log.md riêng. Khi làm việc với topic nào, set TOPIC tương ứng.

### Searching

```bash
WIKI="${WIKI_PATH:-$HOME/Documents/wiki}"
TOPIC="trading"  # Thay đổi khi làm topic khác
TOPIC_DIR="$WIKI/$TOPIC"
search_files "transformer" path="$WIKI" file_glob="*.md"

# Tìm pages theo filename
search_files "*.md" target="files" path="$WIKI"

# Tìm pages theo tag
search_files "tags:.*alignment" path="$WIKI" file_glob="*.md"

# Hoạt động gần đây
read_file "$WIKI/log.md" offset=<last 20 lines>
```

### Bulk Ingest

Khi ingest nhiều sources cùng lúc, batch các updates:
1. Đọc tất cả sources trước
2. Xác định tất cả entities và concepts qua tất cả sources
3. Check existing pages cho tất cả (một search pass, không phải N)
4. Tạo/update pages trong một pass (tránh redundant updates)
5. Update index.md một lần ở cuối
6. Ghi một log entry duy nhất bao phủ cả batch

### Archiving

Khi content bị thay thế hoàn toàn hoặc scope thay đổi:
1. Tạo `_archive/` nếu chưa có
2. Di chuyển page đến `_archive/` với path gốc (ví dụ `_archive/trading/entities/old-page.md`)
3. Xóa khỏi `index.md`
4. Update các pages có link đến nó — thay wikilink bằng plain text + "(archived)"
5. Log action archive

### Obsidian Integration

Thư mục wiki hoạt động như một Obsidian vault:
- `[[wikilinks]]` render thành các links clickable
- Graph View visualize knowledge network
- YAML frontmatter hỗ trợ Dataview queries
- Folder `raw/assets/` lưu images referenced via `![[image.png]]`

Để sử dụng tốt:
- Set Obsidian attachment folder thành `raw/assets/`
- Enable "Wikilinks" trong Obsidian settings
- Cài đặt plugin Dataview cho các queries như `TABLE tags FROM "entities" WHERE contains(tags, "company")`

Nếu dùng skill Obsidian cùng lúc, set `OBSIDIAN_VAULT_PATH` trỏ đến root `$WIKI_PATH` (không phải topic con). Obsidian sẽ hiển thị tất cả topics cùng lúc.

### Ghi chú Git
origin  git@github.com:mihb123/trading.git
```bash
WIKI_DIR="${WIKI_PATH:-$HOME/Documents/wiki}"
cd "$WIKI_DIR"
git status
git add --all
git diff --cached
git commit -m "trading: <message>"
git push origin main
```

## Pitfalls

- **KHÔNG BAO GIỜ sửa files trong `raw/`** — sources là bất biến. Sửa chữa đi vào wiki pages.
- **LUÔN orient trước** — đọc SCHEMA + index + recent log trước bất kỳ operation nào trong một session mới.
  Việc bỏ qua gây ra duplicates và missed cross-references.
- **LUÔN update index.md và log.md** — việc bỏ qua khiến wiki xuống cấp. Đây là xương sống điều hướng.
- **Đừng tạo pages cho các mentions tầm thường** — tuân theo Page Thresholds trong SCHEMA.md. Một tên
  xuất hiện một lần trong footnote không đủ điều kiện để có entity page.
- **Đừng tạo pages không có cross-references** — các pages cô lập là vô hình. Mỗi page phải có
  ít nhất 2 wikilinks đến các pages khác.
- **Frontmatter là bắt buộc** — nó cho phép search, filtering, và staleness detection.
- **Tags phải từ taxonomy** — tags tự do sẽ biến thành noise. Thêm tags mới vào SCHEMA.md trước,
  rồi hãy dùng chúng.
- **Giữ các pages dễ quét** — một page nên đọc được trong 30 giây. Tách các pages quá 200 lines.
  Di chuyển phân tích chi tiết sang các deep-dive pages riêng.
- **Hỏi trước khi mass-update** — nếu một ingest sẽ chạm 10+ existing pages, xác nhận scope với user.
- **Rotate log** — khi log.md vượt 500 entries, đổi tên thành `log-YYYY.md` và start fresh.
  Agent nên kiểm tra log size trong lúc lint.
- **Xử lý contradictions một cách rõ ràng** — đừng ghi đè lặng lẽ. Ghi lại cả hai claims với ngày,
  đánh dấu trong frontmatter, flag để user review.
