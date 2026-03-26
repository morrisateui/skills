---
name: code-review-improve-skill
description: Implement and apply code review suggestions by generating actual code changes, refactoring implementations, and ensuring suggestions are properly addressed. Provides bilingual guidance in Traditional Chinese and English.
---

# Code Review Improvement Skill

## Purpose
Transform code review feedback into actionable code changes. This skill helps developers implement suggestions from code reviews by:
- Generating corrected/improved code
- Providing step-by-step implementation guidance
- Explaining the rationale behind each change
- Verifying that suggestions have been properly addressed
- Creating implementation checklists

## Core Principles

### First thing first
- Trying to keep codebase convention if possible
- Keep consistency as possible, if need to break it explain the reason properly and shortly

### Precision and Context
- Understand the original issue and suggestion contextz
- Generate code that matches the project's style and conventions
- Preserve existing functionality while implementing improvements
- Consider edge cases and potential side effects

### Educational Approach
- Explain WHY each change is made
- Show BEFORE and AFTER comparisons
- Provide implementation notes for complex changes
- Reference best practices and documentation

### Completeness
- Address all aspects of the review feedback
- Include necessary imports, types, or dependencies
- Update related tests if needed
- Suggest documentation updates

## Input Formats

This skill can work with:

1. **Code Review Feedback**
    - Markdown code review files
    - Specific review comments with line numbers
    - Issue descriptions from review tools

2. **Original Code Context**
    - Current implementation
    - File paths and line numbers
    - Related functions or modules

3. **Improvement Requests**
    - "Fix the SQL injection issue in userController.js"
    - "Implement the performance optimization suggested"
    - "Apply all critical fixes from the last review"

## Output Format

### Structure Your Implementation

**1. Change Summary (變更摘要)**
```markdown
## Implementation Summary 實作摘要

**English:**
This implementation addresses [X] issues from the code review:
- [Issue 1]: [Brief description]
- [Issue 2]: [Brief description]

**繁體中文：**
此實作解決了程式碼審查中的 [X] 個問題：
- [問題 1]: [簡要描述]
- [問題 2]: [簡要描述]
```

**2. Detailed Changes (詳細變更)**

For each change:

```markdown
### 📝 Change #X: [Change Title]
**Priority:** 🔴 Critical / 🟡 Important / 🟢 Optional
**Category:** [Security / Performance / Code Quality / Bug Fix / Testing / Documentation]
**Files:** `path/to/file.js` (Lines X-Y)

#### Original Issue 原始問題

**English:**
[Description of the original problem from review]

**繁體中文：**
[原始問題的描述]

#### Implementation 實作方式

**English:**
[Explanation of how the fix works]
[Why this approach was chosen]
[Any trade-offs or considerations]

**繁體中文：**
[修復方式的說明]
[為什麼選擇這個方法]
[任何權衡或考量]

#### Code Changes 程式碼變更

**Before 修改前：**
```language
// Current problematic code
[original code]
```

**After 修改後：**
```language
// Improved implementation
[new code]
```

#### Testing Notes 測試注意事項

**English:**
- Test case 1: [Description]
- Test case 2: [Description]
- Edge cases to verify: [List]

**繁體中文：**
- 測試案例 1: [描述]
- 測試案例 2: [描述]
- 需要驗證的邊界情況: [列表]

#### Related Changes 相關變更

If this change requires updates elsewhere:
- [ ] Update tests in `test/file.test.js`
- [ ] Update documentation in `README.md`
- [ ] Update type definitions in `types.ts`
```

**3. Verification Checklist (驗證清單)**

```markdown
## Verification Checklist 驗證清單

### Code Changes 程式碼變更
- [ ] All suggested changes implemented
- [ ] Code follows project conventions
- [ ] No new linting errors introduced
- [ ] Imports/dependencies updated

### Testing 測試
- [ ] Existing tests still pass
- [ ] New tests added for new functionality
- [ ] Edge cases covered
- [ ] Integration tests updated

### Documentation 文件
- [ ] Code comments updated
- [ ] API documentation updated
- [ ] README updated (if needed)
- [ ] CHANGELOG entry added

### Security & Performance 安全性與效能
- [ ] Security vulnerabilities addressed
- [ ] Performance improvements verified
- [ ] No new performance regressions
- [ ] Resource cleanup handled properly
```

