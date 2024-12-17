# ipa-to-ipa-trust-demo
This project showcases the establishment of a trust among multiple FreeIPA deployments, enabling integration and resource sharing across independent ephemeral domains.

## Pre-requisites
To run the IPA-to-IPA Trust Demo and related scenarios, you will need a Fedora host with the following requirements:

### System Requirements  
- **Operating System**: Fedora (latest stable version recommended).
- **RAM**: At least **16 GB** of RAM to ensure smooth operation of the Podman clusters. More RAM is recommended for complex deployments.
- **Storage**: Sufficient disk space to accommodate container images, deployment configurations, and video recordings.


### Purpose of the Fedora Host  
1. **Podman Clusters Deployment**  
   - The Fedora host serves as the deployment environment for **Podman clusters** simulating FreeIPA servers, clients, and replicas.  
   - These clusters enable testing scenarios such as *FreeIPA-to-FreeIPA Trust* and *FreeIPA-to-FreeIPA Migration*.  

2. **Automated Video Recording**  
   - The host is also used to record demonstration videos using [VHS](https://github.com/charmbracelet/vhs), a tool for automating terminal recordings.  
   - Predefined scripts included in the `.tape` folder allow you to generate videos showcasing deployment steps, trust establishment, and policy configurations.  


## Installation

### Installing System Dependencies

```bash
dnf copr enable -y abbra/vhs
```

```bash
sudo dnf install -y git-core ansible ansible-collection-containers-podman podman gcc libvirt-devel krb5-devel python3-devel podman-compose vhs
```

### Usage

1. **Clone the Repository**

    ```bash
    git clone https://github.com/f-trivino/ipa-to-ipa-trust-demo.git
    ```

2. **Install ipalab-config and ansible-freeipa**

    Usage of Python's virtual environment is encouraged:
    ```bash
    python3 -m venv ipalab && cd ipalab && source bin/activate
    pip install "ansible-core<2.18"
    pip install ipalab-config
    ansible-galaxy collection install freeipa.ansible_freeipa
    ```

3. **Deploy the multihost clusters and deploy FreeIPA**

    The tool ipalab-config will generate all needed files.
    ```bash
    ipalab-config -f ../image/containerfile ../multihosts/ipa1demo-ipa2demo.clusters && cd ipa1demo-ipa2demo
    podman-compose up -d
    ansible-playbook -i inventory.yml playbooks/install-cluster.yml
    ```

4. **Play with the development preview feature**

    In this example you can see how to establish trust between two different IPA deployments.
    ![Establish IPA Trust](./videos/establish-trust.webm)

## Ansible

You can also develop your own Ansible playbooks to automate more complex configurations. Use standard Ansible playbooks to interact with the IPA deployments. In this example, an IPA trust is established between the ipa1demo and ipa2demo clusters:
    ```bash
    ansible-playbook -i inventory.yml ../data/establish-trust.yaml
    ```

## Troubleshooting

Make modifications on the development preview image:
```bash
compose down
compose up -d --build
```

Disaster recovery:
```bash
podman stop --all
podman system prune --all --force && podman rmi --all
```

ansible-core issues:
```bash
pip install --force 'ansible-core < 2.18'
```

How to connect to the containers:
```bash
podman ps 
podman exec -it m1 /bin/bash
```

# Credits
This demo project would not have been possible without the contributions and efforts of the following individuals and tools:

ipalab-config: Developed by Rafael Jeffman (@rjeffman), a key component in managing and configuring environments within this demo.

ipa-to-ipa trust work: Pioneered by Alexander Bokovoy, whose work has been instrumental in enabling IPA-to-IPA trust scenarios and improving overall IdM capabilities.
