# Podman

There are many tools out there in the world of containers. Docker is clearly the most popular tool in terms of container engines. But an alternative – Podman – is attracting growing attention and could be a good choice for your projects. In this blog post, I will explain what Podman is and how it differs from Docker, and give you the information you need to help you decide when to use Podman in your environment.

What is Podman?

Podman is an open source, OCI (Open Container Initiative) compliant container engine (see GitHub). It is driven by Red Hat and incorporates a few major differences from Docker, such as its daemonless architecture and support for rootless containers. At their core, both tools do the same thing: manage images and containers. One of Podman’s objectives is to have a Docker-compatible API. Hence almost all CLI (command line interface) commands from the Docker CLI are also available in Podman.
You may find two other tools in the Podman ecosystem: Buildah and Skopeo. Buildah is a CLI tool used to build container images, and Skopeo is a CLI tool for running operations on images, such as push, pull or inspect. Please check out GitHub for more information on these tools and their relationship with Podman.

 The major differences

The greatest difference between Docker and Podman is their architecture. Docker runs on a client-server architecture, while Podman runs on a daemonless architecture. But what does that mean? When working with Docker, you have to use the Docker CLI, which communicates with a background daemon (the Docker daemon). The main logic resides in the daemon, which builds images and executes containers. This daemon runs with root privileges. The Podman architecture by contrast allows you to run the containers under the user that is starting the container (fork/exec), and this user does not need any root privileges. Because Podman has a daemonless architecture, each user running Podman can only see and modify their own containers. There is no common daemon that the CLI tool communicates with.
Since Podman does not have a daemon, it needs a way to support running containers in the background. It therefore provides an integration with systemd, which allows containers to be controlled via systemd units. Depending on the Podman version, you can generate these units for existing containers or generate units that are able to create containers if they do not exist in the system. There is another integration model with systemd, which enables systemd to run inside a container.

The second major difference concerns how containers are executed. With Podman, containers are executed under the user’s privileges and not under the daemon. At this point, the concept of rootless containers comes into play, meaning that the container can be started without root privileges. Rootless containers have a huge advantage over rootful containers since (you guessed it) they do not run under the root account. The benefit of this is that if an attacker is able to capture and escape a container, this attacker is still a normal user on the host. Containers that are started by a user cannot have more privileges or capabilities than the user itself. This adds a natural layer of protection. Additional setup steps are required to use rootless containers (see GitHub) because rootless containers rely on user namespaces and other features that may need to be installed or configured first. Docker is implementing a rootless mode for the Docker daemon at the time of writing (see Docker documentation), but this mode is still experimental.

Is Podman something for your projects?

Podman is a good candidate if you have increased security requirements in terms of runtime privileges or if you are able to start a project from scratch. You can aim to move to Kubernetes later if you use pods from the beginning. Remember that Podman is intrinsically based on Linux concepts and has no support for Windows containers. Your developers therefore need Linux expertise.

If you have an ongoing project that depends on Docker specifics (such as Docker Compose), using Podman is generally not worthwhile, as the migration requires some effort and changes in the development environment.

Depending on your project’s goal, it may be useful to combine the two tools. If you have increased security requirements in production, you could continue to use Docker on the development machines and switch to Podman in the runtime environments (dev, int, prod and so on). This combination is possible because both tools are OCI-compliant, and Podman supports Dockerfiles and image specifics. These kinds of combinations must be validated carefully as they require knowledge of both tools.

Podman is growing, and its developers are continually adding new functionalities. They recently added a RESTful API that enables remote control of Podman. Previously only a varlink API was available for remote control. According to the road map, improvements will be made to the RESTful API and to the rootless feature. In addition, the developers are going to build an integration with CRI-O.

-------------
