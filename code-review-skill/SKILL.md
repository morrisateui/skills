---
name: code-review-skill
description: Perform focused code reviews on branch changes based on git diff from merge-base. Reviews only modified code and directly related functions/services, providing bilingual feedback in Traditional Chinese and English.
---

# Code Review Skill

## Purpose
Review code changes introduced in a specific branch/commit by analyzing the diff from merge-base.
Provide focused, constructive feedback without expanding scope to unmodified code.
If possible, use older versions of code review markdown files to check whether an issue has been resolved, which could be sorted by datetime.
And output the result to root directory with format `code-review-{BRANCH}-{ymdhm}.md`.

When the code review is completed, according review output making a implement list sorting by impact, then use `code-review-improve-skill` to implmenet suggestions.


## Core Principles

### Scope Discipline
**CRITICAL**: Review ONLY the code changes introduced in this branch and commit based on the diff of merge-base, plus any functions or services directly called or related to these changes.

**DO NOT**:
- ❌ Expand the review scope to code that is not modified by this PR
- ❌ Review entire files when only specific functions changed
- ❌ Suggest refactoring unmodified legacy code
- ❌ Critique architectural decisions outside the PR scope

**DO**:
- ✅ Focus on the actual diff/changes introduced
- ✅ Review functions directly called by changed code
- ✅ Examine services/modules imported or modified by changes
- ✅ Consider impact on directly related components

## Review Focus Areas

### 1. Code Quality and Best Practices
- Naming conventions and code readability
- Adherence to language/framework conventions
- Code organization and structure
- DRY (Don't Repeat Yourself) principles
- Separation of concerns
- Design patterns usage

### 2. Potential Bugs or Issues
- Logic errors or edge cases
- Null/undefined handling
- Error handling and recovery
- Race conditions or concurrency issues
- Off-by-one errors
- Type safety violations
- Incorrect API usage

### 3. Performance Considerations
- Algorithm efficiency (time/space complexity)
- Unnecessary loops or computations
- Memory leaks or resource management
- Database query optimization
- Network request efficiency
- Caching opportunities
- Bundle size impact (for frontend)

### 4. Security Implications
- Input validation and sanitization
- SQL injection vulnerabilities
- XSS (Cross-Site Scripting) risks
- Authentication and authorization
- Sensitive data exposure
- Dependency vulnerabilities
- CSRF protection
- API security best practices

### 5. Test Coverage
- Are new features covered by tests?
- Are edge cases tested?
- Test quality and assertions
- Mock usage appropriateness
- Integration vs unit test balance
- Test maintainability

### 6. Documentation Updates
- Code comments for complex logic
- API documentation (JSDoc, docstrings, etc.)
- README updates if needed
- CHANGELOG entries
- Type definitions/interfaces
- Migration guides if breaking changes

## Output Format

### Structure Your Review

**1. Summary (概述)**
Provide a brief overview of the changes in both languages:
- What was changed
- Overall assessment
- Major concerns (if any)

**2. Detailed Feedback (詳細回饋)**

For each concern or suggestion, use this format:

```
📍 [File Path] Line X-Y
🔍 Issue Category: [Code Quality / Bug / Performance / Security / Testing / Documentation]

**English:**
[Detailed explanation of the issue]
[Why this matters]
[Specific suggestion for improvement]

**繁體中文：**
[問題的詳細說明]
[為什麼這很重要]
[具體的改進建議]

Example code (if applicable):
```language
// Suggested improvement
code here
```
```

**3. Positive Highlights (優點)**
- Acknowledge good practices in the changes
- Recognize improvements or clever solutions

**4. Priority Levels**
Use these labels:
- 🔴 **Critical (必須修正)**: Must fix before merge (security, bugs)
- 🟡 **Important (建議修正)**: Should fix (performance, best practices)
- 🟢 **Optional (可選)**: Nice to have (minor improvements, style)

## Example Review Format

```markdown
## Code Review - [PR Title/Branch Name]

### Summary 概述

**English:**
This PR introduces [brief description]. Overall the changes are well-structured, but there are [X] concerns that should be addressed before merging.

**繁體中文：**
此 PR 引入了 [簡要描述]。整體而言，變更結構良好，但在合併前應該處理 [X] 個問題。

---

### Detailed Feedback 詳細回饋

#### 🔴 Critical Issues 必須修正的問題

📍 **src/api/userController.js** Line 45-52
🔍 **Issue Category:** Security

**English:**
The user input is directly concatenated into the SQL query without sanitization, creating an SQL injection vulnerability.

**繁體中文：**
使用者輸入直接串接到 SQL 查詢中而沒有進行清理，造成 SQL 注入漏洞。

**Suggestion 建議：**
```javascript
// ❌ Current (vulnerable)
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ Suggested (safe)
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [userId]);
```

---

#### 🟡 Important Improvements 建議修正的問題

📍 **src/utils/dataProcessor.js** Line 23-35
🔍 **Issue Category:** Performance

**English:**
The nested loop has O(n²) complexity. For large datasets, this could cause performance issues.

**繁體中文：**
巢狀迴圈具有 O(n²) 的複雜度。對於大型資料集，這可能會導致效能問題。

**Suggestion 建議：**
Consider using a Map for O(n) lookup instead.

---

#### 🟢 Optional Enhancements 可選的改進

📍 **src/components/Button.tsx** Line 12-18
🔍 **Issue Category:** Code Quality

**English:**
The component could benefit from extracting the style logic into a separate function for better readability.

**繁體中文：**
將樣式邏輯提取到單獨的函數中，可以提高可讀性。

---

### Positive Highlights 優點

✅ Excellent error handling in the API layer
✅ Good test coverage for new features
✅ Clear and descriptive variable names
✅ 良好的錯誤處理機制
✅ 新功能有充分的測試覆蓋
✅ 清晰且具描述性的變數命名

---

### Testing Coverage 測試覆蓋

**English:**
- ✅ Unit tests added for new functions
- ⚠️ Missing integration tests for the new API endpoint
- ⚠️ Edge cases not fully covered

**繁體中文：**
- ✅ 已為新函數添加單元測試
- ⚠️ 缺少新 API 端點的整合測試
- ⚠️ 邊界情況未完全覆蓋

---

### Documentation 文件

**English:**
- ✅ Code comments added for complex logic
- ⚠️ API documentation needs updating for new endpoints
- ℹ️ Consider adding a migration guide for breaking changes

**繁體中文：**
- ✅ 已為複雜邏輯添加程式碼註解
- ⚠️ 新端點的 API 文件需要更新
- ℹ️ 考慮為破壞性變更添加遷移指南

---

### Overall Recommendation 整體建議

**English:**
[Approve / Request Changes / Comment]
The PR is [nearly ready / needs work]. Please address the critical issues before merging.

**繁體中文：**
[批准 / 請求修改 / 評論]
此 PR [幾乎準備就緒 / 需要修改]。請在合併前處理關鍵問題。
```

