# Go Application Deployment with Docker and Kubernetes

This guide walks you through the process of creating a simple Go web application, containerizing it with Docker, and deploying it on Kubernetes.

## 1. Create a Simple Go Application

Create a file named `main.go` with the following content:

```go
package main

import (
	"fmt"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello, Go!")
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
```

2. Create a Dockerfile
Create a file named Dockerfile in the same directory as your main.go:

Dockerfile
``` bash
# Use the official Go image as the base image
FROM golang:1.17-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy the local package files to the container's workspace
COPY . .

# Build the Go application
RUN go build -o main .

# Expose port 8080 to the outside world
EXPOSE 8080

# Command to run the executable
CMD ["./main"]
``` 
3. Build and Push Docker Image
Navigate to the directory containing your Dockerfile and main.go file. Run the following commands:

``` bash
# Build the Docker image
docker build -t your-username/your-go-app:latest .

# Push the Docker image to a container registry (e.g., Docker Hub)
docker push your-username/your-go-app:latest
```
4. Set Up a Kubernetes Deployment
Create a Kubernetes Deployment YAML file, let's call it your-go-app-deployment.yaml:

``` bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-go-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: your-go-app
  template:
    metadata:
      labels:
        app: your-go-app
    spec:
      containers:
      - name: your-go-app-container
        image: your-username/your-go-app:latest
        ports:
        - containerPort: 8080
        
``` 
Apply the deployment:

``` bash
kubectl apply -f your-go-app-deployment.yaml 
```
5. Expose the Deployment
Create a Kubernetes Service YAML file, let's call it your-go-app-service.yaml:

``` bash
apiVersion: v1
kind: Service
metadata:
  name: your-go-app-service
spec:
  selector:
    app: your-go-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
  
 ``` 
Apply the service:

``` bash
kubectl apply -f your-go-app-service.yaml
``` 
6. Access the Application
Retrieve the external IP of the service:

``` bash 
kubectl get services -o wide
```
Access your application using the external IP. Open your web browser and navigate to http://<external-ip>.

