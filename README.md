graph TD
    subgraph "Developer's Local Machine"
        dev[Developer]
    end

    subgraph "External Services"
        github[("GitHub Repositories<br>- jenkins-pipeline-app<br>- jenkins-shared-lib<br>- my-app-manifests")]
    end

    subgraph "AWS Cloud (us-east-1)"
        subgraph "IAM"
            iam_user["IAM User<br>(devops-project-admin)"]
            iam_role["IAM Role<br>(EBS_CSI_DriverRole)"]
        end

        subgraph "EC2 Compute"
            jenkins_master["EC2: Jenkins Master<br>(t3.medium)"]
            jenkins_agent["EC2: Jenkins Agent<br>(t3.medium)"]
        end

        subgraph "EKS Cluster (my-devops-cluster)"
            eks_cp["EKS Control Plane"]
            subgraph "Worker Nodes (t3.medium)"
                argo["ArgoCD Pods"]
                monitoring["Prometheus & Grafana Pods"]
                app["Deployed Application Pods"]
            end
        end

        subgraph "Storage & Registry"
            ecr["ECR Repository<br>(my-ubuntu-app)"]
            ebs["EBS Volumes<br>(For Prometheus)"]
        end
    end

    dev -- "git push" --> github
    github -- "Webhook" --> jenkins_master
    jenkins_master -- "Delegates Job" --> jenkins_agent
    jenkins_agent -- "Builds & Pushes Image" --> ecr
    jenkins_agent -- "Updates Manifests" --> github
    argo -- "Monitors Repo" --> github
    argo -- "Deploys App" --> app
    app -- "Pulls Image" --> ecr
    monitoring -- "Scrapes Metrics" --> app
    monitoring -- "Uses Storage" --> ebs
    iam_role -- "Grants Permission" --> ebs

# Welcome to Ubuntu Console Management

## How can I edit this code?

There are several ways of editing your application.
**Use your preferred IDE**

If you want to work locally using your own IDE, you can clone this repo and push changes.

The only requirement is having Node.js & npm installed - [install with nvm](https://github.com/nvm-sh/nvm#installing-and-updating)

Follow these steps:

```sh
# Step 1: Clone the repository using the project's Git URL.
git clone <YOUR_GIT_URL>

# Step 2: Navigate to the project directory.
cd <YOUR_PROJECT_NAME>

# Step 3: Install the necessary dependencies.
npm i

# Step 4: Start the development server with auto-reloading and an instant preview.
npm run dev
```

**Edit a file directly in GitHub**

- Navigate to the desired file(s).
- Click the "Edit" button (pencil icon) at the top right of the file view.
- Make your changes and commit the changes.

**Use GitHub Codespaces**

- Navigate to the main page of your repository.
- Click on the "Code" button (green button) near the top right.
- Select the "Codespaces" tab.
- Click on "New codespace" to launch a new Codespace environment.
- Edit files directly within the Codespace and commit and push your changes once you're done.

## What technologies are used for this project?

This project is built with:

- Vite
- TypeScript
- React
- shadcn-ui
- Tailwind CSS
