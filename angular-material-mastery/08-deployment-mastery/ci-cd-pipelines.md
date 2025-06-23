# CI/CD Pipeline Mastery for Angular Material

## 🔄 **Enterprise CI/CD Architecture**

This guide provides comprehensive CI/CD patterns for Angular Material applications, covering everything from basic automation to enterprise-grade deployment orchestration.

## 📊 **Pipeline Architecture Overview**

```mermaid
graph TB
    subgraph "Source Control"
        A[Feature Branch] --> B[Pull Request]
        B --> C[Code Review]
        C --> D[Merge to Main]
    end
    
    subgraph "Continuous Integration"
        E[Code Checkout] --> F[Dependency Installation]
        F --> G[Code Quality Checks]
        G --> H[Unit Testing]
        H --> I[Integration Testing]
        I --> J[Security Scanning]
        J --> K[Build Optimization]
        K --> L[Artifact Creation]
    end
    
    subgraph "Continuous Deployment"
        M[Environment Promotion] --> N[Infrastructure Provisioning]
        N --> O[Application Deployment]
        O --> P[Health Checks]
        P --> Q[Smoke Testing]
        Q --> R[Traffic Routing]
    end
    
    subgraph "Monitoring & Feedback"
        S[Performance Monitoring] --> T[Error Tracking]
        T --> U[User Analytics]
        U --> V[Rollback Triggers]
    end
    
    D --> E
    L --> M
    R --> S
    V --> M
    
    style A fill:#e3f2fd
    style E fill:#e8f5e8
    style M fill:#fff3e0
    style S fill:#f3e5f5
```

## 🏗️ **Advanced GitHub Actions Workflows**

