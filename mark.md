Cấu trúc thư mục (Directory Structure) bạn vừa đưa ra thực sự **nằm ở đẳng cấp của một dự án Enterprise QA (Doanh nghiệp) thực tế!** Việc bóc tách từng Test Case ra các file `.md` riêng biệt (`TC-FR01-DT-001.md`), tách riêng khu vực chạy test (Test Runs) và báo cáo lỗi (Bug Reports) sẽ giúp quản lý phiên bản Git cực kỳ sạch sẽ. Giảng viên chấm bài chắc chắn sẽ cho bạn điểm tối đa ở phần trình bày và tư duy hệ thống (Traceability).

Tuy nhiên, với cấu trúc xé nhỏ này, **Agent Skill ở bản trước sẽ không còn phù hợp**. Thay vì bảo AI in ra một cái bảng Markdown dài ngoằng, ta phải "ép" AI xuất ra định dạng từng file riêng biệt và chỉ định đúng đường dẫn lưu file.

Dưới đây là 3 bước để bạn triển khai thần tốc cấu trúc này:

---

### 🛠️ BƯỚC 1: LỆNH KHỞI TẠO TỰ ĐỘNG CẤU TRÚC THƯ MỤC

Đừng tạo tay từng thư mục! Hãy mở Terminal / Git Bash tại thư mục bạn định làm bài tập (Project Root), copy và chạy toàn bộ khối lệnh dưới đây. Nó sẽ tự động tạo khung xương cho 4 tính năng bạn đã chọn (FR-05, FR-08, FR-12, FR-22):

```bash
# Khởi tạo Git
git init

# Tạo các thư mục gốc và thư mục con
mkdir -p tests/test-cases/{FR-05-search,FR-08-checkout,FR-12-access,FR-22-mobile}/{domain-testing,bva}
mkdir -p tests/test-runs tests/test-summary
mkdir -p bug-reports/screenshots
mkdir -p ai-gap-analysis reports skills .github/ISSUE_TEMPLATE

# Tạo sẵn các file rỗng bắt buộc
touch README.md git-log.txt
touch tests/test-summary/traceability-matrix.md tests/test-summary/test-summary-report.md
touch reports/main-report.md reports/ai-audit-report.md reports/ai-critique.md
touch skills/domain-testing-skill.md skills/bva-skill.md

# Tạo file chạy test & gap analysis cho từng tính năng
for fr in FR-05-search FR-08-checkout FR-12-access FR-22-mobile; do
  touch tests/test-runs/${fr}-run.md
  touch ai-gap-analysis/${fr}-gap-analysis.md
done

# Commit đầu tiên
git add .
git commit -m "chore: setup initial QA project structure"

echo "✅ Cấu trúc thư mục đã được tạo thành công!"

```

---

### 👑 BƯỚC 2: NÂNG CẤP AGENT SKILL (V2 - Structure Aware)

Hãy copy nội dung tiếng Anh dưới đây, lưu vào file `skills/domain-testing-skill.md` và cài đặt nó làm `.cursorrules` hoặc Custom Instructions cho công cụ AI bạn đang dùng.
_(Lưu ý: Nếu bạn dùng **Cursor IDE** hoặc **Cline**, với format `` bên dưới, AI sẽ tự động tạo file và lưu thẳng vào ổ cứng cho bạn mà không cần copy-paste thủ công!)_

````markdown
# AGENT ROLE & OBJECTIVE

You are an ISTQB-Certified QA Test Designer. Your objective is to assist a student in the "HW02 - Domain Testing" assignment for the "EShop" SUT.
CRITICAL CONSTRAINT: You MUST act step-by-step. NEVER act as a black-box. STOP and WAIT for user approval after completing each step.

# FILE STRUCTURE DIRECTIVES

The user is maintaining a strict project repository. When you generate test cases or bug reports, you MUST format your output as a Markdown code block. At the very top of the code block (outside of it), you MUST write the exact target file path as an HTML comment, following this structure:

- Domain Tests: ``
- BVA Tests: ``
- Test Runs: ``
- Bug Reports: ``
- Gap Analysis: ``

# STEP-BY-STEP WORKFLOW

## STEP 1 & 2 & 3: Variables, EP, and BVA Logic

- **Action:** Read SUT context. Identify variables, define EP partitions, and calculate BVA boundaries.
- **Wait:** Present the analysis logic to the user and ask: _"Are these logic tables correct? Shall I generate the individual Markdown Test Case files?"_ -> STOP.

## STEP 4: Atomic Test Case File Generation

