---
layout: post
title: Getting Started
author: Theron E. Weimer, Jr.
updated: 2021-03-10 11:51:00 -0500
---
## Get Jekyll working within VSCode

It doesn't appear that there is currently an extension for VSCode/VSCodium to Run a [Jekyll](https://jekyllrb.com/) site. However, [this](https://www.codemunki.es/2020/09/22/run-jekyll-vs-code-devcontainer/) [codemunkies](https://www.codemunki.es/) article (which references [this article by Carlos Mendible](https://carlos.mendible.com/2020/01/10/vs-code-remote-containers-jekyll/)) provided a great example of using [Docker](https://www.docker.com/) [Desktop](https://www.docker.com/products/docker-desktop) ([Windows docs](https://docs.docker.com/docker-for-windows/)) and [devcontainers](https://code.visualstudio.com/docs/remote/create-dev-container).

For reference, enable Windows Subsystem for Linux version 2 (WSL2) [documentation](https://docs.microsoft.com/en-us/windows/wsl/install-win10), and [integrate with Docker](https://docs.docker.com/docker-for-windows/wsl/).

1. Make sure that Windows Subsystem for Linux is enabled as well as the Virtual Machine feature.

    ```powershell
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
    ```

1. Install the [WSL2 kernel](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi).
1. Optional: Set WSL2 as the default.

    ```powershell
    wsl --set-default-version 2
    ```

1. Ensure that the WSL image installed is version 2.

    ```powershell
    wsl --list --verbose
    ```

    Convert an image to WSL2.

    ```powershell
    wsl --set-version <Distro> 2
    ```

1. Make sure the Windows 10 Hyper-V feature is enabled.  Reference the Microsoft documentation for [Windows containers](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-10) and [enable Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

    ```powershell
    Get-WindowsOptionalFeature -Online
    ```

    Note the feature name is "HypervisorPlatform" now.

    ```powershell
    Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
    ```

    or via ```Turn Windows features on and off```

1. Install Docker Desktop

    ```powershell
    choco install docker-desktop
    ```

    If the Chocolatey installer doesn't seem to install properly, use the installer from [hub.docker.com](https://hub.docker.com).

1. Enable Docker integration with WSL2 image.  When Docker Desktop starts, go to Settings > Resources > WSL Integration
1. Install the VSCode extension.  Currently, the [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension is not working in VSCodium, so it is necessary to use VSCode.
1. Create the `.devcontainer` directory and files.

    ```shell
    mkdir -p .devcontainer
    curl https://raw.githubusercontent.com/steve-codemunkies/steve-codemunkies.github.io/main/.devcontainer/Dockerfile --output .devcontainer/Dockerfile
    curl https://raw.githubusercontent.com/steve-codemunkies/steve-codemunkies.github.io/main/.devcontainer/devcontainer.json --output .devcontainer/devcontainer.json
    ```

1. Update the `Dockerfile` to support `bundler` for use with GitHub Pages.

    Added after `FROM`:

    ```docker
    # Necessary to run bundler within Docker.
    # https://bundler.io/guides/bundler_docker_guide.html
    ENV GEM_HOME="/usr/local/bundle"
    ENV PATH $GEM_HOME/bin:$GEM_HOME/gems/bin:$PATH
    ```

    Add after installing Jekyll:

    ```docker
    # [Optional] Uncomment this line to install bundler to use with GitHub Pages.
    RUN gem install bundler
    ```

1. Create the VSCode tasks file.

    ```shell
    mkdir -p .vscode
    curl https://raw.githubusercontent.com/steve-codemunkies/steve-codemunkies.github.io/main/.vscode/tasks.json --output .vscode/tasks.json
    ```

1. Edit the `tasks.json` file to suit the project.

    Use `bundler` to install necessary gems.

    ```json
    {
        "label": "Bundle Install",
        "command": "bundle install",
        "args": [],
        "type": "shell",
        "problemMatcher": []
    }
    ```

    Use `bundler` to run Jekyll.

    ```json
    "command": "bundle",
    "args": [
        "exec",
        "jekyll",
    ...
    ```

1. When VSCode prompts to "Reopen in a Container", do so and the image will be built.

*[WSL]: Windows Subsystem for Linux
*[WSL2]: Windows Subsystem for Linux version 2