### **Multi-Environment Deployment Pipeline**
```yaml
# .github/workflows/advanced-deploy.yml
name: Advanced Angular Material Deployment Pipeline

on:
  push:
    branches: [ main, develop, 'release/*', 'hotfix/*' ]
  pull_request:
    branches: [ main, develop ]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        default: 'staging'
        type: choice
        options:
        - staging
        - production
      skip_tests:
        description: 'Skip test execution'
        required: false
        default: false
        type: boolean

env:
  NODE_VERSION: '18'
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  setup:
    runs-on: ubuntu-latest
    outputs:
      environment: ${{ steps.determine-env.outputs.environment }}
      version: ${{ steps.version.outputs.version }}
      should-deploy: ${{ steps.determine-env.outputs.should-deploy }}
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      with:
        fetch-depth: 0
        
    - name: Determine environment
      id: determine-env
      run: |
        if [[ "${{ github.event_name }}" == "workflow_dispatch" ]]; then
          echo "environment=${{ github.event.inputs.environment }}" >> $GITHUB_OUTPUT
          echo "should-deploy=true" >> $GITHUB_OUTPUT
        elif [[ "${{ github.ref }}" == "refs/heads/main" ]]; then
          echo "environment=production" >> $GITHUB_OUTPUT
          echo "should-deploy=true" >> $GITHUB_OUTPUT
        elif [[ "${{ github.ref }}" == "refs/heads/develop" ]]; then
          echo "environment=staging" >> $GITHUB_OUTPUT
          echo "should-deploy=true" >> $GITHUB_OUTPUT
        elif [[ "${{ github.ref }}" == refs/heads/release/* ]]; then
          echo "environment=staging" >> $GITHUB_OUTPUT
          echo "should-deploy=true" >> $GITHUB_OUTPUT
        else
          echo "environment=none" >> $GITHUB_OUTPUT
          echo "should-deploy=false" >> $GITHUB_OUTPUT
        fi
        
    - name: Generate version
      id: version
      run: |
        if [[ "${{ github.ref }}" == "refs/heads/main" ]]; then
          VERSION=$(git describe --tags --always)
        else
          VERSION="${{ github.ref_name }}-$(git rev-parse --short HEAD)"
        fi
        echo "version=$VERSION" >> $GITHUB_OUTPUT

  code-quality:
    runs-on: ubuntu-latest
    needs: setup
    if: ${{ !github.event.inputs.skip_tests }}
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run ESLint
      run: npm run lint -- --format=json --output-file=eslint-report.json
      continue-on-error: true
      
    - name: Run Prettier check
      run: npm run format:check
      
    - name: Run TypeScript compiler check
      run: npm run type-check
      
    - name: Upload ESLint report
      uses: actions/upload-artifact@v3
      with:
        name: eslint-report
        path: eslint-report.json

  security-scan:
    runs-on: ubuntu-latest
    needs: setup
    if: ${{ !github.event.inputs.skip_tests }}
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run npm audit
      run: npm audit --audit-level moderate
      
    - name: Run Snyk security scan
      uses: snyk/actions/node@master
      env:
        SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      with:
        args: --severity-threshold=medium
        
    - name: Run CodeQL analysis
      uses: github/codeql-action/init@v2
      with:
        languages: javascript
        
    - name: Perform CodeQL analysis
      uses: github/codeql-action/analyze@v2

  unit-tests:
    runs-on: ubuntu-latest
    needs: [setup, code-quality]
    if: ${{ !github.event.inputs.skip_tests }}
    
    strategy:
      matrix:
        node-version: [16, 18, 20]
        
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run unit tests
      run: npm run test:ci
      
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage/lcov.info
        name: node-${{ matrix.node-version }}

  integration-tests:
    runs-on: ubuntu-latest
    needs: [setup, code-quality]
    if: ${{ !github.event.inputs.skip_tests }}
    
    services:
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
      postgres:
        image: postgres:15-alpine
        env:
          POSTGRES_PASSWORD: postgres
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run integration tests
      run: npm run test:integration
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
        REDIS_URL: redis://localhost:6379

  e2e-tests:
    runs-on: ubuntu-latest
    needs: [setup, code-quality]
    if: ${{ !github.event.inputs.skip_tests }}
    
    strategy:
      matrix:
        browser: [chrome, firefox, edge]
        
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Install Playwright browsers
      run: npx playwright install ${{ matrix.browser }}
      
    - name: Build application
      run: npm run build:test
      
    - name: Run E2E tests
      run: npm run e2e:${{ matrix.browser }}
      
    - name: Upload test results
      uses: actions/upload-artifact@v3
      if: failure()
      with:
        name: e2e-results-${{ matrix.browser }}
        path: e2e-results/

  performance-tests:
    runs-on: ubuntu-latest
    needs: [setup, unit-tests]
    if: ${{ !github.event.inputs.skip_tests }}
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Build application
      run: npm run build:prod
      
    - name: Run Lighthouse audit
      uses: treosh/lighthouse-ci-action@v9
      with:
        configPath: '.lighthouserc.json'
        uploadArtifacts: true
        temporaryPublicStorage: true
        
    - name: Run bundle size analysis
      run: npm run analyze:bundle
      
    - name: Comment bundle size
      uses: andresz1/size-limit-action@v1
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}

  build-and-push:
    runs-on: ubuntu-latest
    needs: [setup, unit-tests, integration-tests, e2e-tests, security-scan]
    if: ${{ needs.setup.outputs.should-deploy == 'true' }}
    
    outputs:
      image-tag: ${{ steps.meta.outputs.tags }}
      image-digest: ${{ steps.build.outputs.digest }}
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Build application
      run: npm run build:prod
      env:
        VERSION: ${{ needs.setup.outputs.version }}
        ENVIRONMENT: ${{ needs.setup.outputs.environment }}
        
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3
      
    - name: Log in to Container Registry
      uses: docker/login-action@v3
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
        
    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
        tags: |
          type=ref,event=branch
          type=ref,event=pr
          type=sha,prefix={{branch}}-
          type=raw,value=${{ needs.setup.outputs.version }}
          type=raw,value=latest,enable={{is_default_branch}}
          
    - name: Build and push
      id: build
      uses: docker/build-push-action@v5
      with:
        context: .
        platforms: linux/amd64,linux/arm64
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        cache-from: type=gha
        cache-to: type=gha,mode=max
        build-args: |
          VERSION=${{ needs.setup.outputs.version }}
          BUILD_DATE=${{ github.event.head_commit.timestamp }}
          VCS_REF=${{ github.sha }}

  deploy-staging:
    runs-on: ubuntu-latest
    needs: [setup, build-and-push]
    if: ${{ needs.setup.outputs.environment == 'staging' }}
    environment: 
      name: staging
      url: https://staging.your-domain.com
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'v1.28.0'
        
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-west-2
        
    - name: Update kubeconfig
      run: aws eks update-kubeconfig --name staging-cluster
      
    - name: Deploy to staging
      run: |
        helm upgrade --install angular-material-app ./helm/angular-material-app \
          --namespace staging \
          --create-namespace \
          --set image.tag=${{ needs.setup.outputs.version }} \
          --set environment=staging \
          --set ingress.host=staging.your-domain.com \
          --wait --timeout=10m
          
    - name: Verify deployment
      run: |
        kubectl rollout status deployment/angular-material-app -n staging
        kubectl get pods -n staging -l app=angular-material-app
        
    - name: Run smoke tests
      run: npm run smoke-tests:staging

  deploy-production:
    runs-on: ubuntu-latest
    needs: [setup, build-and-push]
    if: ${{ needs.setup.outputs.environment == 'production' }}
    environment: 
      name: production
      url: https://your-domain.com
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      
    - name: Setup kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'v1.28.0'
        
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-west-2
        
    - name: Update kubeconfig
      run: aws eks update-kubeconfig --name production-cluster
      
    - name: Blue-Green deployment
      run: |
        # Deploy to green environment
        helm upgrade --install angular-material-app-green ./helm/angular-material-app \
          --namespace production \
          --set image.tag=${{ needs.setup.outputs.version }} \
          --set environment=production \
          --set service.name=angular-material-app-green \
          --set ingress.enabled=false \
          --wait --timeout=15m
          
        # Run production smoke tests
        npm run smoke-tests:production -- --endpoint=http://angular-material-app-green.production.svc.cluster.local
        
        # Switch traffic
        kubectl patch service angular-material-app -n production \
          -p '{"spec":{"selector":{"app":"angular-material-app-green"}}}'
          
        # Scale down blue environment
        kubectl scale deployment angular-material-app-blue --replicas=0 -n production || true
        
    - name: Verify production deployment
      run: |
        kubectl rollout status deployment/angular-material-app-green -n production
        npm run smoke-tests:production
        
    - name: Notify deployment
      uses: 8398a7/action-slack@v3
      with:
        status: success
        text: 'Angular Material App deployed to production successfully! :rocket:'
      env:
        SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

  post-deployment:
    runs-on: ubuntu-latest
    needs: [deploy-staging, deploy-production]
    if: always() && (needs.deploy-staging.result == 'success' || needs.deploy-production.result == 'success')
    
    steps:
    - name: Update deployment tracking
      run: |
        curl -X POST "${{ secrets.DEPLOYMENT_WEBHOOK }}" \
          -H "Content-Type: application/json" \
          -d '{
            "deployment_id": "${{ github.run_id }}",
            "version": "${{ needs.setup.outputs.version }}",
            "environment": "${{ needs.setup.outputs.environment }}",
            "status": "success",
            "commit": "${{ github.sha }}",
            "timestamp": "${{ github.event.head_commit.timestamp }}"
          }'
          
    - name: Trigger monitoring alerts
      run: |
        # Enable monitoring for new deployment
        curl -X POST "${{ secrets.MONITORING_API }}/deployments" \
          -H "Authorization: Bearer ${{ secrets.MONITORING_TOKEN }}" \
          -H "Content-Type: application/json" \
          -d '{
            "service": "angular-material-app",
            "version": "${{ needs.setup.outputs.version }}",
            "environment": "${{ needs.setup.outputs.environment }}"
          }'
```

