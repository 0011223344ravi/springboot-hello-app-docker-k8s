## Docker Steps 


1. Build the JAR:
   ./mvnw clean package -DskipTests

2. Create a Dockerfile in project root with:
   FROM openjdk:17-jdk-slim
   WORKDIR //app
   COPY target/*.jar app.jar
   EXPOSE 8080
   ENTRYPOINT ["java", "-jar", "app.jar"]

3. Build Docker image:
   docker build -t springboot-hello-app .

4. Verify image exists:
   docker images

5. Run the container:
   docker run -p 8080:8080 springboot-hello-app

6. Access the app:
   Visit http://localhost:8080 in your browser.




## K8s

1. Start Minikube:
   minikube start --driver=docker

2. Point your terminal to Minikube Docker daemon (to use local images):
   eval $(minikube docker-env)  or minikube docker-env

3. Build Docker image (inside Minikube environment):
   docker build -t springboot-hello-app .

4. Create deployment YAML (springboot-deployment.yaml):
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: springboot-hello-deployment
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: springboot-hello
     template:
       metadata:
         labels:
           app: springboot-hello
       spec:
         containers:
         - name: springboot-hello
           image: springboot-hello-app
           imagePullPolicy: IfNotPresent
           ports:
           - containerPort: 8080

5. Create service YAML (springboot-service.yaml):
   apiVersion: v1
   kind: Service
   metadata:
     name: springboot-hello-service
   spec:
     type: NodePort
     selector:
       app: springboot-hello
     ports:
       - protocol: TCP
         port: 8080
         targetPort: 8080
         nodePort: 30036

6. Apply deployment and service:
   kubectl apply -f springboot-deployment.yaml
   kubectl apply -f springboot-service.yaml

7. Access the service in browser:
   minikube service springboot-hello-service

8. Optional: View the dashboard:
   minikube dashboard
