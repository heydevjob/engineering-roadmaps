# Project to HeyDevJob Ticket Map

Every project in this library maps to a real ticket on [HeyDevJob](https://heydevjob.com),
where you fix it in a live cloud workspace. This table is the index.

## DevOps

| Project | HeyDevJob ticket |
|---------|------------------|
| [Fix the Nginx 502 Bad Gateway](projects/devops/fix-the-nginx-502-bad-gateway.md) | DO-101 |
| [Recover a Crashed Linux Service](projects/devops/recover-a-crashed-linux-service.md) | DO-102 |
| [Bind a Node App to 0.0.0.0 (All Interfaces)](projects/devops/bind-a-node-app-to-all-interfaces.md) | DO-104 |
| [Shrink a Bloated Docker Image (Multi-Stage Build)](projects/devops/shrink-a-bloated-docker-image.md) | DO-114 |
| [Stop a Worker From Filling the Disk](projects/devops/stop-a-worker-filling-the-disk.md) | DO-205 |
| [Reconcile Terraform State Drift](projects/devops/reconcile-terraform-state-drift.md) | DO-212 |
| [Resolve Postgres Pool Exhaustion Under Load](projects/devops/resolve-postgres-pool-exhaustion.md) | DO-223 |
| [Automate a Node.js Deploy Pipeline With Rollback](projects/devops/automate-a-nodejs-deploy-with-rollback.md) | DO-307 |
| [Triage a SEV1 Production Incident](projects/devops/triage-a-sev1-production-incident.md) | DO-308 |
| [Build a Versioned Secret-Rotation System](projects/devops/build-a-secret-rotation-system.md) | DO-317 |

## Backend

| Project | HeyDevJob ticket |
|---------|------------------|
| [Return the Correct HTTP Status Code](projects/backend/return-the-correct-http-status-code.md) | BE-101 |
| [Add Server-Side Form Validation](projects/backend/add-server-side-validation.md) | BE-103 |
| [Add JWT Authentication to an API](projects/backend/add-jwt-authentication.md) | BE-112 |
| [Add a Rate Limiter to an API](projects/backend/add-a-rate-limiter.md) | BE-113 |
| [Cache a Hot Read Path With Redis](projects/backend/cache-a-hot-read-path-with-redis.md) | BE-114 |
| [Index a Slow Postgres Query](projects/backend/index-a-slow-postgres-query.md) | BE-115 |
| [Switch to Cursor-Based Pagination](projects/backend/switch-to-cursor-pagination.md) | BE-116 |
| [Honor an Idempotency-Key Header on POST](projects/backend/honor-an-idempotency-key.md) | BE-123 |
| [Fix the N+1 Query](projects/backend/fix-the-n-plus-1-query.md) | BE-203 |
| [Stop a Double-Charge Race Condition](projects/backend/stop-a-double-charge-race-condition.md) | BE-204 |
| [Rename a Postgres Column With Zero Downtime](projects/backend/rename-a-postgres-column-zero-downtime.md) | BE-307 |
| [Build an Order Saga With Compensating Actions](projects/backend/build-an-order-saga.md) | BE-309 |
| [Design a URL Shortener (System Design)](projects/backend/design-a-url-shortener.md) | BE-310 |

## Data

| Project | HeyDevJob ticket |
|---------|------------------|
| [Make an ETL Pipeline Idempotent](projects/data/make-an-etl-pipeline-idempotent.md) | DE-101 |
| [Make a CSV Importer Resilient to Bad Data](projects/data/make-a-csv-importer-resilient.md) | DE-104 |
| [Debug a Broken SQL Revenue Aggregation](projects/data/debug-a-broken-sql-aggregation.md) | DE-108 |
| [Implement Incremental Loading in an ETL](projects/data/implement-incremental-loading.md) | DE-110 |
| [Convert CSV to Parquet for a Lakehouse](projects/data/convert-csv-to-parquet.md) | DE-113 |
| [Rank Rows Per Group With SQL Window Functions](projects/data/rank-rows-with-window-functions.md) | DE-115 |
| [Optimize Batch Insert Throughput (COPY)](projects/data/optimize-batch-insert-with-copy.md) | DE-203 |
| [Schedule a Pipeline as an Airflow DAG](projects/data/schedule-a-pipeline-with-airflow.md) | DE-205 |
| [Build a Staging-to-Marts dbt Project](projects/data/build-a-dbt-staging-to-marts-project.md) | DE-206 |
| [Migrate a pandas Pipeline to Polars](projects/data/migrate-pandas-to-polars.md) | DE-208 |
| [Build a Kimball Star Schema](projects/data/build-a-kimball-star-schema.md) | DE-302 |
| [Backfill a Column Safely (Batched + Verified)](projects/data/backfill-a-column-safely.md) | DE-304 |
| [Build an Exactly-Once Kafka Pipeline](projects/data/build-an-exactly-once-kafka-pipeline.md) | DE-308 |

## Security

| Project | HeyDevJob ticket |
|---------|------------------|
| [Close a SQL Injection in a Search Endpoint](projects/security/close-a-sql-injection.md) | SE-101 |
| [Neutralize a Stored XSS in Comments](projects/security/neutralize-a-stored-xss.md) | SE-102 |
| [Hash Passwords Instead of Storing Plaintext](projects/security/hash-passwords-instead-of-plaintext.md) | SE-104 |
| [Block a Path Traversal Vulnerability](projects/security/block-a-path-traversal.md) | SE-106 |
| [Harden Session Cookie Security Flags](projects/security/harden-session-cookie-flags.md) | SE-110 |
| [Scrub a Secret From Git History](projects/security/scrub-a-secret-from-git-history.md) | SE-116 |
| [Close a Command Injection in a Diagnostics API](projects/security/close-a-command-injection.md) | SE-119 |
| [Patch the JWT None-Algorithm Bypass](projects/security/patch-the-jwt-none-algorithm-bypass.md) | SE-201 |
| [Add Defense-in-Depth to a Public API](projects/security/add-defense-in-depth-to-an-api.md) | SE-205 |
| [Patch Vulnerable npm Dependencies](projects/security/patch-vulnerable-npm-dependencies.md) | SE-207 |
| [Move a Plaintext Secret to AWS Secrets Manager](projects/security/move-a-secret-to-aws-secrets-manager.md) | SE-214 |
| [Fix an IDOR Exposing Other Users' Invoices](projects/security/fix-an-idor.md) | SE-216 |
| [Block an SSRF in a URL Fetcher](projects/security/block-an-ssrf.md) | SE-217 |
| [Build a Security Scan Pipeline (SAST + SCA)](projects/security/build-a-security-scan-pipeline.md) | SE-301 |
| [Move JWT to httpOnly Cookies (XSS + CSRF)](projects/security/move-jwt-to-httponly-cookies.md) | SE-304 |
| [Tighten Over-Permissive AWS IAM Policies](projects/security/tighten-aws-iam-policies.md) | SE-305 |

## AI/ML

| Project | HeyDevJob ticket |
|---------|------------------|
| [Restore a Broken LLM API Integration](projects/ai/restore-a-broken-llm-api-integration.md) | AI-101 |
| [Improve an LLM Sentiment Classifier Prompt](projects/ai/improve-a-classifier-prompt.md) | AI-102 |
| [Parse JSON From an LLM (Strip Markdown Fences)](projects/ai/parse-json-from-an-llm.md) | AI-104 |
| [Build a Text Summarization Endpoint With an LLM](projects/ai/build-a-summarization-endpoint.md) | AI-106 |
| [Add Conversation Memory to a Chatbot](projects/ai/add-conversation-memory.md) | AI-107 |
| [Restore Token Streaming on a Chat Endpoint](projects/ai/restore-token-streaming.md) | AI-109 |
| [Cut LLM Costs by Caching With Redis](projects/ai/cut-llm-costs-with-redis-caching.md) | AI-203 |
| [Power RAG Search With a Vector Database](projects/ai/power-rag-with-a-vector-database.md) | AI-204 |
| [Fix the Chunking That's Wrecking RAG Retrieval](projects/ai/fix-the-rag-chunking.md) | AI-205 |
| [Make an LLM Pick the Right Tool (Function Calling)](projects/ai/make-an-llm-call-tools.md) | AI-211 |
| [Build an End-to-End RAG Pipeline](projects/ai/build-an-end-to-end-rag-pipeline.md) | AI-301 |
| [Build an LLM Evaluation Harness (Golden Set)](projects/ai/build-an-llm-eval-harness.md) | AI-302 |
| [Build a Tool-Calling Agent Loop](projects/ai/build-a-tool-calling-agent-loop.md) | AI-309 |

## Kubernetes

| Project | HeyDevJob ticket |
|---------|------------------|
| [Fix a Kubernetes CrashLoopBackOff](projects/kubernetes/fix-a-kubernetes-crashloopbackoff.md) | K8-101 |
| [Heal a Kubernetes Service With No Endpoints](projects/kubernetes/heal-a-service-with-no-endpoints.md) | K8-102 |
| [Fix a Kubernetes ImagePullBackOff](projects/kubernetes/fix-a-kubernetes-imagepullbackoff.md) | K8-103 |
| [Schedule a Pending Kubernetes Pod](projects/kubernetes/schedule-a-pending-pod.md) | K8-104 |
| [Reconnect a Pod to Its ConfigMap](projects/kubernetes/reconnect-a-pod-to-its-configmap.md) | K8-105 |
| [Write a Production Kubernetes Deployment](projects/kubernetes/write-a-production-deployment.md) | K8-111 |
| [Write and Lint a Helm Chart](projects/kubernetes/write-and-lint-a-helm-chart.md) | K8-203 |
| [Configure a Kubernetes HPA for Autoscaling](projects/kubernetes/configure-an-hpa-for-autoscaling.md) | K8-206 |
| [Release a New Image With a Rolling Update](projects/kubernetes/release-with-a-rolling-update.md) | K8-207 |
| [Add Liveness and Readiness Probes to a Deployment](projects/kubernetes/add-liveness-and-readiness-probes.md) | K8-208 |
| [Lock Down a Namespace With a NetworkPolicy](projects/kubernetes/lock-down-a-namespace-with-networkpolicy.md) | K8-303 |
| [Configure a Zero-Downtime Rolling Update](projects/kubernetes/configure-a-zero-downtime-rolling-update.md) | K8-305 |
| [Harden a Pod With a securityContext](projects/kubernetes/harden-a-pod-with-securitycontext.md) | K8-308 |

## Fullstack

| Project | HeyDevJob ticket |
|---------|------------------|
| [Fix a React useState Stale-State Bug](projects/fullstack/fix-a-react-stale-state-bug.md) | FS-101 |
| [Upload Files to AWS S3 From a React Form](projects/fullstack/upload-files-to-s3-from-react.md) | FS-102 |
| [Fix a Blank React Dashboard (Failed Fetch)](projects/fullstack/fix-a-blank-react-dashboard.md) | FS-103 |
| [Fix the CORS Error Blocking Your API Calls](projects/fullstack/fix-the-cors-error.md) | FS-106 |
| [Stop the Form From Reloading the Page on Submit](projects/fullstack/stop-the-form-reloading-on-submit.md) | FS-107 |
| [Return 400 on a Missing Field Instead of Crashing With 500](projects/fullstack/return-400-instead-of-500.md) | FS-110 |
| [Wire React to a Database (CRUD)](projects/fullstack/wire-react-to-a-database-crud.md) | FS-204 |
| [Add a JWT Login and a Protected Route in React](projects/fullstack/add-jwt-login-and-protected-route.md) | FS-207 |
| [Stream the AI Chat Response](projects/fullstack/stream-the-ai-chat-response.md) | FS-208 |
| [Implement Cursor Pagination for a 'Load More' Feed](projects/fullstack/cursor-pagination-load-more.md) | FS-210 |
| [Design a Resumable Large-File Upload](projects/fullstack/design-a-resumable-file-upload.md) | FS-302 |
| [Build Real-Time Chat Broadcast With Server-Sent Events](projects/fullstack/build-realtime-chat-with-sse.md) | FS-305 |
| [Add Optimistic UI With Rollback to a Todo List](projects/fullstack/add-optimistic-ui-with-rollback.md) | FS-306 |