### **Jenkins Pipeline Configuration**
```groovy
// Jenkinsfile
pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: node
    image: node:18-alpine
    command: ['cat']
    tty: true
  - name: docker
    image: docker:24-dind
    securityContext:
      privileged: true
  - name: kubectl
    image: bitnami/kubectl:latest
    command: ['cat']
    tty: true
"""
        }
    }
    
    environment {
        REGISTRY = 'your-registry.com'
        IMAGE_NAME = 'angular-material-app'
        NAMESPACE = "${env.BRANCH_NAME == 'main' ? 'production' : 'staging'}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                script {
                    env.VERSION = sh(
                        returnStdout: true,
                        script: 'git describe --tags --always'
                    ).trim()
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                container('node') {
                    sh 'npm ci'
                }
            }
        }
        
        stage('Code Quality') {
            parallel {
                stage('Lint') {
                    steps {
                        container('node') {
                            sh 'npm run lint'
                        }
                    }
                }
                stage('Type Check') {
                    steps {
                        container('node') {
                            sh 'npm run type-check'
                        }
                    }
                }
                stage('Security Scan') {
                    steps {
                        container('node') {
                            sh 'npm audit --audit-level moderate'
                        }
                    }
                }
            }
        }
        
        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        container('node') {
                            sh 'npm run test:ci'
                        }
                    }
                    post {
                        always {
                            publishTestResults testResultsPattern: 'test-results.xml'
                            publishCoverage adapters: [
                                istanbulCoberturaAdapter('coverage/cobertura-coverage.xml')
                            ]
                        }
                    }
                }
                stage('E2E Tests') {
                    steps {
                        container('node') {
                            sh 'npm run e2e:ci'
                        }
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                container('node') {
                    sh 'npm run build:prod'
                }
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                container('docker') {
                    script {
                        def image = docker.build("${REGISTRY}/${IMAGE_NAME}:${VERSION}")
                        docker.withRegistry("https://${REGISTRY}", 'registry-credentials') {
                            image.push()
                            image.push('latest')
                        }
                    }
                }
            }
        }
        
        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            steps {
                container('kubectl') {
                    withCredentials([kubeconfigFile(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh """
                            helm upgrade --install angular-material-app ./helm/angular-material-app \
                                --namespace ${NAMESPACE} \
                                --create-namespace \
                                --set image.tag=${VERSION} \
                                --set environment=${NAMESPACE} \
                                --wait --timeout=10m
                        """
                    }
                }
            }
        }
        
        stage('Smoke Tests') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            steps {
                container('node') {
                    sh "npm run smoke-tests:${NAMESPACE}"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            slackSend(
                channel: '#deployments',
                color: 'good',
                message: "✅ Angular Material App deployed successfully to ${NAMESPACE}! Version: ${VERSION}"
            )
        }
        failure {
            slackSend(
                channel: '#deployments',
                color: 'danger',
                message: "❌ Angular Material App deployment failed in ${NAMESPACE}! Version: ${VERSION}"
            )
        }
    }
}
```

