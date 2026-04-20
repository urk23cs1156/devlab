🔥 Q1 – Git (commit, branch, PR)
▶ Commands
git init myproject
cd myproject

echo "# DevOps" > README.md
git add .
git commit -m "initial commit"

git checkout -b feature1
echo "hello" > file.txt
git add .
git commit -m "feature added"

git remote add origin https://github.com/<username>/repo.git
git push -u origin feature1
▶ Then
Go to GitHub → Create Pull Request
Merge → Done
🔥 Q2 – Flask + Docker + Push
📄 app.py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Docker"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
📄 requirements.txt
flask
📄 Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python","app.py"]
▶ Run
docker build -t <username>/flask-app .
docker run -p 5000:5000 <username>/flask-app
docker login
docker push <username>/flask-app

👉 Open: http://localhost:5000

🔥 Q3 – Monolithic Flask App
📄 app.py
from flask import Flask, request, jsonify
app = Flask(__name__)

students = []

@app.route('/students', methods=['GET'])
def get_students():
    return jsonify(students)

@app.route('/students', methods=['POST'])
def add_student():
    data = request.form
    student = {"name": data.get("name"), "roll": data.get("roll")}
    students.append(student)
    return jsonify(student)

if __name__ == "__main__":
    app.run(debug=True)
▶ Run
pip install flask
python app.py

👉 Browser: http://127.0.0.1:5000/students

🔥 Q4 – GitHub Actions (Docker CI)

📄 .github/workflows/docker.yml

name: Docker CI
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      - run: docker build -t ${{ secrets.DOCKER_USERNAME }}/app .
      - run: docker push ${{ secrets.DOCKER_USERNAME }}/app
▶ Setup
GitHub → Settings → Secrets:
DOCKER_USERNAME
DOCKER_PASSWORD
▶ Run
git add .
git commit -m "ci"
git push

👉 Check Actions tab

🔥 Q6 – Kubernetes
▶ Start cluster
# Docker Desktop → Enable Kubernetes
kubectl get nodes
▶ Commands
kubectl create deployment myapp --image=nginx
kubectl get pods

kubectl scale deployment myapp --replicas=3

kubectl expose deployment myapp --port=80 --type=NodePort

kubectl get services

kubectl delete deployment myapp
🔥 Q7 – GitHub Actions (Buildx)

📄 .github/workflows/build.yml

name: Docker Build Push
on: [push]

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      - uses: docker/build-push-action@v5
        with:
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/app:latest
🔥 Q8 – Flask Container (same as Q2 without push)
docker build -t flask-app .
docker run -p 5000:5000 flask-app
🔥 Q9 – Clone, Modify, Push
git clone https://github.com/<username>/<repo>.git
cd repo

echo "update" >> README.md

git add .
git commit -m "updated"
git push
🔥 Q10 – GitHub Actions (Testing)
📄 app.py
def add(a,b): return a+b
def greet(name): return f"Hello, {name}!"
📄 test_app.py
from app import add, greet

def test_add():
    assert add(2,3) == 5

def test_greet():
    assert greet("World") == "Hello, World!"
📄 .github/workflows/test.yml
name: test
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - run: pip install pytest
      - run: pytest
