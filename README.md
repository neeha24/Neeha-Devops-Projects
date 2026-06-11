# Jenkins - CI/CD Tool
Jenkins is an open-source automation server used primarily for continuous integration and continuous delivery/deployment (CI/CD) in software development.

<img width="721" height="775" alt="Screenshot 2026-06-10 at 9 40 35 AM" src="https://github.com/user-attachments/assets/cd62fd6c-bdc5-4cc8-b38e-3c4be6b0b123" />

<img width="1233" height="507" alt="Screenshot 2026-06-11 at 8 31 59 AM" src="https://github.com/user-attachments/assets/3294cb22-2b60-49cc-aa10-aaa96aaeb991" />



A developer pushes their changes to a Git repository. Jenkins is watching that repository, so the moment new code arrives it kicks off a pipeline of automated steps. First it builds the code (compiles it into something runnable), then it runs the test suite. If everything passes, it deploys the new version to a server. If something fails, instead of shipping broken code it alerts the developer, who fixes the problem and pushes again, starting the cycle over.
The whole point is that the purple box in the middle does all of this automatically. Without Jenkins, a person would have to manually compile, test, and deploy every change—tedious and error-prone. Jenkins turns that into a hands-off assembly line, which is why it's called an "automation server."


> ## Documentation Index
> Fetch the complete documentation index at: https://notes.kodekloud.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Devops Tools

> This guide explores essential DevOps tools and practices for developing, deploying, and monitoring applications from initial idea to production.

When diving into DevOps, you encounter a diverse set of tools, including Docker, Kubernetes, Ansible, Terraform, Git, GitHub, Jenkins, Prometheus, and Grafana. Although this array of technologies might initially seem overwhelming, this guide walks you through a real-world scenario that demonstrates how each tool plays a crucial role as your application and infrastructure evolve.

## From Idea to First Release

Every great project starts with an idea—imagine building a website that books tickets to Mars, helping users avoid long queues and high prices. As with any innovative project, you start writing code. Hours later, the first version is ready. However, this version is running on your local machine, accessible via HTTP on localhost (port 8080):

```python theme={null}
def book_my_ticket_to_mars():
    # world changing code here

# starts here
if __name__ == '__main__':
    book_my_ticket_to_mars()
```

Running your application locally is ideal for early testing, but it won’t suffice when you need to share it with the world. To make your application accessible 24/7, you must deploy it on a server—whether it’s a physical machine, a virtual machine, or a cloud instance. Simply copying the code isn’t enough; the server also requires the correct runtime environment (like Python or Java) and all necessary libraries with the proper versions. Here, your laptop serves as the development environment, while the server becomes the production environment.

Once deployed, users can access your application using the server’s IP address. However, an IP address isn’t always user friendly. That’s why you purchase a domain name and map it to your server.


## The Basic Workflow: Building and Deploying

Initially, your workflow consists of a few critical steps:

1. Develop the code on your local machine using an editor like VS Code or PyCharm.
2. Build the code by converting it into an executable format or binary (using tools such as Python setuptools, Maven, or Gradle).
3. Deploy the built executable to your production server.

A build script is often used to automate the build process:

```bash theme={null}
$ ./build.sh
$ ./app
```

## Scaling Collaboration with Git and GitHub

As your website gains popularity and your user base expands, you invite other developers to contribute. Each team member works on their local environment, which can lead to conflicts when multiple developers modify the same files. This is where Git, a version control system, becomes indispensable. It allows developers to pull the latest changes, merge their contributions, and push updates back to a shared repository.

Platforms like GitHub, GitLab, and Bitbucket act as cloud-based hubs that streamline code collaboration, version control, and project management. For example, common Git commands include:

```bash theme={null}
$ git pull
$ git push
```

At this stage, while Git manages version control, GitHub provides a centralized repository along with a user-friendly interface for project management.

## Introducing CI/CD for Automated Deployments