### **GitLab CI/CD Configuration**
```yaml
# .gitlab-ci.yml
stages:
  - validate
  - test
  - build
  - deploy
  - post-deploy

variables:
  NODE_VERSION: "18"
  DOCKER_DRIVER: overlay2
  REGISTRY: $CI_REGISTRY
  IMAGE_NAME: $CI_PROJECT_PATH
  
.node_template: &node_template
  image: node:$NODE_VERSION-alpine
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
  before_script:
    - npm ci

validate:
  <<: *node_template
  stage: validate
  script:
    - npm run lint
    - npm run type-check
    - npm audit --audit-level moderate
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - if: $CI_COMMIT_BRANCH == "develop"

unit-test:
  <<: *node_template
  stage: test
  script:
    - npm run test:ci
  coverage: '/Lines\s*:\s*(\d+\.\d+)%/'
  artifacts:
    reports:
      junit: test-results.xml
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - if: $CI_COMMIT_BRANCH == "develop"

e2e-test:
  <<: *node_template
  stage: test
  services:
    - name: docker:24-dind
      alias: docker
  variables:
    DOCKER_HOST: tcp://docker:2376
    DOCKER_TLS_CERTDIR: "/certs"
  script:
    - npm run build:test
    - npm run e2e:ci
  artifacts:
    when: on_failure
    paths:
      - e2e-results/
    expire_in: 1 week
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - if: $CI_COMMIT_BRANCH == "develop"

build:
  stage: build
  image: docker:24
  services:
    - docker:24-dind
  variables:
    VERSION: ${CI_COMMIT_SHORT_SHA}
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  script:
    - |
      if [ "$CI_COMMIT_BRANCH" == "$CI_DEFAULT_BRANCH" ]; then
        export VERSION=$(git describe --tags --always)
      fi
    - docker build --build-arg VERSION=$VERSION -t $REGISTRY/$IMAGE_NAME:$VERSION .
    - docker push $REGISTRY/$IMAGE_NAME:$VERSION
    - |
      if [ "$CI_COMMIT_BRANCH" == "$CI_DEFAULT_BRANCH" ]; then
        docker tag $REGISTRY/$IMAGE_NAME:$VERSION $REGISTRY/$IMAGE_NAME:latest
        docker push $REGISTRY/$IMAGE_NAME:latest
      fi
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - if: $CI_COMMIT_BRANCH == "develop"

deploy-staging:
  stage: deploy
  image: bitnami/kubectl:latest
  environment:
    name: staging
    url: https://staging.your-domain.com
  before_script:
    - kubectl config use-context $STAGING_CONTEXT
  script:
    - |
      helm upgrade --install angular-material-app ./helm/angular-material-app \
        --namespace staging \
        --create-namespace \
        --set image.tag=${CI_COMMIT_SHORT_SHA} \
        --set environment=staging \
        --wait --timeout=10m
  rules:
    - if: $CI_COMMIT_BRANCH == "develop"

deploy-production:
  stage: deploy
  image: bitnami/kubectl:latest
  environment:
    name: production
    url: https://your-domain.com
  before_script:
    - kubectl config use-context $PRODUCTION_CONTEXT
  script:
    - |
      VERSION=$(git describe --tags --always)
      helm upgrade --install angular-material-app ./helm/angular-material-app \
        --namespace production \
        --set image.tag=$VERSION \
        --set environment=production \
        --wait --timeout=15m
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      when: manual

smoke-tests:
  <<: *node_template
  stage: post-deploy
  script:
    - |
      if [ "$CI_COMMIT_BRANCH" == "$CI_DEFAULT_BRANCH" ]; then
        npm run smoke-tests:production
      else
        npm run smoke-tests:staging
      fi
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - if: $CI_COMMIT_BRANCH == "develop"
```