## Implementation Patterns

### Pattern 1: Security Fix

**Example: SQL Injection**

```markdown
### 📝 Change #1: Fix SQL Injection Vulnerability
**Priority:** 🔴 Critical
**Category:** Security
**Files:** `src/api/userController.js` (Lines 45-52)

#### Implementation 實作方式

**English:**
Replace string concatenation with parameterized queries using prepared statements. This prevents user input from being interpreted as SQL commands.

**繁體中文：**
使用參數化查詢（prepared statements）取代字串串接，防止使用者輸入被解釋為 SQL 指令。

#### Code Changes 程式碼變更

**Before 修改前：**
```javascript
const getUserById = async (userId) => {
  const query = `SELECT * FROM users WHERE id = ${userId}`;
  const result = await db.query(query);
  return result.rows[0];
};
```

**After 修改後：**
```javascript
const getUserById = async (userId) => {
  // Use parameterized query to prevent SQL injection
  const query = 'SELECT * FROM users WHERE id = $1';
  const result = await db.query(query, [userId]);
  
  if (!result.rows.length) {
    throw new Error('User not found');
  }
  
  return result.rows[0];
};
```

#### Additional Security Measures 額外的安全措施

```javascript
// Add input validation
const getUserById = async (userId) => {
  // Validate input type
  const id = parseInt(userId, 10);
  if (isNaN(id) || id <= 0) {
    throw new Error('Invalid user ID');
  }
  
  const query = 'SELECT * FROM users WHERE id = $1';
  const result = await db.query(query, [id]);
  
  if (!result.rows.length) {
    throw new Error('User not found');
  }
  
  return result.rows[0];
};
```

#### Testing Notes 測試注意事項

**Test Cases:**
```javascript
describe('getUserById', () => {
  it('should fetch user with valid ID', async () => {
    const user = await getUserById(1);
    expect(user).toBeDefined();
  });
  
  it('should reject SQL injection attempts', async () => {
    await expect(getUserById("1 OR 1=1")).rejects.toThrow('Invalid user ID');
  });
  
  it('should handle non-existent users', async () => {
    await expect(getUserById(99999)).rejects.toThrow('User not found');
  });
});
```
```

### Pattern 2: Performance Optimization

**Example: Reducing Time Complexity**

```markdown
### 📝 Change #2: Optimize Nested Loop Performance
**Priority:** 🟡 Important
**Category:** Performance
**Files:** `src/utils/dataProcessor.js` (Lines 23-35)

#### Implementation 實作方式

**English:**
Replace nested O(n²) loop with Map-based O(n) lookup. This reduces processing time from quadratic to linear, significantly improving performance for large datasets.

**繁體中文：**
使用 Map 結構的 O(n) 查找取代 O(n²) 的巢狀迴圈，將處理時間從平方複雜度降低到線性複雜度，大幅提升大型資料集的效能。

#### Code Changes 程式碼變更

**Before 修改前：**
```javascript
function matchUsers(users, orders) {
  const result = [];
  
  for (const order of orders) {
    for (const user of users) {
      if (user.id === order.userId) {
        result.push({
          ...order,
          userName: user.name,
          userEmail: user.email
        });
        break;
      }
    }
  }
  
  return result;
}
```

**After 修改後：**
```javascript
function matchUsers(users, orders) {
  // Create a Map for O(1) lookup time
  const userMap = new Map(
    users.map(user => [user.id, user])
  );
  
  // Single pass through orders - O(n) complexity
  const result = orders.map(order => {
    const user = userMap.get(order.userId);
    
    if (!user) {
      console.warn(`No user found for order ${order.id}`);
      return order;
    }
    
    return {
      ...order,
      userName: user.name,
      userEmail: user.email
    };
  });
  
  return result;
}
```

#### Performance Comparison 效能比較

```javascript
// Benchmark results:
// Dataset: 1,000 users, 10,000 orders

