# My Journey Setting Up a Docker Repository on Nexus and Pushing to It

This outlines my experience setting up a private Docker repository on Nexus and configuring everything to be able to push Docker images to it. I'll walk you through the key steps I took/

![Diagram](https://github.com/Princeton45/nexus-droplet-setup/blob/main/images/diagram.jpg)

## Technologies I Used

*   **Docker:** To build, manage, and push Docker images.
*   **Nexus Repository Manager:**  The server hosting my private Docker repository.
*   **DigitalOcean:** The cloud provider where my Nexus server is hosted (on a Droplet).
*   **Linux:** The operating system running on my DigitalOcean Droplet (Ubuntu).

## Steps I Took

Here's how I got everything working:

### 1. Created a Docker Hosted Repository on Nexus

First, I logged into my Nexus server's web UI and created a new **hosted** Docker repository. This is where my Docker images will be stored. I made sure to enable the "HTTP" connector on a specific port, and to check "Allow anonymous docker pull", so I can test pulling images without needing to log in.

*   **Suggestions for Visuals:**
    *   **Picture 1:** A screenshot of the Nexus UI showing the "Create repository" page, highlighting the settings for the new Docker hosted repository, especially the HTTP port.
    *   **Picture 2:** The Nexus UI showing the newly created Docker repository in the list of repositories.

### 2. Created a Docker Repository Role on Nexus

I then created a new role within Nexus specifically for managing access to my Docker repository. I granted this role the necessary privileges to push and pull images.

*   **Suggestions for Visuals:**
    *   **Picture 3:** A screenshot of the Nexus UI showing the "Create role" page, with the privileges for the Docker repository role selected.

### 3. Created a Docker Repository user on Nexus

Then, I created a new user that has the role I just created.

*   **Suggestions for Visuals:**
    *   **Picture 4:** A screenshot of the Nexus UI showing the "Create User" page, with the role added for the Docker repository user selected.

### 4. Configured the DigitalOcean Droplet and Docker for Pushing

This was the trickiest part. I needed to configure my DigitalOcean Droplet (where Nexus is running) and my local Docker setup to allow pushing to the new repository. This involved modifying the Docker daemon settings on my local machine to add an insecure registry entry for my Nexus server, since I'm using HTTP for now. Then I logged in to my Nexus server through docker login.

*   **Suggestions for Visuals:**
    *   **Picture 5:** My terminal showing the `docker login` command I used to authenticate with my Nexus server using the created user.

### 5. Built and Pushed a Docker Image

Finally, I built a Docker image on my local machine and tagged it appropriately to point to my Nexus Docker repository. Then, using the `docker push` command, I pushed the image to the repository.

*   **Suggestions for Visuals:**
    *   **Picture 6:** My terminal showing the `docker build` command used to build a sample Docker image.
    *   **Picture 7:** My terminal showing the `docker tag` command used to tag the image for my Nexus repository.
    *   **Picture 8:** My terminal showing the `docker push` command successfully pushing the image to Nexus.
    *   **Picture 9:** The Nexus UI showing the newly pushed Docker image in my hosted repository.

## Conclusion

And that's it! I now have a fully functional private Docker repository on my Nexus server. I can build Docker images locally and push them to this repository, making them available for deployment or sharing. This setup greatly improves my workflow for managing Docker images. I hope sharing my experience helps you in setting up your own private Docker repositories! Feel free to reach out if you have any questions. Happy coding!