## 🔧 **Pipeline Configuration Files**

### **Lighthouse CI Configuration**
```json
{
  "ci": {
    "collect": {
      "url": ["http://localhost:4200"],
      "startServerCommand": "npm run serve:prod",
      "numberOfRuns": 3
    },
    "assert": {
      "assertions": {
        "categories:performance": ["error", {"minScore": 0.9}],
        "categories:accessibility": ["error", {"minScore": 0.9}],
        "categories:best-practices": ["error", {"minScore": 0.9}],
        "categories:seo": ["error", {"minScore": 0.9}],
        "categories:pwa": ["warn", {"minScore": 0.8}]
      }
    },
    "upload": {
      "target": "temporary-public-storage"
    }
  }
}
```

### **Size Limit Configuration**
```json
{
  "size-limit": [
    {
      "name": "Main bundle",
      "path": "dist/**/*.js",
      "limit": "500 KB"
    },
    {
      "name": "CSS bundle",
      "path": "dist/**/*.css",
      "limit": "100 KB"
    },
    {
      "name": "Initial load",
      "path": "dist/main*.js",
      "limit": "300 KB"
    }
  ]
}
```

### **Dependency Check Configuration**
```yaml
# dependency-check.yml
dependency-check:
  project: Angular Material App
  format: ALL
  suppression: dependency-check-suppressions.xml
  failBuildOnCVSS: 7
  exclude:
    - "node_modules/**"
    - "dist/**"
  analyzers:
    nodeAuditEnabled: true
    yarnAuditEnabled: true
    ossIndexEnabled: true
```

This comprehensive CI/CD guide provides enterprise-grade automation patterns that ensure reliable, secure, and efficient deployment of Angular Material applications across multiple environments and platforms.