With multiple developers continuously pushing new features and bug fixes, manually transferring code between environments is inefficient and prone to errors. A dedicated build server is implemented to fetch the latest code, build it, and deploy it first to a testing environment. After successful tests, the new build is moved to production.

<Callout icon="lightbulb" color="#1CB2FE">
  Automating your deployment process reduces errors and accelerates feature delivery. Tools like Jenkins, GitHub Actions, and GitLab CI/CD enable continuous integration and deployment, facilitating multiple daily deployments and quicker feedback loops.
</Callout>

The automated pipeline typically works as follows:

* Code is pulled from the GitHub repository.
* The build server compiles the code.
* Automated tests run on the build.
* If tests pass, the executable is deployed to production.

## Packaging with Containers

Even with CI/CD automation, ensuring that applications run consistently across different environments requires precise dependency management. Manually installing every dependency on each server is inefficient and error-prone. Containers solve this problem by encapsulating your application and its dependencies in an image that can run uniformly across any system.

Docker is one of the most popular containerization technologies. Developers create a Dockerfile that describes the application environment. During the build process, Docker generates an image that can be deployed on any server:

```bash theme={null}
$ vi Dockerfile
$ docker build -t mars_ticket_app .
$ docker run -d -p 80:80 mars_ticket_app
```

Containers also provide process isolation, allowing multiple containers to run on the same server without interference.

## Scaling with Container Orchestration

As user demand grows, you may need to run containers on several servers, a process that becomes challenging to manage manually. Container orchestration platforms like Kubernetes help automate tasks such as starting new containers when demand increases, shutting them down when demand decreases, and restarting failed containers. Kubernetes automatically scales and manages containerized applications, ensuring consistency and efficiency across your infrastructure.

A typical workflow in this scenario involves developers pushing code to GitHub, CI/CD pipelines building Docker images, testing the application, and finally Kubernetes deploying the containers to production.

## Infrastructure as Code with Terraform

Manually provisioning servers to ensure they all have the same configuration is a complex and error-prone task. To address this, tools like Terraform allow you to define your infrastructure as code. Using manifest files, Terraform provisions new servers with consistent configurations, such as operating system versions, storage, kernel settings, and container runtimes.

Below is an excerpt from a Terraform manifest that provisions three servers with identical configurations:

```hcl theme={null}
resource "aws_instance" "node01" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"

  tags = {
    Name = "controlplane"
  }
}

resource "aws_instance" "node02" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"

  tags = {
    Name = "worker01"
  }
}

resource "aws_instance" "node03" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"

  tags = {
    Name = "worker02"
  }
}
```

Modifications to your infrastructure are made in the Terraform code and applied with:

```bash theme={null}
$ terraform apply
```

## Post-Provisioning Configuration with Ansible

Provisioning servers is only part of the equation. After your servers are up and running, they require further configuration—installing necessary software, configuring services, and adjusting system settings. Ansible automates these post-provisioning tasks through playbooks, which are version-controlled like any other code. While Terraform handles the provisioning, Ansible excels at configuring and managing software on your servers.

An example Ansible playbook for deploying a Kubernetes cluster might look like this:

```yaml theme={null}
---
# This playbook deploys the Kubernetes cluster.
- name: Apply common configuration to all nodes
  hosts: all
  remote_user: root
  roles:
    - common

- name: Configure and deploy the controlplane
  hosts: controlplane
  remote_user: root
  roles:
    - kubernetes_controlplane

- name: Configure and deploy worker nodes
  hosts: workers
  remote_user: root
  roles:
    - kubernetes_workers
```

## Monitoring Infrastructure with Prometheus and Grafana

Deploying and configuring your infrastructure is only half the battle; ongoing monitoring is essential to ensure everything runs smoothly. Prometheus gathers metrics such as CPU usage, memory consumption, and process statuses from your servers. Grafana then visualizes this data through dashboards, helping you quickly spot trends and potential issues.



