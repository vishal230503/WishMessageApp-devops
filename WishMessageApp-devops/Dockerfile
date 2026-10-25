# ---------- Stage 1: build the WAR ----------
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn -B -q dependency:go-offline
COPY src ./src
RUN mvn -B -q clean package -DskipTests

# ---------- Stage 2: run on Tomcat 10.1 (Jakarta EE 10, Java 17) ----------
FROM tomcat:10.1-jre17-temurin
RUN rm -rf /usr/local/tomcat/webapps/*
# Deploy as ROOT so the relative links (wishurl, page.html) work at "/"
COPY --from=build /app/target/WishMessageApp.war /usr/local/tomcat/webapps/ROOT.war

# Run as non-root
RUN useradd -r -u 1001 appuser && chown -R appuser /usr/local/tomcat
USER 1001

EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=3 \
  CMD curl -fs http://localhost:8080/page.html || exit 1
CMD ["catalina.sh", "run"]
