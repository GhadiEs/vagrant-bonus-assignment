# Vagrant Load Balancer Project

# Introduction

This project sets up a simple environment using Vagrant and VirtualBox:
- One machine (`lb`) acts as a **Load Balancer**.
- Three machines (`web1`, `web2`, `web3`) serve a simple React application.

The Load Balancer distributes incoming requests across the web servers.

---

# Requirements

You need to have the following installed:

- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
- [Node.js + npm](https://nodejs.org/)

---

# Setup Instructions

1. Open the project folder in VS Code or Command Prompt (CMD).
2. Go inside the `frontend` folder and install the dependencies:

   ```bash
   cd frontend
   npm install
   npm run build
