ARG MSVC_NAME=msvc-users

FROM openjdk:17-jdk-alpine as builder
ARG MSVC_NAME
WORKDIR /app/$MSVC_NAME
# copio el pom.xml padre en carpeta app
COPY ./pom.xml /app
#copio mvn en una carpeta mvn en workdir
COPY ./$MSVC_NAME/.mvn ./.mvn
#copio el ejecutable en la raiz de workdir
COPY ./$MSVC_NAME/mvnw .
COPY ./$MSVC_NAME/pom.xml .

#salta test y compilacion de test y main
RUN ./mvnw clean package -Dmaven.test.skip -Dmaven.main.skip -Dspring-boot.repackage.skip && rm -r ./target/
#RUN ./mvnw dependency:go-offline (alternativa)
#copiar codigo fuente completo en workdir msvc-users
COPY ./$MSVC_NAME/src ./src
#si realizo un cambio, solo cambia esta instruccion, lo demas en CACHE
RUN ./mvnw clean package -DskipTests

# # # # # # # # a partir de una imagen creo otra

FROM openjdk:17-jdk-alpine
WORKDIR /app
RUN mkdir ./logs
# copio el jar de la construccion anterior en app, imagen que usaré
COPY --from=builder /app/msvc-users/target/msvc-users-0.0.1-SNAPSHOT.jar .
ENV PORT 8001
EXPOSE $PORT
ENTRYPOINT ["java","-jar","msvc-users-0.0.1-SNAPSHOT.jar"]

#docker build -t usuarios . -f .\msvc-users\Dockerfile