## Guidelines for Effective Reviews

### Be Constructive
- Focus on the code, not the person
- Explain the "why" behind suggestions
- Offer specific solutions, not just criticism
- Acknowledge good work

### Be Specific
- Reference exact line numbers
- Provide code examples
- Link to relevant documentation or standards
- Explain potential impacts

### Be Consistent
- Apply the same standards throughout
- Follow team conventions and style guides
- Prioritize issues appropriately

### Be Efficient
- Group related issues together
- Don't nitpick trivial style issues if automated linting exists
- Focus on what matters most

## Language Guidelines

### Traditional Chinese Translation Tips
- Use technical terms commonly accepted in Taiwan/Hong Kong
- Keep code examples in English (don't translate variable names)
- Maintain professional but friendly tone
- Use traditional characters (繁體中文), not simplified (简体中文)

### Common Technical Terms
- PR/Pull Request: PR / 拉取請求
- Merge: 合併
- Branch: 分支
- Commit: 提交
- Bug: 錯誤 / 漏洞
- Performance: 效能
- Security: 安全性
- Test Coverage: 測試覆蓋率
- Refactoring: 重構
- Code Review: 程式碼審查

## Usage

When a user provides:
1. A git diff
2. Code changes from a branch
3. A pull request description
4. Files that changed

You should:
1. **Analyze only the changed lines** and directly related functions
2. **Identify issues** across the six focus areas
3. **Prioritize findings** (Critical → Important → Optional)
4. **Provide bilingual feedback** in the structured format
5. **Suggest specific improvements** with code examples
6. **Acknowledge good practices** in the changes

Remember: **Stay within scope**. This is a focused review of changes, not a full codebase audit.

---

## Ready to Review

I'm ready to perform code reviews following this framework. Please provide:
- The git diff or code changes
- Context about the branch/PR (optional but helpful)
- Any specific concerns you want me to focus on (optional)

I'll deliver a focused, bilingual review with actionable feedback.

