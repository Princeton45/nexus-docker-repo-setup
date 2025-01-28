# My Journey Setting Up a Docker Repository on Nexus and Pushing to It

This outlines my experience setting up a private Docker repository on Nexus and configuring everything to be able to push Docker images to it. I'll walk you through the key steps I took/

![Diagram](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/diagram.jpg)

## Technologies I Used

*   **Docker:** To build, manage, and push Docker images.
*   **Nexus Repository Manager:**  The server hosting my private Docker repository.
*   **DigitalOcean:** The cloud provider where my Nexus server is hosted (on a Droplet).
*   **Linux:** The operating system running on my DigitalOcean Droplet (Ubuntu).

## Steps I Took

Here's how I got everything working:

### 1. Created a Docker Hosted Repository on Nexus

First, I logged into my Nexus server's web UI and created a new **hosted** Docker repository. This is where my Docker images will be stored. 

![docker_repo](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/docker_repo.png)


### 2. Created a Docker Repository Role on Nexus

I then created a new role within Nexus specifically for managing access to my Docker repository. I granted this role the necessary privileges to push and pull images.

![docker_role](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/docker_role.png)

### 3. Added the new role to the Nexus user account

Then, I created a new user and attached the role to the user.

![attached](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/attached-role.png)

### 4. Add HTTP connector for Docker Repo on Nexus

Docker requires explicit HTTP configuration because by default it only accepts secure HTTPS connections for registry communication as a security measure.

Since we're not using HTTPS, we need to specifically configure both Docker and Nexus to allow insecure registry connections (HTTP)

We will use `8083` as the port that the Docker repository will listen on.

The Docker repository cannot listen on port `8081` because that port is typically reserved for Nexus's main web interface/API

![attached](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/8083.png)

### 5. Open port 8083 on Droplet

We now need to open the port `8083` on the Digital Ocean Droplet (VM)

![firewall](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/firewall.png)


### 6. Configure Docker Realm in Nexus

We now need to configure the Docker Bearer Token Realm in Nexus Security Realms to allow Docker authentication to work properly. 

Without enabling this realm, Docker clients won't be able to authenticate with the Nexus repository, even if the repository itself is properly configured.

![realm](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/docker_realm.png)

### 7. Allow insecure repository in Docker
 
 We will need to configure the `insecure-registries` option in Docker. Without this configuration, Docker will refuse to push or pull images from the Docker hosted repository since we're using HTTP.

This is necessary because Docker requires HTTPS by default for security reasons.

I edited the `Docker Engine` config in my Docker desktop to add the `insecure-registries` parameter

![insecure-reg](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/insecure-reg.png)

Now we can do a `docker login` to authenticate to the Docker Hosted repository on Nexus.

![docker-login](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/login.png)

### 8. Pushing an image to the repository

I currently have an image in docker named `my-app:1.0` and I will retag the image to include the name of the Docker Hosted repository

`docker tag my-app:1.0 146.190.78.188:8083/my-app:1.0`

Then we can push to the repo:

`docker push 146.190.78.188:8083/my-app:1.0`

![docker-push](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/docker-push.png)

Now we can see the image pushed to the Docker hosted repo in Nexus:

![final](https://github.com/Princeton45/nexus-docker-repo-setup/blob/main/images/final.png)
