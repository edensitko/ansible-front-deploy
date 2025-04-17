# Ansible Docker App Deployer

A **unified**, **idempotent** Ansible solution to **build**, **package**, and **deploy** both **React** and **Next.js** applications in Docker containers.

---

## 🎭 Roles Overview

- **deploy_react_app**: 
  - **Purpose**: Build a static **React** application and serve it via **NGINX** in a Docker container.
  - **Build**: Uses a multi‑stage Dockerfile to install dependencies, build the React project (`npm run build`), and copy the `build/` output into an NGINX image.
  - **Run**: Starts NGINX to serve the static files on the configured ports.

- **deploy_nextjs_app**:
  - **Purpose**: Build and run a **Next.js** application in **production mode** inside a Docker container.
  - **Build**: Uses a multi‑stage Dockerfile to install dependencies, run `next build`, and copy the `.next`, `public`, and `node_modules` folders into a lightweight Node.js image.
  - **Run**: Starts the Next.js server (`npm run start`) to handle both server‑side rendering (SSR) and static assets on the configured ports.

---
## 📂 Repository Layout
```
project-root/
├── inventory.ini            # Hosts & SSH credentials
├── vars.yml                 # App-specific variables (repo URL, ports, etc.)
├── site.yml                 # Main playbook: picks the role to execute
└── roles/
    ├── deploy_react_app/    # Builds & serves static React via NGINX
    │   ├── defaults/vars.yml
    │   ├── tasks/main.yml
    │   └── templates/Dockerfile.j2
    └── deploy_nextjs_app/   # Builds & runs Next.js via Node.js
        ├── defaults/vars.yml
        ├── tasks/main.yml
        └── templates/Dockerfile.j2
```

---
## 🚀 Quick Start

1. **Clone** this repository:
   ```bash
   git clone <repo_url>
   cd <repo_name>
   ```

2. **Configure** your inventory (`inventory.ini`):
   ```ini
   [all]
   your_server_ip ansible_user=ubuntu ansible_ssh_private_key_file=/path/to/key.pem
   ```

3. **Set** application variables in `vars.yml`:
   ```yaml
   # Required: your app Git repo
   app_repo_url: "https://github.com/<your_user>/myPortfolio.git"

   # Docker image & container names
   image_name: "my-app-image"
   container_name: "my-app-container"

   # Container port (React → 80, Next.js → 3000)
   docker_container_port: 80      
   # docker_container_port: 3000 

   # Host port to expose
   docker_host_port: 8080
   ```

4. **Choose** which role to run:
   ```bash
   # React:
   ansible-playbook -i inventory.ini site.yml \  
     -e selected_role=deploy_react_app

   # Next.js:
   ansible-playbook -i inventory.ini site.yml \  
     -e selected_role=deploy_nextjs_app
   ```

5. **Visit** your app in the browser:
   ```
   http://your_server_ip:{{ docker_host_port }}
   ```

---
## 💡 Tips & Best Practices
- **Single source of truth**: inventory, vars and roles in one repo—no scattered playbooks.
- **Idempotent**: safe to re-run; only changes what's needed.
- **Role-driven**: add new frameworks (Vue, Angular) by creating `deploy_<framework>_app`.
- **Secure**: avoid hardcoding credentials; use Ansible Vault or environment variables.
- **Extend**: customize NGINX configuration or Node command by editing the `Dockerfile.j2` template.

# ansible-front-deploy
