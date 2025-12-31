# 🚀 Server Deployment Guide - 2C Creative Interactive

## 📍 Server Infrastructure

- **IP:** 212.227.139.247
- **OS:** Ubuntu 24.04 LTS
- **Nginx:** 1.24.0
- **Node.js:** 20.x
- **Package Manager:** pnpm
- **Web Framework:** Astro 5.x

## 🌐 Live Sites

| Dominio | Cartella | SSL | Status |
|---------|----------|-----|--------|
| https://2c-creativeinteractive.com | `/var/www/html` | Let's Encrypt ✅ | Live |
| https://lauraecaterina60.it | `/var/www/lauraecaterina60` | Let's Encrypt ✅ | Live |
| https://mata33.art | `/var/www/mata33art` | Let's Encrypt ✅ | Live |

## 🔧 NGINX UI Dashboard

- **URL:** https://212.227.139.247:9000
- **Port:** 9000
- **HTTPS:** Self-signed certificate

## 📦 Struttura Directory

/var/www/
├── html/ # 2c-creativeinteractive.com
│ └── [dist files]
├── lauraecaterina60/ # lauraecaterina60.it
│ └── [dist files]
├── mata33art/ # mata33.art
│ └── [dist files]
├── astro-site/ # Repo 2c-creativeinteractive-astro
│ ├── src/
│ ├── dist/
│ └── package.json
├── laura-sito/ # Repo Laura
│ ├── src/
│ ├── dist/
│ └── package.json
└── mata-sito/ # Repo Mata33
├── src/
├── dist/
└── package.json


## 🚀 Deploy Manuale (Metodo Consigliato)

### Per 2c-creativeinteractive.com

ssh root@212.227.139.247
cd /var/www/astro-site

Pull + Build
git pull origin main
pnpm install
pnpm build

Deploy Live
cp -r dist/* /var/www/html/
chown -R www-data:www-data /var/www/html
systemctl reload nginx

Verifica
curl -I https://2c-creativeinteractive.com


### Per lauraecaterina60.it

ssh root@212.227.139.247
cd /var/www/laura-sito

git pull origin main
pnpm install
pnpm build

cp -r dist/* /var/www/lauraecaterina60/
chown -R www-data:www-data /var/www/lauraecaterina60
systemctl reload nginx

curl -I https://lauraecaterina60.it

### Per mata33.art

ssh root@212.227.139.247
cd /var/www/mata-sito

git pull origin main
pnpm install
pnpm build

cp -r dist/* /var/www/mata33art/
chown -R www-data:www-data /var/www/mata33art
systemctl reload nginx

curl -I https://mata33.art


## 🎯 NGINX UI - Aggiungere Nuovo Sito

### Step 1: Prepara Dominio

IONOS → DNS → A Record:
nuovo-dominio.it → 212.227.139.247
www.nuovo-dominio.it → 212.227.139.247

Aspetta 5-30 min propagazione DNS

text

### Step 2: Deploy Astro su Server

ssh root@212.227.139.247
cd /var/www/

git clone https://github.com/CodigoInteractivo/REPO_NUOVO.git nuovo-sito
cd nuovo-sito

pnpm install
pnpm build

mkdir -p /var/www/nuovo-cartella
cp -r dist/* /var/www/nuovo-cartella/
chown -R www-data:www-data /var/www/nuovo-cartella
chmod -R 755 /var/www/nuovo-cartella

text

### Step 3: NGINX UI - Add Site (SENZA REDIRECT INIZIALE)

**NGINX UI → Sites → Add Site**

Configuration Name: nuovo-dominio.it

Directives HTTP (listen 80):
listen 80;
listen [::]:80;
server_name nuovo-dominio.it www.nuovo-dominio.it;
root /var/www/nuovo-cartella;
index index.html index.htm;

Directives HTTPS (listen 443):
listen 443 ssl http2;
listen [::]:443 ssl http2;
server_name nuovo-dominio.it www.nuovo-dominio.it;
root /var/www/nuovo-cartella;
index index.html index.htm;

text

**SAVE!**

### Step 4: Genera SSL Certificate

**NGINX UI → nuovo-dominio.it → Tab SSL**

✅ Enable TLS: Checked
✅ Challenge Method: HTTP01
✅ Key Type: EC256
✅ OCSP Must Staple: Unchecked
✅ Revoke Old Certificate: Checked

→ NEXT → FINISHED

(Aspetta 5 min per certificato)

text

### Step 5: Aggiungi Redirect HTTP→HTTPS (DOPO SSL OK)

**SSH Server:**

nano /etc/nginx/sites-available/nuovo-dominio.it

text

**Sostituisci blocco HTTP:**

listen 80;
listen [::]:80;
server_name nuovo-dominio.it www.nuovo-dominio.it;
return 301 https://$server_name$request_uri;

text

**SALVA + Ricarica:**

systemctl reload nginx

text

### Step 6: Verifica Live

curl -I https://nuovo-dominio.it

HTTP/2 200 ✅

