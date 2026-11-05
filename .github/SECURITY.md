# 🔐 Security & Governance

**Status:** Ready for activation ✅

## Branch Protection Rules

### Protected Branches

- `main` - Production branch
- `develop` - Integration branch

### Requirements

1. Pull request review required (1+ reviewer via CODEOWNERS)
2. All status checks passing (CI/CD)
3. Branches up to date before merge
4. Force pushes blocked
5. Branch deletions blocked
6. Stale pull request dismissal enabled

## Quality Checks

### Pre-commit (Local)

- ESLint with TypeScript support
- Prettier formatting
- **Status:** ✅ Active

### CI/CD (GitHub Actions)

- Lint check
- Format check
- Build check
- **Workflow:** `.github/workflows/ci.yml`
- **Status:** ✅ Active

## Code Owners

- Path: `.github/CODEOWNERS`
- Default owner: @OUSMANE-SACKO
- Auto-request reviews on all PRs

## Teams

Configure in organization settings:

- `tech-leads` - Architecture & reviews
- `data` - Data pipelines
- `product` - Features & specs
- `evaluation` - Assessment

## Workflow

1. Create feature branch from `develop`
2. Make changes (pre-commit hook runs automatically)
3. Push to remote
4. Open PR (template auto-fills, CODEOWNERS auto-assigned)
5. Pass all CI checks
6. Get approval from CODEOWNERS
7. Merge to `develop`
8. Periodically merge `develop` → `main`

## What's Blocked

| Action                           | Blocked | Reason                      |
| -------------------------------- | ------- | --------------------------- |
| Direct push to `main`/`develop`  | ✅ YES  | Ruleset: PR required        |
| Commit with lint errors          | ✅ YES  | Pre-commit hook             |
| Merge PR with CI failures        | ✅ YES  | Ruleset: Checks required    |
| Force push to protected branches | ✅ YES  | Ruleset: Force push blocked |
| Delete protected branches        | ✅ YES  | Ruleset: Deletion blocked   |

## Testing the Protections

### Test 1: Direct Push

```bash
git checkout -b test/direct
echo "test" >> test.txt
git add test.txt
git commit -m "test"
git push origin develop
# Expected: ❌ REJECTED
```

### Test 2: Bad Formatting

```bash
git checkout -b test/bad-fmt
# Write code without formatting
git commit -m "unformatted"
# Expected: ❌ REJECTED by pre-commit hook
```

### Test 3: Failed CI Check

```bash
git checkout -b test/ci-fail
# Add ESLint violation (e.g., `var x = 1;`)
git commit -m "feat: bad code"
git push origin test/ci-fail
# Open PR → CI check will be RED
# Expected: ❌ Cannot merge
```

## References

- [GitHub Branch Protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches)
- [GitHub Rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets)
- [CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)
