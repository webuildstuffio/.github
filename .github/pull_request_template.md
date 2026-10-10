## Description

<!-- Provide a brief description of the changes in this PR -->

## Type of Change

<!-- Mark the relevant option with an "x" -->

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Refactoring (code change that neither fixes a bug nor adds a feature)
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Test update

## Related Issues

<!-- Link to related issues/tickets -->

Closes #

## Changes Made

<!-- List the specific changes made in this PR -->

-
-
-

## Testing

<!-- Describe the tests you ran and how to reproduce them -->

- [ ] Unit tests pass (`bun run test`)
- [ ] Type checking passes (`bun run type-check`)
- [ ] Linting passes (`bun run lint`)
- [ ] Manual testing completed

### Test Coverage

<!-- Describe new/updated tests -->

-

## Screenshots (if applicable)

<!-- Add screenshots to help explain your changes -->

## Code Quality Checklist

- [ ] **No Hardcoded Colors**: All colors use Tailwind CSS variables (`bun run color-guardian`)
- [ ] **Z-Index Management**: Only approved z-index values from theme config
- [ ] **TypeScript Strict Mode**: No type errors or `any` types
- [ ] **Feature-Based Organization**: Code grouped by feature, not by type
- [ ] **File Naming Conventions**: Follow industry-standard mixed approach
  - [ ] React Components: PascalCase (`UserProfile.tsx`)
  - [ ] Utilities/Services: kebab-case (`transaction-filters.ts`)
  - [ ] Server Files: kebab-case (`investment-prices.ts`)
  - [ ] Tests: kebab-case with `.test.ts` suffix (`tax-calculations.test.ts`)
- [ ] **Error Handling**: Comprehensive error boundaries and user-friendly messages
- [ ] **Audit Trail**: Data modifications logged to `audit_log` table (if applicable)
- [ ] **SQL Safety**: Parameterized queries only (no string concatenation)
- [ ] **Transactions**: Multi-step DB operations wrapped in `BEGIN`/`COMMIT`

## Database/SQLite Checklist (if applicable)

**If this PR touches database schema, queries, or data operations:**

- [ ] **Schema Changes**: All monetary columns use `INTEGER` (not `REAL`/`FLOAT`/`DOUBLE`)
- [ ] **Foreign Keys**: `PRAGMA foreign_keys = ON` enforced for referential integrity
- [ ] **WAL Mode**: Journal mode set to WAL for better concurrency
- [ ] **Parameterized Queries**: All database access uses prepared statements
- [ ] **Transactions**: Multi-step operations wrapped in `BEGIN`/`COMMIT`
- [ ] **Index Optimization**: Proper indexes on frequently queried columns
- [ ] **Audit Logging**: Data modifications trigger `audit_log` entries
- [ ] **Migration Safety**: Schema changes are backward compatible
- [ ] **Test Database**: Test schemas match production exactly
- [ ] **Data Types**: Unix timestamps as integers, monetary values as cents

## Security Checklist (if applicable)

- [ ] **Input Validation**: All user inputs validated with Zod schemas
- [ ] **SQL Injection**: Parameterized queries used throughout
- [ ] **XSS Prevention**: User-generated content properly escaped
- [ ] **Authentication**: Protected routes have auth checks
- [ ] **Data Encryption**: Sensitive data encrypted (PII, financial data)
- [ ] **PIPEDA Compliance**: Canadian privacy requirements met

## Documentation

- [ ] Code is self-documenting with clear variable/function names
- [ ] Complex logic has explanatory comments
- [ ] README.md updated (if needed)
- [ ] API documentation updated (if API changes)
- [ ] Migration guide provided (if breaking changes)

## Pre-Merge Checklist

- [ ] All CI/CD checks pass
- [ ] No merge conflicts with `main`
- [ ] Branch is up-to-date with `main`
- [ ] Reviewed my own code for obvious errors
- [ ] Code follows project style guidelines
- [ ] Changes are backward compatible (or breaking changes are documented)

## Deployment Notes

<!-- Any special deployment considerations? Database migrations? Environment variables? -->

---

**Reviewer Notes**: Please pay special attention to:

<!-- Highlight specific areas that need extra review -->

-
