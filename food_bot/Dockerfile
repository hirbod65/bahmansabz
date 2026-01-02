# =========================
# Stage 1: Build
# =========================
FROM maven:3.9-eclipse-temurin-21 AS build

WORKDIR /build

# فقط فایل‌های وابستگی برای cache بهتر
COPY pom.xml .
RUN mvn dependency:go-offline

# کپی سورس و build
COPY src ./src
RUN mvn clean package -DskipTests

# =========================
# Stage 2: Runtime
# =========================
FROM eclipse-temurin:21-jre-alpine

# ساخت non-root user
RUN addgroup -S spring && adduser -S spring -G spring

WORKDIR /app

# کپی jar از stage قبل
COPY --from=build /build/target/*.jar app.jar

# تغییر مالکیت
RUN chown -R spring:spring /app

USER spring

# پورت پیش‌فرض Spring Boot
EXPOSE 8080

# Healthcheck (نیازمند actuator)
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s \
  CMD wget -qO- http://localhost:8080/actuator/health || exit 1

# اجرای اپلیکیشن
ENTRYPOINT ["java","-jar","app.jar"]
