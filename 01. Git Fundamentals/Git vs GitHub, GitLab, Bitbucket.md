# Git vs GitHub, GitLab, Bitbucket

## The Fundamental Distinction

Git is a distributed version control system. It runs on your machine. It tracks changes, manages branches, and stores history locally. You can use Git without any network connection, without accounts, and without paying anyone. It is a tool.

GitHub, GitLab, and Bitbucket are hosting platforms. They provide servers that store Git repositories remotely. They add collaboration features like pull requests, issue tracking, and access controls. You cannot use these platforms without Git, but you can use Git without them.

Think of Git as the engine and these platforms as the car body, dashboard, and navigation system. The engine makes movement possible. The rest makes it usable for teams.

## GitHub

The largest Git hosting platform. Owned by Microsoft. Dominates open source projects due to network effects. Developers expect public projects to live on GitHub. Its interface has become the default mental model for how Git collaboration works.

Strengths include extensive third-party integrations, a massive marketplace of actions for CI/CD, and strong community support. Most tutorials and documentation assume GitHub terminology. Pull requests are called pull requests everywhere because GitHub popularized the term.

Weaknesses include limited built-in CI/CD compared to GitLab. GitHub Actions solves this but requires separate configuration. Enterprise features cost significantly more than competitors. The platform focuses on code hosting rather than end-to-end DevOps.

## GitLab

Positions itself as a complete DevOps platform. Includes Git repository hosting, CI/CD pipelines, container registry, monitoring, and project management in a single product. Available as SaaS or self-hosted. This flexibility appeals to organizations with strict data residency requirements.

The integrated CI/CD pipeline configuration lives in `.gitlab-ci.yml` within the repository. No separate service needed. Pipelines execute on GitLab's runners or your own infrastructure. This unified approach reduces context switching between tools.

Interface complexity grows with feature breadth. New users face steeper learning curves than GitHub. Smaller community means fewer third-party resources and templates. Self-hosting requires operational overhead that many teams underestimate.

## Bitbucket

Atlassian's Git hosting solution. Integrates tightly with Jira and Confluence. Teams already using Atlassian products find Bitbucket natural because issues, commits, and documentation exist in the same ecosystem. Cross-referencing Jira tickets in commit messages automatically links them.

Supports both Git and Mercurial historically, though Mercurial support ended in 2020. Focuses on enterprise customers rather than open source. Less visible in public developer communities. Pricing scales with user count which benefits small teams but becomes expensive at scale.

CI/CD through Bitbucket Pipelines offers simplicity for basic workflows but lacks the extensibility of GitHub Actions or GitLab CI. Fewer marketplace integrations than GitHub. Community support is smaller.

## Choosing Between Them

**Choose GitHub if**: You work on open source projects. Your team values ecosystem breadth over integrated features. You prefer best-of-breed tools connected through APIs rather than monolithic platforms. Most developers already have GitHub accounts reducing onboarding friction.

**Choose GitLab if**: You want a single platform for source control, CI/CD, and deployment. You need self-hosting options for compliance reasons. Your team prefers integrated workflows over assembling separate tools. You value having all DevOps capabilities from one vendor.

**Choose Bitbucket if**: Your organization heavily uses Jira and Confluence. You prioritize seamless integration between issue tracking and code review. You are an enterprise customer already invested in the Atlassian ecosystem. Open source visibility matters less than internal collaboration efficiency.

## Technical Reality

All three platforms use standard Git protocols. Cloning, pushing, pulling, and branching work identically regardless of where you host. The differences appear in collaboration workflows, automation capabilities, and administrative features. Switching platforms requires migrating repositories and reconfiguring CI/CD but does not change how Git itself functions.

Teams often maintain repositories on multiple platforms during transitions. Git's distributed nature makes this straightforward. Clone from one platform, add a second remote, push to both. Eventually consolidate once migration completes.
