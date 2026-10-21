# Pull Request & Code Review Guideline

Before creating a PR, ensure following this guideline:

[Github Repository Guideline](/doc/554fde1d-0a79-405a-b9cb-b82a1e1ec880)

## 1. Purpose

Pull requests (PRs) and code reviews ensure that all changes meet the project’s quality standards and align with the codebase’s design and architecture. The review process aims to:

* Maintain **high code quality and readability**
* Detect **bugs and edge cases**
* Enforce **consistent coding style**
* Encourage **knowledge sharing and collaboration** within the team

## 2. Contribution Workflow

The typical development workflow follows a **branch / pull request model**.

### Standard Workflow

1. **Create or identify an issue**
   * Discuss the problem or feature with maintainers before implementation.
   * Ensure the change aligns with project direction.
2. **Create a branch**
   * Work in a dedicated feature branch.
3. **Implement the solution**
   * Follow coding standards.
   * Add necessary tests and documentation.
4. **Test the changes**
   * Run unit tests, integration tests, and linters.
   * Verify the feature works as expected.
5. **Create a Pull Request**
   * Provide a clear description and context.
6. **Review process**
   * Reviewers provide feedback.
   * Author updates the PR.
7. **Approval and merge**
   * After approval and passing CI checks, the PR is merged.

## 3. Creating a Good Pull Request

### 3.1 Communicate Early

Before starting development:

* Comment on the related issue
* Confirm the implementation approach with maintainers
* Avoid duplicate work or misaligned solutions

### 3.2 Keep Pull Requests Small

Small PRs are easier to review and safer to merge.

Guidelines:

* Solve **one problem per PR**
* Avoid mixing unrelated changes
* Prefer multiple small PRs over one large PR
* Target **manageable change size** (often <500 lines if possible)

Benefits:

* Faster reviews
* Easier rollback
* Lower merge risk

To help you manage multiple small PRs, you can use **Stacked Pull Request** technique.

Read more:

* <https://graphite.com/blog/stacked-prs>
* <https://tinyideasxyz.medium.com/daisy-chaining-pull-requests-0d5a41a87b23>
* How to: <https://medium.com/inside-bukalapak/smaller-faster-code-review-with-stacked-merge-request-1dc130705bc1>

### 3.3 Pull Request Structure

Each PR should include:

#### Required

* Include ticket id in PR title
* Description explaining the change
* Linked issue
* Tests and documentation when applicable
* Env variable or production config changes needed before deployment
* Passing CI checks

#### Recommended

* Screenshots for UI changes
* Performance comparison (if relevant)
* Migration instructions for breaking changes

### 3.4 Provide a Clear PR Description

Every PR should explain:

* **Problem / Motivation**
* **Solution implemented**
* **How the change was tested**
* **Impact or breaking changes**
* **Anything reviewers should focus on**

Example structure:

```markdown
## Summary
Brief description of the change.

## Problem
What issue or limitation this PR addresses.

## Solution
How the change solves the problem.

## Test Evidence
Add a simple screenshot or video recording, covering normal flow and some edge cases. 

## Deployment Prerequisite / Breaking Changes
Mention env changes, DB migration, deleted function, etc

## Notes
Additional context for reviewers.
```

A clear description helps reviewers understand the change without deeply analyzing the entire codebase.

## 4. Review Process

The review process typically follows these stages:

1. **Reviewer assignment**
2. **Feedback**
3. **Discussion**
4. **Revision**
5. **Repeat until approved**
6. **Merge**

This iterative cycle ensures improvements through collaboration.

## 5. Code Review Guidelines

### 5.1 Goals of Code Review

Reviewers focus on:

* Correctness
* Maintainability
* Performance
* Security
* Consistency with architecture

The objective is **improving the code**, not criticizing the author.

### 5.2 What Reviewers Should Check

#### Correctness

* Does the code solve the problem?
* Are edge cases handled?

#### Readability

* Is the code easy to understand?
* Are functions too large or complex?

#### Architecture

* Does the change fit the system design?
* Does it introduce unnecessary coupling?

#### Testing

* Are tests present and meaningful?

#### Documentation

* Are comments or docs updated when behavior changes?

### 5.3 Reviewer Best Practices

Reviewers should:

* Be respectful and constructive
* Explain reasoning behind feedback
* Distinguish between:
  * **Required changes**
  * **Optional suggestions (nit)**

If something is unclear, ask questions instead of assuming.

## 6. Responding to Review Feedback

Authors should:

* Address every review comment
* Ask for clarification when needed
* Explain reasoning when disagreeing
* Avoid blindly applying fixes without understanding

After updates:

* Re-request review
* Highlight important changes

Review discussions should remain collaborative.

## 7. CI and Merge Requirements

Before merging, ensure:

* All CI checks pass
* All discussions are resolved
* Required reviewers approve
* Commit messages follow guidelines

If CI fails, the PR must be updated before merging.

## Best Practices Summary

### Authors

* Communicate early
* Keep PRs small
* Write clear descriptions
* Follow commit conventions
* Test thoroughly

### Reviewers

* Focus on improvement
* Give clear actionable feedback
* Avoid blocking on trivial issues
* Encourage maintainable code

## Read More

* <https://www.swarmia.com/blog/a-complete-guide-to-code-reviews>
* <https://google.github.io/eng-practices/review/reviewer/looking-for.html>
