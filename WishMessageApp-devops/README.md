# WishMessageApp – DevOps & Cloud Deployment

Servlet app (Jakarta EE 9+, Java 17) -> WAR -> Docker image (Tomcat 10.1) -> Kubernetes.

## Endpoints
- `/`          -> HomeServlet (welcome file `homeurl`)
- `/page.html` -> link to the wish servlet
- `/wishurl`   -> WishMessageServlet (Good Morning/Afternoon/Evening/Night)

## Run locally
    mvn clean package                 # builds target/WishMessageApp.war
    docker compose up --build         # http://localhost:8080

## CI/CD
- GitHub Actions: `.github/workflows/ci-cd.yml` (build -> push to GHCR -> kubectl deploy)
- Jenkins:        `Jenkinsfile` (build -> push to Docker Hub -> kubectl deploy)

Secrets needed: `KUBE_CONFIG_B64` (GitHub) or credentials `dockerhub-creds`, `kubeconfig` (Jenkins).

## Deploy to cloud (Kubernetes: EKS / AKS / GKE)
    kubectl apply -f k8s/

## Notes
- Must run on Tomcat 10+ (jakarta.* namespace). Tomcat 9 will NOT work.
- WAR is deployed as ROOT so relative links work.
- The server clock decides the greeting, so containers use UTC (set `TZ` env var for local time).
