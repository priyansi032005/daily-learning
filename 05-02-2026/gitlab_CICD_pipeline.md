# GitLab CI/CD Pipeline Learning Notes

## Overview
GitLab CI/CD is a powerful tool for automating software development workflows, from code integration to deployment.

## Key Concepts

### Pipeline
- A pipeline is a collection of jobs that run in stages
- Defined in `.gitlab-ci.yml` file in the repository root
- Triggered automatically on commits, merge requests, or manually

### Jobs
- Individual tasks that run in a pipeline
- Can run scripts, tests, builds, or deployments
- Execute in isolated environments (containers)

### Stages
- Logical groupings of jobs that run sequentially
- Default stages: build, test, deploy
- Jobs in the same stage run in parallel

### Runners
- Agents that execute CI/CD jobs
- Can be shared (GitLab.com) or self-hosted
- Support different executors (Docker, Shell, Kubernetes)

## Basic .gitlab-ci.yml Structure

```yaml
stages:
  - build
  - test
  - deploy

variables:
  NODE_VERSION: "18"

build_job:
  stage: build
  image: node:$NODE_VERSION
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  image: node:$NODE_VERSION
  script:
    - npm install
    - npm run test
  coverage: '/Coverage: \d+\.\d+%/'

deploy_job:
  stage: deploy
  script:
    - echo "Deploying application"
  only:
    - main
```

## Important Keywords and Directives

### Job Control
- `only`: Specify when jobs should run (branches, tags, merge requests)
- `except`: Specify when jobs should NOT run
- `rules`: More flexible job control with conditions
- `when`: Control job execution (on_success, on_failure, manual, always)

### Artifacts
- `artifacts`: Files/directories to pass between jobs
- `expire_in`: How long to keep artifacts
- `reports`: Special artifacts for test results, coverage, etc.

### Caching
- `cache`: Store dependencies between pipeline runs
- `key`: Define cache scope (per branch, per job, etc.)
- `paths`: Directories to cache

### Environment Variables
- `variables`: Define custom variables
- Built-in variables: `CI_COMMIT_SHA`, `CI_PIPELINE_ID`, etc.
- Protected variables for sensitive data

## Advanced Features

### Parallel Jobs
```yaml
test:
  parallel: 3
  script:
    - npm run test -- --shard=$CI_NODE_INDEX/$CI_NODE_TOTAL
```

### Matrix Jobs
```yaml
test:
  parallel:
    matrix:
      - NODE_VERSION: ["16", "18", "20"]
        OS: ["ubuntu", "alpine"]
```

### Include External Files
```yaml
include:
  - local: '/templates/.gitlab-ci-template.yml'
  - project: 'group/project'
    file: '/templates/.gitlab-ci-template.yml'
  - remote: 'https://example.com/.gitlab-ci.yml'
```

### Conditional Logic with Rules
```yaml
deploy:
  script: echo "Deploy to production"
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
    - if: $CI_COMMIT_TAG
    - when: manual
      allow_failure: true
```

## Best Practices

### 1. Pipeline Efficiency
- Use caching for dependencies
- Minimize Docker image sizes
- Run jobs in parallel when possible
- Use artifacts sparingly

### 2. Security
- Use protected variables for secrets
- Limit job permissions with `only`/`rules`
- Scan for vulnerabilities in dependencies
- Use signed commits and protected branches

### 3. Maintainability
- Use templates and includes for reusability
- Keep jobs focused and single-purpose
- Use meaningful job names and descriptions
- Document complex pipeline logic

### 4. Testing Strategy
- Run fast tests early (unit tests)
- Use parallel testing for large test suites
- Generate test reports and coverage
- Fail fast on critical issues

## Common Patterns

### Multi-Environment Deployment
```yaml
deploy_staging:
  stage: deploy
  script: deploy.sh staging
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy_production:
  stage: deploy
  script: deploy.sh production
  environment:
    name: production
    url: https://example.com
  when: manual
  only:
    - main
```

### Docker Build and Push
```yaml
build_image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

### Code Quality Checks
```yaml
lint:
  stage: test
  script:
    - npm run lint
  allow_failure: true

security_scan:
  stage: test
  script:
    - npm audit
  allow_failure: false
```

## Troubleshooting Tips

### Common Issues
1. **Job failures**: Check logs, verify script syntax
2. **Artifact issues**: Ensure paths exist, check permissions
3. **Cache problems**: Clear cache, verify cache keys
4. **Runner issues**: Check runner availability and tags

### Debugging Strategies
- Use `echo` statements to debug scripts
- Enable debug mode with `CI_DEBUG_TRACE: "true"`
- Test locally with GitLab Runner
- Use `artifacts:reports:junit` for test visibility

## Useful GitLab CI/CD Variables

```yaml
variables:
  # Common built-in variables
  CI_COMMIT_SHA: # Current commit SHA
  CI_COMMIT_REF_NAME: # Branch or tag name
  CI_PIPELINE_ID: # Unique pipeline ID
  CI_JOB_NAME: # Current job name
  CI_REGISTRY_IMAGE: # Container registry image path
  CI_PROJECT_PATH: # Project path (group/project)
```

## Resources and References
- [GitLab CI/CD Documentation](https://docs.gitlab.com/ee/ci/)
- [GitLab CI/CD Examples](https://docs.gitlab.com/ee/ci/examples/)
- [GitLab Runner Documentation](https://docs.gitlab.com/runner/)
- [CI/CD Best Practices](https://docs.gitlab.com/ee/ci/pipelines/pipeline_efficiency.html)

---

*Last updated: February 5, 2026*