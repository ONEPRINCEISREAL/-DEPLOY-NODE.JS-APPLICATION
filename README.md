# 🚀 Dockerized Node.js Application with NGINX Load Balancer

This project demonstrates how to containerize a Node.js application using Docker and implement load balancing using NGINX.

---

## 📌 Overview

Docker is a container platform that helps developers avoid the **"works on my machine"** problem by packaging applications with all dependencies into lightweight containers.

In this project:
- A simple Node.js app is created
- The app is containerized using Docker
- Two instances of the app are run
- NGINX is used as a load balancer (round-robin)

---

## ⚙️ Node.js Application

This is a simple HTTP server that returns a message using an environment variable.

### 📄 `index.js`
```js
var http = require('http');

http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/html'});
  res.end(`<h1>${process.env.MESSAGE}</h1>`);
}).listen(8080);
🐳 Dockerizing Node.js App
📄 Dockerfile
FROM node
RUN mkdir -p /usr/src/app
COPY index.js /usr/src/app
EXPOSE 8080
CMD ["node", "/usr/src/app/index"]
🔨 Build Image
docker build -t load-balanced-app .
▶️ Run Two Containers
docker run -e "MESSAGE=First instance" -p 8081:8080 -d load-balanced-app
docker run -e "MESSAGE=Second instance" -p 8082:8080 -d load-balanced-app
🌐 NGINX Load Balancer
📄 nginx.conf
upstream my-app {
    server 172.17.0.1:8081;
    server 172.17.0.1:8082;
}

server {
    location / {
        proxy_pass http://my-app;
    }
}

👉 Uses Round Robin (default algorithm)

🐳 Dockerizing NGINX
📄 Dockerfile
FROM nginx
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d/default.conf
🔨 Build Image
docker build -t load-balance-nginx .
▶️ Run Container
docker run -p 8080:80 -d load-balance-nginx
🌍 Access the Application

Open in browser:

http://localhost:8080

🔄 Refresh multiple times → you’ll see:

"First instance"

"Second instance"

👉 This shows NGINX load balancing working

🧠 Key Concepts Learned

Docker containerization

Writing Dockerfiles

Running multiple containers

Environment variables in containers

NGINX configuration

Load balancing (Round Robin)

⚠️ Notes

172.17.0.1 is Docker's default gateway (may vary)

Ensure Docker is installed before running commands

🚀 Future Improvements

Use Docker Compose

Deploy on AWS EC2

Add CI/CD using GitHub Actions

Use custom domain with load balancer

👨‍💻 Author

Prince Singh Chauhan
DevOps & Cloud Enthusiast 🚀