// Before: ~2,500ms (O(n²))
// After:  ~15ms (O(n))
// Improvement: 166x faster
```

#### Testing Notes 測試注意事項

```javascript
describe('matchUsers Performance', () => {
  it('should handle large datasets efficiently', () => {
    const users = Array.from({ length: 1000 }, (_, i) => ({
      id: i,
      name: `User ${i}`,
      email: `user${i}@example.com`
    }));
    
    const orders = Array.from({ length: 10000 }, (_, i) => ({
      id: i,
      userId: Math.floor(Math.random() * 1000)
    }));
    
    const start = Date.now();
    const result = matchUsers(users, orders);
    const duration = Date.now() - start;
    
    expect(result).toHaveLength(10000);
    expect(duration).toBeLessThan(100); // Should complete in <100ms
  });
  
  it('should handle missing users gracefully', () => {
    const users = [{ id: 1, name: 'User 1', email: 'user1@example.com' }];
    const orders = [
      { id: 1, userId: 1 },
      { id: 2, userId: 999 } // Non-existent user
    ];
    
    const result = matchUsers(users, orders);
    expect(result).toHaveLength(2);
    expect(result[1].userName).toBeUndefined();
  });
});
```
```

### Pattern 3: Code Quality Improvement

**Example: Extract Complex Logic**

```markdown
### 📝 Change #3: Improve Readability with Function Extraction
**Priority:** 🟢 Optional
**Category:** Code Quality
**Files:** `src/components/Button.tsx` (Lines 12-28)

#### Implementation 實作方式

**English:**
Extract complex style logic into separate functions to improve readability and maintainability. This follows the Single Responsibility Principle and makes the component easier to test.

**繁體中文：**
將複雜的樣式邏輯提取到獨立函數中，提升可讀性和可維護性。這符合單一職責原則，使元件更容易測試。

#### Code Changes 程式碼變更

**Before 修改前：**
```typescript
const Button: React.FC<ButtonProps> = ({ variant, size, disabled, children }) => {
  const className = `btn ${
    variant === 'primary' ? 'bg-blue-500 text-white hover:bg-blue-600' :
    variant === 'secondary' ? 'bg-gray-500 text-white hover:bg-gray-600' :
    variant === 'outline' ? 'border-2 border-blue-500 text-blue-500 hover:bg-blue-50' :
    'bg-white text-gray-800 hover:bg-gray-50'
  } ${
    size === 'sm' ? 'px-2 py-1 text-sm' :
    size === 'lg' ? 'px-6 py-3 text-lg' :
    'px-4 py-2'
  } ${
    disabled ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer'
  }`;
  
  return <button className={className} disabled={disabled}>{children}</button>;
};
```

**After 修改後：**
```typescript
// Extract style logic into separate functions
const getVariantStyles = (variant: ButtonVariant): string => {
  const variants = {
    primary: 'bg-blue-500 text-white hover:bg-blue-600',
    secondary: 'bg-gray-500 text-white hover:bg-gray-600',
    outline: 'border-2 border-blue-500 text-blue-500 hover:bg-blue-50',
    default: 'bg-white text-gray-800 hover:bg-gray-50'
  };
  
  return variants[variant] || variants.default;
};

const getSizeStyles = (size: ButtonSize): string => {
  const sizes = {
    sm: 'px-2 py-1 text-sm',
    md: 'px-4 py-2',
    lg: 'px-6 py-3 text-lg'
  };
  
  return sizes[size] || sizes.md;
};

const getDisabledStyles = (disabled: boolean): string => {
  return disabled ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer';
};

