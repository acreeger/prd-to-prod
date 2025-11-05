# Agent Execution Guide

> **Purpose**: Instructions for AI agents to autonomously execute GitHub issues from GITHUB_ISSUE_BREAKDOWN.md
> **Audience**: AI coding agents (Claude Code, Gemini Code Assist, GitHub Copilot)
> **Last Updated**: 2025-11-05

---

## Quick Start

```bash
# 1. Read the issue breakdown
cat docs/FromProd/GITHUB_ISSUE_BREAKDOWN.md

# 2. Start with Issue #1 (MS-01.01: Project Setup)
# 3. Follow implementation steps sequentially
# 4. Verify all success criteria before moving to next issue
# 5. Check dependencies before starting each issue
```

---

## Issue Structure Overview

Each issue in `GITHUB_ISSUE_BREAKDOWN.md` follows this proven template:

### 1. Metadata
- **Milestone ID** (e.g., MS-01.01)
- **Dependencies** (blocking issues)
- **Complexity** (Low/Medium/High)
- **Labels** (for GitHub issue creation)

### 2. Objective
Single-sentence goal stating what will be built.

### 3. Required Tools & Packages
- ✅ **Tools to USE** - With justification
- ❌ **Tools to AVOID** - With reasons why

### 4. Implementation Steps
1. Sequential, atomic steps
2. Each step includes exact commands
3. Verification commands after each step

### 5. Success Criteria
Checklist of requirements that MUST pass:
- [ ] Testable criterion 1
- [ ] Testable criterion 2
- [ ] ...

