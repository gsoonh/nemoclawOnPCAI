# NemoClaw Deployment on HPE Private Cloud AI


## 📑 Table of Contents

- [Description](#description)
- [Key Features](#key-features)
- [Use Cases](#use-cases)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Contributing](#contributing)

## 📝 Description

NVIDIA NemoClaw deploys very well in Docker-based environments. However, deploying NemoClaw on Kubernetes is more complex, as NVIDIA currently does not provide a complete end-to-end deployment guide or reference architecture for Kubernetes platforms such as HPE Private Cloud AI (PCAI).

This project provides a comprehensive step-by-step guide for deploying NemoClaw on Kubernetes running within HPE PCAI. It first explains the deployment architecture and core concepts, then walks through the installation, configuration, and deployment procedures required to successfully run NemoClaw in an enterprise Kubernetes environment. In the event if you don't have HPE PCAI, this solution also work on kubernetes on any platform with minor changes.

## ✨ Key Features

- **📂 HPE Private Cloud AI** — A turnkey private cloud platform, co-engineered with Nvidia to delivers a complete, secure AI workbench for rapid deployment of AI models and production use cases. 
- **🛠️ Ready to be deploy** — You can clone and deploy on any kubernetes with minor modification. 

## 🎯 Use Cases

- Establishing a clean slate for starting a new software prototype.
- Testing repository connection, configuration, and integration pipelines with minimal noise.

## ⚡ Quick Start

# 1. Clone the repository
```
git clone https://github.com/gsoonh/nemoclawOnPCAI.git
```
# 2. Make sure you have a model to serve the openclaw. 
For my case, I deploy mistra:7b on ollama and expose it out as an end-point.  In HPE PCAI, you can also deploy a model using MLIS and expose it out as a API endpoint. I choose ollama for simplicity.

# 3. Deploy on HPE PCAI using Import Framework 
I had included the deployment steps as images: pcai-import1.PNG to pcai-import4.PNG. Refer it in the "images" folder. 
To do so, login to HPE PCAI, go to Tools/Framework and choose import framework and follow the deployment steps. 

If you are not using HPE PCAI, you can deploy directly using the helm chart. However, you would need to modify the values.yaml file. In particular, the "ezua" section and replace with your own virtualservices. Also, reconfigure your istiogateway. Also, in the virtualServices.yaml file, remove all the "hpe-ezua" label or any "ezua" related settings. After all these are done, you would need to repackage your helm chart.
     

# Architecture

Here is the architecture diagram:

![NemoClaw Kubernetes Architecture](./images/Architecture.png)

NemoClaw is deployed on a Kubernetes (k8s) cluster running on one worker nodes of the PCAI. Since k8s uses containerd instead of Docker as its native runtime, the cluster does not provide a Docker daemon (dockerd) required by the NemoClaw onboarding workflow. To bridge this gap, the nemoclaw-app Pod runs a dedicated Docker-in-Docker (DinD) container that provides an isolated Docker daemon inside k3s. Alongside it, a separate workspace container hosts the Docker CLI, NemoClaw tooling, installer logic, and socat proxy services. Both containers operate side-by-side within the same Pod, sharing the same localhost network namespace, Pod IP, and mounted volumes such as /var/run. This allows the workspace container to communicate directly with the Docker daemon exposed by the DinD container through localhost:2375 and shared Docker socket paths, enabling NemoClaw onboarding and container lifecycle operations to function seamlessly inside a containerd-based , k3s environment. The deployment follows Kubernetes’ native ownership hierarchy — Deployment → ReplicaSet → Pod — providing scalability, resiliency, and orchestration benefits while maintaining a fully self-contained Docker-enabled workspace. In parallel, the openwebui namespace hosts Open WebUI, Ollama, and the mistra:7b model.


## 📁 Project Structure


## 👥 Contributing

Contributions are welcome! Here's the standard flow:

1. **Fork** the repository
2. **Clone** your fork: `git clone https://github.com/your-username/repo.git`
3. **Branch**: `git checkout -b feature/your-feature`
4. **Commit**: `git commit -m 'feat: add some feature'`
5. **Push**: `git push origin feature/your-feature`
6. **Open** a pull request

Please follow the existing code style and include tests for new behavior where applicable.


