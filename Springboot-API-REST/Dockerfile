# ══ STAGE 1: Build con Maven ═════════════════════════
FROM maven:3.9-eclipse-temurin-17-alpine AS builder

WORKDIR /app

# Copiar el pom.xml primero (aprovecha caché de Docker)
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copiar el código fuente y compilar
COPY src ./src
RUN mvn clean package -DskipTests -B

# ══ STAGE 2: Imagen de producción ════════════════════
FROM eclipse-temurin:17-jre-alpine

# Crear usuario no-root (requerido por la rúbrica)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Copiar solo el JAR generado
COPY --from=builder /app/target/*.jar app.jar

# Cambiar propietario del archivo
RUN chown appuser:appgroup app.jar

# Cambiar al usuario no-root
USER appuser

EXPOSE 8080

# Ejecutar el JAR de Spring Boot
ENTRYPOINT ["java", "-jar", "app.jar"]