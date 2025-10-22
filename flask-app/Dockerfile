# 1️⃣ Base image
FROM alpine:3.21.0

# 2️⃣ Installer Python 3, pip et dépendances système
RUN apk add --no-cache build-base libffi-dev openssl-dev py3-pip python3

# 3️⃣ Définir le dossier de travail
WORKDIR /usr/src/app

# 4️⃣ Créer un environnement virtuel Python
RUN python3 -m venv venv

# 5️⃣ Forcer l’utilisation du venv
ENV PATH="/usr/src/app/venv/bin:$PATH"

# 6️⃣ Copier le fichier requirements et installer les dépendances Python
COPY requirements.txt ./
RUN pip install --no-cache-dir --upgrade pip && pip install --no-cache-dir -r requirements.txt

# 7️⃣ Copier le code de ton application Flask
COPY app.py ./
COPY templates/index.html ./templates/

# 8️⃣ Exposer le port 5000 (celui utilisé par Flask)
EXPOSE 5000

# 9️⃣ Commande à exécuter quand le conteneur démarre
CMD ["python", "/usr/src/app/app.py"]
