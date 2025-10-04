## Intro: The Weekend That Changed Everything
It started with a single crash. My VPS unexpectedly went down at 2AM on a Saturday. I was out of town. No access. No backups. That’s when I promised myself — never again.

Fast-forward a month later, I’ve automated the entire setup of my production-ready server infrastructure using **[GitHub Actions, Ansible](https://www.youtube.com/@hugovalters)**, and some clever scripting. From a fresh Hetzner VPS to a hardened, monitored, and SSL-secured Kubernetes-ready node — all in one push.

Here’s how I did it, what worked, and what almost fried my brain.

## Step 1: Why GitHub Actions?
Most people use GitHub Actions for CI/CD pipelines. But I realized — why not use it to automate infrastructure setup? You can trigger provisioning, SSH into fresh hosts, and run Ansible playbooks.

Benefits:
* You don’t need your laptop online to deploy
* Reproducible environments for clients and projects
* Full audit logs via GitHub

## Step 2: Laying the Groundwork (Hetzner + Cloud-init)
I started with Hetzner Cloud. You can deploy servers via API with predefined SSH keys and cloud-init scripts.

I set up a GitHub Action that:
* Calls Hetzner API to create a VPS with a tag (e.g. rancher-node)
* Injects SSH public key
* Waits for the server to become reachable
* Triggers the next job: Ansible provisioning

## Step 3: Ansible Magic — From Zero to Production
My Ansible playbook handles:
* User creation + SSH key setup
* UFW with ports 22 (or 2025), 80, 443, 3306
* Docker + Docker Compose install
* Fail2Ban and automatic updates
* Caddy reverse proxy with Cloudflare DNS plugin
* Tailscale install and auto-auth
* Daily backups to external ZimaBlade over SSH

YAML never looked so sexy.

## Step 4: Secrets and Safety (Without Burning the House Down)
I store secrets (Cloudflare tokens, Tailscale auth keys) in GitHub Encrypted Secrets. Pro tip:

* Use different SSH keys for Ansible than your laptop
* Limit token scopes
* Validate with dummy servers before going full prod

Also: always test with --check and --diff in Ansible before deploying changes.

## Step 5: Reverse Proxy Glory (Caddy FTW)
Forget nginx config hell. Caddy auto-generates HTTPS certs via Let’s Encrypt or Cloudflare DNS.

```
myapp.domain.com {
  reverse_proxy localhost:3000
  tls {
    dns cloudflare {env.CLOUDFLARE_API_TOKEN}
  }
}
```
This is poetry.

## Step 6: Backups That Actually Work
Each server connects via Tailscale to a home ZimaBlade node with a 1TB disk. Nightly rsync backups + email alert if fail. Restoring is one command.

Bonus: I monitor everything with Grafana + Prometheus, also deployed via Ansible.

## Final Thoughts: Why This Matters
Since this setup, I’ve:
* Launched 5+ client projects without touching the console
* Recovered from server failures in <15 mins
* Slept better

Automation isn’t just for big companies. If you’re a solo dev, freelancer, or indie hacker — this kind of setup gives you freedom, resilience, and peace of mind.

If this helped, drop a comment or follow — I plan to open-source parts of the playbooks soon.

**TL;DR? **I automated my whole infrastructure using GitHub Actions + Ansible. Cloudflare, Caddy, Tailscale, Docker, Backups — all in one repo.

And I’ll never SSH manually at 2AM again.

Follow for more:<br>
X.com: https://x.com/hugovalters<br>
bsky.app: https://bsky.app/profile/hugovalters.bsky.social<br>
YouTube: https://www.youtube.com/@hugovalters<br>
Homepage: https://www.valters.eu<br>
GitHub: https://github.com/hugovalters<br>
GitLab: [https://gitlab.com/hugovalters](https://gitlab.com/hugovalters)<br>
Medium: https://blog.valters.eu

By Hugo Valters