### 6. Anti-Patterns to Avoid
Explicit list of common mistakes:
- ❌ Pattern (why it's wrong)

### 7. Environment Variables
Exact format and values needed.

### 8. Testing Commands
Commands to verify success at each stage.

### 9. Reference Files
Files created/modified in this issue.

---

## Execution Flow

### Phase 1: Foundation (No Database)

```
┌─────────────────────────────────────────────────────────────┐
│                    Issue #1: MS-01.01                       │
│                    Project Setup                            │
│                    (No dependencies)                        │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                    Issue #2: MS-01.02                       │
│                    Google Maps Integration                  │
│                    (Depends on #1)                          │
└──────────┬────────────────────────────────────┬─────────────┘
           │                                    │
           ▼                                    ▼
┌──────────────────────────┐     ┌─────────────────────────────┐
│   Issue #3: MS-01.03     │     │   Issue #4: MS-01.04        │
│   MARTA Transit Overlay  │     │   Event & Venue Data        │
│   (Depends on #2)        │     │   (Depends on #2)           │
└──────────────────────────┘     └──────────┬──────────────────┘
                                             │
                                             ▼
                                  ┌─────────────────────────────┐
                                  │   Issue #5: MS-02.01        │
                                  │   Bilingual Support         │
                                  │   (Depends on #1)           │
                                  └──────────┬──────────────────┘
                                             │
                                             ▼
                                  ┌─────────────────────────────┐
                                  │   Issue #6: MS-02.02        │
                                  │   User Favorites            │
                                  │   (Depends on #4)           │
                                  └──────────┬──────────────────┘
                                             │
           ┌─────────────────────────────────┴──────────────────┐
           │                  All Phase 1 Complete              │
           └─────────────────────────┬──────────────────────────┘
                                     │
                                     ▼
                          ┌─────────────────────────┐
                          │   Issue #7: MS-03.01    │
                          │   Vercel Deployment     │
                          │   (Depends on #1-6)     │
                          └─────────┬───────────────┘
                                    │
                                    │
════════════════════════════════════╪════════════════════════════
                Phase 1 / Phase 2 Boundary
════════════════════════════════════╪════════════════════════════
                                    │
                                    ▼
                          ┌─────────────────────────┐
                          │   Issue #8: MS-03.01-P2 │
                          │   Database Integration  │
                          │   (Depends on #7)       │
                          │   ** Phase 2 Only **    │
                          └─────────────────────────┘
```

---

## Critical Paths

### Minimum Viable Product (MVP)
Fastest path to working deployment:
```
#1 → #2 → #7
(Setup → Maps → Deploy)
```

### Full Phase 1 (Recommended)
Complete feature set without database:
```
#1 → #2 → #3 → #4 → #5 → #6 → #7
```

### Phase 2 (Database Migration)
After Phase 1 proves concept:
```
#7 → #8
```

---

## Agent Execution Protocol

### Before Starting Any Issue

1. **Read entire issue** - Don't just scan
2. **Check dependencies** - All blocking issues must be DONE
3. **Understand objective** - Can you state it in one sentence?
4. **Review anti-patterns** - What NOT to do?
5. **Verify tools available** - Do you have access to required tools?

### During Execution

1. **Follow steps sequentially** - Don't skip or reorder
2. **Run verification commands** - After each step
3. **Check for errors** - Read all output carefully
4. **Document blockers** - If stuck, note exact error and context
5. **Don't improvise** - If instructions unclear, ask for clarification

### After Completion

1. **Verify ALL success criteria** - No exceptions
2. **Run full test suite** - Use testing commands
3. **Check anti-patterns** - Did you avoid them all?
4. **Review reference files** - Are they all present?
5. **Mark issue complete** - Only when 100% done

---

## Common Anti-Patterns Across All Issues

These appear in multiple issues - memorize them:

### ❌ NEVER Do These
1. **Install react-spring** - Always use Framer Motion
2. **Install database packages in Phase 1** - Wait until Issue #8
3. **Install next-i18next** - Use Next.js built-in i18n
4. **Use npm or yarn** - This project requires pnpm
5. **Commit .env files** - Only commit .env.example
6. **Hardcode API keys** - Always use environment variables
7. **Use custom CSS files** - Stick to Tailwind utilities
8. **Skip error handling** - Always add try/catch
9. **Use older Google Maps libraries** - Only @vis.gl/react-google-maps
10. **Create custom webpack config** - Use Next.js defaults

### ✅ ALWAYS Do These
1. **Use TypeScript** - For everything
2. **Use Tailwind CSS** - For all styling
3. **Use pnpm** - For package management
4. **Verify build locally** - Before deploying
5. **Read environment variables** - Never hardcode
6. **Follow Next.js 16 patterns** - App Router, not Pages Router
7. **Test on mobile** - Responsive design required
8. **Check console for errors** - Zero errors policy
9. **Update translations** - For both EN and ES
10. **Document as you go** - Comments and types

---

## Dependency Matrix

| Issue | Blocks Issues | Blocked By |
|-------|---------------|------------|
| #1: Setup | #2, #5 | None |
| #2: Maps | #3, #4 | #1 |
| #3: Transit | #7 | #2 |
| #4: Events | #6, #7 | #2 |
| #5: i18n | #7 | #1 |
| #6: Favorites | #7 | #4 |
| #7: Deploy | #8 | #1-6 |
| #8: Database | None | #7 |

---

## Verification Checklist

Use this after completing each issue:

### Code Quality
- [ ] No TypeScript errors (`pnpm tsc --noEmit`)
- [ ] No ESLint errors (`pnpm lint`)
- [ ] Build succeeds (`pnpm build`)
- [ ] Dev server runs (`pnpm dev`)

### Functionality
- [ ] All success criteria met (from issue)
- [ ] Manual testing passed (browser verification)
- [ ] Mobile responsive (test on small viewport)
- [ ] No console errors (check DevTools)

### Best Practices
- [ ] No anti-patterns used (checked against list)
- [ ] Environment variables used (no hardcoded values)
- [ ] Translations updated (EN + ES)
- [ ] Types defined (TypeScript interfaces)

### Documentation
- [ ] Comments added for complex logic
- [ ] Reference files created/updated
- [ ] README updated if needed
- [ ] Issue marked complete in tracker

---

## Error Recovery

### If Build Fails

```bash
# 1. Clean everything
rm -rf node_modules .next .vercel

# 2. Fresh install
pnpm install

# 3. Verify environment variables
cat .env.local

# 4. Try build again
pnpm build
```

### If Dependencies Conflict

```bash
# 1. Check for wrong packages
pnpm list react-spring  # Should NOT exist
pnpm list next-i18next  # Should NOT exist

# 2. Remove if found
pnpm remove react-spring next-i18next

# 3. Install correct packages
pnpm add framer-motion
```

### If Deployment Fails

```bash
# 1. Verify build locally
pnpm build

# 2. Check environment variables
vercel env ls

# 3. Redeploy
vercel --prod
```

---

## Parallel Execution

Some issues can be executed in parallel:

### Parallelizable After Issue #2
```
Issue #3 (Transit) || Issue #4 (Events)
```
These don't depend on each other, both depend on #2.

### Must Be Sequential
```
#1 → #2 (Setup must finish before Maps)
#4 → #6 (Events must finish before Favorites)
#1-6 → #7 (All Phase 1 must finish before Deploy)
```

---

## Tech Stack Reference

Quick reference for tech decisions:

### Core Stack (All Milestones)
- **Framework**: Next.js 16 (App Router)
- **Language**: TypeScript 5
- **Styling**: Tailwind CSS 3
- **Package Manager**: pnpm 9+

### Feature-Specific
- **Maps**: @vis.gl/react-google-maps
- **Animation**: Framer Motion (NOT react-spring)
- **Data Fetching**: SWR
- **i18n**: Next.js built-in (NOT next-i18next)
- **Storage**: localStorage (Phase 1), Postgres (Phase 2)

### Deployment
- **Platform**: Vercel
- **CI/CD**: GitHub integration (auto-deploy)
- **Database**: Vercel Postgres (Phase 2 only)

---

## Success Metrics

### Per-Issue Metrics
- **Time to Complete**: Track for planning
- **Errors Encountered**: Document for learning
- **Success Criteria Pass Rate**: Should be 100%
- **Anti-Patterns Avoided**: Should be 100%

### Phase 1 Completion
- All 7 issues completed
- Zero TypeScript errors
- Zero console errors in production
- Mobile responsive
- Deployed to Vercel
- All features working

### Phase 2 Completion
- Database integrated
- Data migrated from localStorage
- No performance regression
- Multi-device sync working

---

## Quick Command Reference

### Setup
```bash
pnpm install              # Install dependencies
pnpm dev                  # Start dev server
pnpm build                # Build for production
pnpm start                # Start production server
```

### Testing
```bash
pnpm tsc --noEmit        # Check TypeScript
pnpm lint                 # Lint code
curl http://localhost:3000/api/events  # Test API
```

### Deployment
```bash
vercel login             # Login to Vercel
vercel                   # Deploy preview
vercel --prod            # Deploy production
vercel logs              # View logs
```

### Database (Phase 2)
```bash
pnpm db:generate         # Generate Prisma Client
pnpm db:migrate          # Run migrations
pnpm db:seed             # Seed database
pnpm db:studio           # Open Prisma Studio
```

---

## Troubleshooting Guide

### "Module not found: @vis.gl/react-google-maps"
```bash
pnpm add @vis.gl/react-google-maps
```

### "Invalid API key" in Google Maps
1. Check `NEXT_PUBLIC_GMAK` environment variable
2. Verify API key in Google Cloud Console
3. Enable Maps JavaScript API

### "localStorage is not defined"
- localStorage only works client-side
- Add `'use client'` directive to component
- Use `useEffect` for localStorage access

### "Build failed" on Vercel
1. Verify local build: `pnpm build`
2. Check environment variables in Vercel
3. Review build logs in Vercel dashboard

### "MARTA API not responding"
1. Verify API keys in environment variables
2. Check MARTA API status
3. Review API route logs

---

## Next Steps After Completion

### Phase 1 Complete
- [ ] User acceptance testing
- [ ] Performance optimization
- [ ] SEO optimization
- [ ] Analytics integration
- [ ] Error monitoring (Sentry)

### Phase 2 Planning
- [ ] Provision Vercel Postgres
- [ ] Plan data migration
- [ ] Design database schema
- [ ] Test connection pooling
- [ ] Backup strategy

### Future Enhancements
- [ ] Authentication (Auth.js)
- [ ] Push notifications
- [ ] Offline support (PWA)
- [ ] Real-time updates (WebSockets)
- [ ] Social sharing

---

## Resources

### Documentation
- [Next.js 16 Docs](https://nextjs.org/docs)
- [@vis.gl/react-google-maps](https://visgl.github.io/react-google-maps/)
- [Framer Motion Docs](https://www.framer.com/motion/)
- [SWR Documentation](https://swr.vercel.app/)
- [Vercel Documentation](https://vercel.com/docs)

### APIs
- [MARTA API Docs](http://www.itsmarta.com/app-developer-resources.aspx)
- [Google Maps API](https://developers.google.com/maps)
- [GTFS Realtime](https://gtfs.org/realtime/)

### Tools
- [Prisma Docs](https://www.prisma.io/docs) (Phase 2)
- [Vercel Postgres](https://vercel.com/docs/storage/vercel-postgres) (Phase 2)

---

## Contact & Support

For issues with this guide or execution:
1. Check troubleshooting section first
2. Review anti-patterns list
3. Consult GEMINI_MILESTONE_TOOLS.md source
4. Open GitHub issue with:
   - Which issue you were executing
   - What step failed
   - Exact error message
   - Environment details

---

**Document**: Agent Execution Guide
**Created**: 2025-11-05
**Version**: 1.0.0
**Next Review**: After first agent completes Phase 1