const Button: React.FC<ButtonProps> = ({ 
  variant = 'default', 
  size = 'md', 
  disabled = false, 
  children 
}) => {
  const className = [
    'btn',
    getVariantStyles(variant),
    getSizeStyles(size),
    getDisabledStyles(disabled)
  ].join(' ');
  
  return (
    <button className={className} disabled={disabled}>
      {children}
    </button>
  );
};
```

#### Benefits 優點

**English:**
- ✅ Easier to read and understand
- ✅ Individual functions can be tested separately
- ✅ Easy to add new variants or sizes
- ✅ Better TypeScript type checking
- ✅ Follows Single Responsibility Principle

**繁體中文：**
- ✅ 更容易閱讀和理解
- ✅ 個別函數可以獨立測試
- ✅ 容易新增新的變體或尺寸
- ✅ 更好的 TypeScript 類型檢查
- ✅ 符合單一職責原則

#### Testing 測試

```typescript
describe('Button Style Functions', () => {
  describe('getVariantStyles', () => {
    it('should return correct styles for primary variant', () => {
      expect(getVariantStyles('primary')).toBe('bg-blue-500 text-white hover:bg-blue-600');
    });
    
    it('should return default styles for unknown variant', () => {
      expect(getVariantStyles('unknown' as any)).toBe('bg-white text-gray-800 hover:bg-gray-50');
    });
  });
  
  describe('getSizeStyles', () => {
    it('should return correct styles for each size', () => {
      expect(getSizeStyles('sm')).toBe('px-2 py-1 text-sm');
      expect(getSizeStyles('md')).toBe('px-4 py-2');
      expect(getSizeStyles('lg')).toBe('px-6 py-3 text-lg');
    });
  });
});
```
```

## Usage Scenarios

### Scenario 1: Apply All Critical Fixes

**Input:**
```
"Apply all critical security fixes from code-review-202401151430.md"
```

**Output:**
- Generate complete implementations for all 🔴 Critical issues
- Provide consolidated diff
- Include comprehensive testing checklist
- List all files that need to be changed

### Scenario 2: Implement Specific Suggestion

**Input:**
```
"Implement the performance optimization suggestion for the dataProcessor.js file"
```

**Output:**
- Detailed before/after code
- Performance benchmarking approach
- Test cases
- Migration notes if needed

### Scenario 3: Refactor Based on Review

**Input:**
```
"Refactor the authentication module based on the code quality suggestions"
```

**Output:**
- Step-by-step refactoring plan
- Multiple code examples
- Incremental implementation approach
- Backward compatibility notes

## Best Practices

### When Generating Implementations

1. **Preserve Functionality**
   - Ensure existing behavior is maintained
   - Don't introduce breaking changes without noting them
   - Consider backward compatibility

2. **Follow Project Conventions**
   - Match existing code style
   - Use project's naming conventions
   - Follow established patterns

3. **Be Complete**
   - Include all necessary imports
   - Update type definitions
   - Consider error handling
   - Add appropriate logging

4. **Provide Context**
   - Explain why the change is needed
   - Document any trade-offs
   - Note potential side effects
   - Reference related code

5. **Enable Testing**
   - Suggest test cases
   - Provide test examples
   - Consider edge cases
   - Include performance tests if relevant

## Implementation Checklist Template

Use this template for tracking implementation progress:

```markdown
## Implementation Progress 實作進度

### High Priority Changes 高優先級變更
- [ ] 🔴 Fix SQL injection in userController.js
  - [ ] Update query implementation
  - [ ] Add input validation
  - [ ] Write security tests
  - [ ] Update documentation

### Medium Priority Changes 中優先級變更
- [ ] 🟡 Optimize dataProcessor performance
  - [ ] Replace nested loops with Map
  - [ ] Add performance benchmarks
  - [ ] Update existing tests

### Low Priority Changes 低優先級變更
- [ ] 🟢 Extract Button component styles
  - [ ] Create helper functions
  - [ ] Update component
  - [ ] Add unit tests

### Documentation Updates 文件更新
- [ ] Update API documentation
- [ ] Add code comments
- [ ] Update README
- [ ] Add CHANGELOG entry

### Testing 測試
- [ ] All existing tests pass
- [ ] New tests added
- [ ] Edge cases covered
- [ ] Performance verified
```

## Ready to Implement

I'm ready to help you implement code review suggestions. Please provide:

1. **Code Review Feedback**
    - Review markdown file
    - Specific issues to address
    - Priority level (if focusing on certain priorities)

2. **Current Code** (optional but helpful)
    - Files that need changes
    - Context about the project structure

3. **Implementation Preferences** (optional)
    - Specific approaches you prefer
    - Constraints or requirements
    - Testing framework being used

I'll generate:
- ✅ Complete, working code implementations
- ✅ Before/after comparisons
- ✅ Testing guidance
- ✅ Documentation updates
- ✅ Implementation checklists
- ✅ Bilingual explanations (English & 繁體中文)

Let's turn those code review suggestions into actual improvements!