- **Action:** Translate approved EP and BVA into individual Markdown files.
- **Format Example:**
  ```markdown
  # Test Case: TC-FR08-DT-001

  **Feature:** FR-08 Checkout | **Technique:** Equivalence Partitioning
  **Test Data:** `total_amount = 500000`
  **Expected Result:** 200 OK, Order Created.
  **Actual Result:** [Leave Blank]
  **Status:** [Leave Blank]
  ```
````

- **Wait:** Ask the user to execute these test cases manually on the SUT and report back. -> STOP.

## STEP 5: Bug Report & Test Run File

- **Trigger:** User reports manual test results.
- **Action 1:** Generate `tests/test-runs/[FR-ID]-run.md` summarizing Passed/Failed TCs.
- **Action 2:** Generate `bug-reports/BUG-[XXX].md` using standard GitHub Issue format (Title, Severity, Steps to Reproduce, Expected vs Actual). Include an image placeholder `![Screenshot](./screenshots/BUG-XXX.png)`.
- **Action 3:** Generate `ai-gap-analysis/[FR-ID]-gap-analysis.md` explaining WHY the AI initially missed this bug or why human intervention was necessary (e.g., Happy-path bias).
- **Wait:** Say "Done" -> STOP.

# MANDATORY: AI AUDIT LOG

Append this at the end of EVERY response:

```text
=== AI AUDIT LOG ENTRY ===
* Tool: [LLM Name]
* Date: [Current Date]
* User Prompt: [Summary]
* AI Action: [Summary]
==========================

```

```

---

### 🎬 BƯỚC 3: QUY TRÌNH "LÀM NHƯ GIÁM ĐỐC" (WORKFLOW & GIT)

Với cấu trúc mới này, bạn không cần phải tự gõ văn bản nữa. Bạn chỉ đóng vai trò là "Người ra quyết định" (Reviewer) và "Người chạy test" (Tester). Cứ xong bước nào là bạn Commit Git bước đó để lấy file log nộp bài.
Kịch bản thực chiến cho **FR-08 (Checkout)**:

1. **Khởi tạo & Thiết kế:**
   * **Bạn chat:** *"Kích hoạt Skill. Phân tích FR-08 Checkout. Hãy làm Step 1, 2, 3 tập trung vào biến `total_amount`."*
   * **AI:** Sinh ra các lý luận logic EP và BVA.
   * **Bạn:** Copy các đoạn logic này dán vào `reports/main-report.md` (Để làm nội dung báo cáo nộp).

2. **Sinh File Test Case:**
   * **Bạn chat:** *"Logic chuẩn rồi. Hãy làm Step 4 sinh các file Test Case cho FR-08."*
   * **AI:** Xuất ra các khối code kèm đường dẫn file `TC-FR08-DT-001.md`, v.v...
   * **Bạn (nếu dùng Cursor/Cline):** Chỉ cần bấm nút *Apply/Save*, AI tự động ghi file vào đúng thư mục `tests/test-cases/...`. Nếu không có, bạn copy dán thủ công.
   * 💻 **Gõ Terminal:** `git add . && git commit -m "test: generate EP and BVA cases for FR-08"`

3. **Chạy Thực Tế & Bắt Lỗi:**
   * **Bạn thao tác tay:** Bật server Backend lên, dùng Postman gửi request mua hàng với số tiền âm (`total_amount: -50000`). Màn hình báo thành công (bug!). Chụp ảnh lưu vào `bug-reports/screenshots/BUG-001.png`.
   * **Bạn chat:** *"Tôi đã chạy test. File `TC-FR08-BVA-002` (test số âm) FAILED. Server vẫn cho qua thay vì báo lỗi. Hãy làm Step 5: Viết Run report, Bug report, và Gap analysis."*
   * **AI:** Viết mọi thứ tự động vào đúng các file.
   * 💻 **Gõ Terminal:** `git add . && git commit -m "fix: execute FR-08, log BUG-001 and gap analysis"`

4. **Tổng hợp cuối cùng (Sau khi làm xong 4 Features):**
   * Copy gom các block `=== AI AUDIT LOG ENTRY ===` lưu vào `reports/ai-audit-report.md`.
   * Điền nốt bảng điểm ở `README.md`.
   * 💻 **Gõ Terminal:** Chạy lệnh `git log > git-log.txt` (để xuất file lịch sử commit nộp bài).
   * Nén toàn bộ thư mục gốc lại thành `MSSV_HW02_AI_DomainTesting_100.zip`.

Cách tổ chức thư mục kết hợp cùng Agent Skill này không chỉ giúp bạn nhàn hơn, thỏa mãn 100% tiêu chí chấm điểm, mà còn tạo ra một "Portfolio" thực chiến để bạn khoe khi đi phỏng vấn xin việc (Intern/Fresher QA) sau này!

```
