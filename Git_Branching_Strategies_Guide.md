# Git Branching Strategies - DevOps Interview Guide

## Table of Contents
1. [Overview](#overview)
2. [Git Flow](#git-flow)
3. [GitHub Flow](#github-flow)
4. [GitLab Flow](#gitlab-flow)
5. [Trunk-Based Development](#trunk-based-development)
6. [Comparison Matrix](#comparison-matrix)
7. [Interview Tips](#interview-tips)

---

## Overview

Branching strategies are fundamental patterns that teams use to organize code changes, manage releases, and coordinate development work. Here are the 3 most popular strategies asked about in DevOps interviews:

### Quick Summary
- **Git Flow**: Complex, multiple long-lived branches, scheduled releases
- **GitHub Flow**: Simple, main + feature branches, continuous deployment
- **GitLab Flow**: Middle ground, environment-aware branching

---

## Git Flow

### Core Structure
Git Flow uses **5 types of branches**:
- **main/master**: Production-ready code only
- **develop**: Integration branch for ongoing development
- **feature/***: Individual feature development
- **release/***: Preparing and stabilizing releases
- **hotfix/***: Emergency fixes for production issues

### Workflow Steps

#### 1. Feature Development
```bash
# Start from develop
git checkout develop
git pull origin develop

# Create feature branch
git checkout -b feature/user-profile-page

# Work on feature (multiple commits)
git commit -m "Add profile component"
git commit -m "Add profile editing functionality"
git commit -m "Add profile image upload"

# Merge back to develop
git checkout develop
git merge feature/user-profile-page
git branch -d feature/user-profile-page
```

#### 2. Release Process
```bash
# Create release branch from develop
git checkout develop
git checkout -b release/v1.2.0

# Bug fixes and final tweaks on release branch
git commit -m "Fix styling issues for mobile"
git commit -m "Update version numbers"

# Merge to main
git checkout main
git merge release/v1.2.0
git tag v1.2.0

# Merge back to develop
git checkout develop
git merge release/v1.2.0
git branch -d release/v1.2.0
```

#### 3. Hotfix Process
```bash
# Emergency fix from main
git checkout main
git checkout -b hotfix/critical-security-fix

# Quick fix
git commit -m "Fix SQL injection vulnerability"

# Merge to main
git checkout main
git merge hotfix/critical-security-fix
git tag v1.2.1

# Merge to develop
git checkout develop
git merge hotfix/critical-security-fix
git branch -d hotfix/critical-security-fix
```

### Real-World Example: E-commerce Platform

**Scenario**: Version 2.0 Release Cycle

**Month 1-2: Feature Development**
```bash
# Team develops features in parallel
git checkout -b feature/shopping-cart    # Developer A
git checkout -b feature/payment-gateway  # Developer B
git checkout -b feature/order-tracking   # Developer C

# Each feature merges to develop when complete
git checkout develop
git merge feature/shopping-cart
git merge feature/payment-gateway
git merge feature/order-tracking
```

**Month 3: Release Preparation**
```bash
# Create release branch
git checkout -b release/v2.0.0

# QA finds bugs, developers fix on release branch
git commit -m "Fix cart calculation bug"
git commit -m "Improve payment error handling"
git commit -m "Add missing validation"
```

**Release Day**
```bash
# Deploy release to production
git checkout main
git merge release/v2.0.0
git tag v2.0.0

# Sync fixes back to develop
git checkout develop
git merge release/v2.0.0
```

### Branch Lifecycle Timeline
```
main:     v1.0 -------- v1.1 -- v2.0 - v2.0.1
                         |       |       |
release:                 |   rel/v2.0 ---|
                         |       |
develop:  -------- features ---- |-- new features --
                         |
feature:     feat/cart --
             feat/pay -----
             feat/track ----
```

### When Git Flow Works Best
- Large teams (10+ developers)
- Scheduled releases (quarterly, monthly)
- Enterprise applications with strict quality gates
- Products requiring extensive testing cycles
- Teams that need feature isolation

- In Git Flow:

•  main: Production-ready code only. Tagged with version numbers. Receives merges only from release branches and hotfix branches. Should always be stable and deployable.
•  develop: Integration branch for features. Contains the latest development changes. Used as the base for feature branches and receives completed features via pull requests. May be less stable than main.

The key difference: main is for releases, develop is for active development. Features are merged into develop, and when ready for release, develop is merged into main via a release branch.

---

## GitHub Flow

### Core Principles
- **Single main branch**: `main` is always deployable
- **Short-lived feature branches**: Work happens in temporary branches
- **Pull request workflow**: All changes reviewed before merging
- **Deploy immediately**: After merge, deploy to production

### Step-by-Step Process

#### 1. Create Feature Branch
```bash
git checkout main
git pull origin main
git checkout -b feature/add-user-authentication
```

#### 2. Develop Feature
```bash
git add .
git commit -m "Add login form component"
git commit -m "Implement password validation"
git commit -m "Add authentication tests"
```

#### 3. Push and Create Pull Request
```bash
git push origin feature/add-user-authentication
```
Then create PR on GitHub targeting `main`.

#### 4. Code Review
Team reviews the PR:
- Automated tests run
- Peer code review
- Discussions and feedback
- Request changes if needed

#### 5. Merge to Main
Once approved:
- Merge PR (squash, merge commit, or rebase)
- Feature branch gets deleted automatically

#### 6. Deploy
Deploy `main` immediately to production.

### Real-World Example: E-commerce Website

**Scenario**: Adding shopping cart functionality

1. **Branch Creation**
   ```bash
   git checkout -b feature/shopping-cart
   ```

2. **Development** (over 2-3 days)
   - Day 1: Create cart component
   - Day 2: Add/remove items functionality  
   - Day 3: Persist cart in localStorage

3. **Pull Request**
   - Title: "Add shopping cart functionality"
   - Description includes screenshots, testing notes
   - Automated tests verify cart operations

4. **Review Process**
   - Senior dev reviews code structure
   - QA team tests on staging environment
   - Security review for data handling

5. **Merge & Deploy**
   - PR approved and merged Friday 2pm
   - Automated deployment to production within 10 minutes
   - Users see new shopping cart feature live

### When GitHub Flow Works Best
- Web applications with continuous deployment
- Teams comfortable with frequent releases
- Good automated testing coverage
- Strong code review culture

---

## GitLab Flow

### Core Concept
Combines Git Flow simplicity with environment-specific branches.

### Structure
- **main**: Development integration
- **production**: What's currently live
- **pre-production/staging**: Testing environment
- **feature/***: Individual changes

### Flow
Features merge to main → main promotes to pre-production → pre-production promotes to production.

### When GitLab Flow Works Best
- Teams needing multiple environments
- Regulated industries
- Staged deployments

---

## Trunk-Based Development

### Core Concept
Developers commit directly to main/trunk or use very short-lived branches (hours, max 1 day).

### Key Characteristics
- **Branch Lifespan**: Hours (or none)
- **Commits to Main**: Direct or very frequent
- **Integration Frequency**: Multiple times per day
- **Code Reviews**: Post-merge or during development
- **Feature Flags**: Essential for incomplete features

### Example
```bash
# Developer commits directly to main multiple times daily
git checkout main
git pull
# ... make small change
git commit -m "Add validation to email field"
git push origin main

# Later same day
git pull
# ... another small change
git commit -m "Update error message styling"
git push origin main
```

### When Trunk-Based Works Best
- High-performing teams with strong practices
- Need rapid deployment cycles
- Strong automated testing pipeline
- Feature flags infrastructure exists

---

## Comparison Matrix

| Aspect | Git Flow | GitHub Flow | GitLab Flow | Trunk-Based |
|--------|----------|-------------|-------------|-------------|
| **Complexity** | High | Low | Medium | Low |
| **Branch Types** | 5 types | 2 types | 3-4 types | 1-2 types |
| **Release Frequency** | Scheduled | Continuous | Staged | Continuous |
| **Team Size** | Large (10+) | Small-Medium | Medium | Any |
| **Integration Speed** | Slow | Medium | Medium | Fast |
| **Feature Isolation** | High | Medium | Medium | Low |
| **Emergency Fixes** | Hotfix branch | Feature branch | Direct to prod | Direct to main |
| **Code Review** | Optional | Mandatory (PR) | Mandatory (MR) | Optional |
| **Best For** | Enterprise, scheduled releases | Web apps, CD | Multi-environment | High-velocity teams |

---

## GitHub Flow vs Trunk-Based Development

### Key Differences

#### Branch Strategy
- **GitHub Flow**: Uses feature branches that live for days/weeks
- **Trunk-Based**: Developers commit directly to main/trunk or use very short-lived branches (hours, max 1 day)

#### Code Integration
- **GitHub Flow**: Integration happens when PR is merged
- **Trunk-Based**: Continuous integration - code merged multiple times daily

#### Review Process
- **GitHub Flow**: Mandatory pull request reviews before merge
- **Trunk-Based**: Reviews can be post-commit or pair programming during development

### When to Use Each

#### GitHub Flow is Better When:
- Need thorough code reviews
- Complex features requiring isolation
- Junior developers on team
- Less frequent deployments acceptable

#### Trunk-Based is Better When:
- High-performing teams with strong practices
- Need rapid deployment cycles
- Strong automated testing pipeline
- Feature flags infrastructure exists

---

## Interview Tips

### Key Points to Remember
1. **Each strategy balances complexity vs. control**
   - Git Flow: Most control, highest complexity
   - GitHub Flow: Simplest, least control
   - GitLab Flow: Middle ground with environment awareness
   - Trunk-Based: Fastest integration, requires discipline

2. **Context Matters**
   - Team size and experience level
   - Release frequency requirements
   - Industry regulations
   - Available tooling and infrastructure

3. **Common Interview Questions**
   - "When would you choose Git Flow over GitHub Flow?"
   - "How do you handle hotfixes in each strategy?"
   - "What are the trade-offs of trunk-based development?"
   - "How do code reviews work in each model?"

4. **Practical Examples**
   - Be ready to draw branch diagrams
   - Explain command sequences for common scenarios
   - Discuss real-world challenges and solutions

### Red Flags to Avoid
- Claiming one strategy is always best
- Not considering team context
- Ignoring deployment requirements
- Overlooking code review importance

---

## Conclusion

The choice of branching strategy should align with:
- **Team maturity and size**
- **Release cadence requirements**
- **Quality assurance needs**
- **Deployment infrastructure**
- **Risk tolerance**


Understanding these strategies and their trade-offs demonstrates DevOps maturity and practical git knowledge essential for modern software development teams.
