Here I explain how to deploy your code using GitHub Actions and Ansible with Tailscale VPN.
I assume your workstation runs Linux or MacOS, your server runs Ubuntu/Debian or derivative
and you have already [installed](https://tailscale.com/kb/1031/install-linux) Tailscale.

1. Open your Github repo secrets page: Settings > Secrets and Variables > Actions.

2. Create Tailscale tag: at https://login.tailscale.com/admin/acls/file add `"tag:ci": [...],` to `tagOwners` section.

3. Create Oauth secret at Tailscale control panel: go to <https://login.tailscale.com/admin/settings/oauth>,
click "Generate OAuth client", check both "Read" and "Write" for Auth Keys and select `tag:ci`. Copy newly
created client ID and secret to Github secrets `TS_OAUTH_CLIENT_ID` and `TS_OAUTH_SECRET`,
respectively.

4. Create deploy SSH key, then create Github secret named `DEPLOY_SSH_KEY` with the
contents of `~/.ssh/github-deploy` file:
```
ssh-keygen -f ~/.ssh/github-deploy
```
 
5. Create server user for deploy:
```
sudo useradd -m deploy
sudo mkdir -p /home/deploy/.ssh
sudo nano /home/deploy/.ssh/authorized_keys # and paste the contents of ~/.ssh/github-deploy.pub
sudo chmod 600 /home/deploy/.ssh/authorized_keys
sudo chmod 700 /home/deploy/.ssh
```
From now on your commits to the `main` branch will trigger the deploy.
