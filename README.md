# Run MATLAB in GitHub Codespaces

This repository shows how to run MATLAB&reg; in [GitHub&trade; Codespaces](https://github.com/features/codespaces).

## Get Started

A [codespace (GitHub Docs)](https://docs.github.com/en/codespaces/overview) is a development environment you can run in the cloud. Codespaces run in Docker containers called development containers, or [dev containers (GitHub Docs)](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers). You can customize your codespace by modifying `devcontainer.json`, the configuration file of the dev container.

The examples in this repository show different ways you can configure your dev containers to run MATLAB in codespaces. For each example, you can find the corresponding `devcontainer.json` configuration file in the [.devcontainer](./.devcontainer) folder.

1. [Using the MATLAB Image on Docker Hub](#using-the-matlab-image-on-docker-hub)
2. [Using the MATLAB Image on Docker Hub and MATLAB Proxy](#using-the-matlab-image-on-docker-hub-and-matlab-proxy) 
3. [Using the MATLAB Devcontainer Feature](#using-the-matlab-devcontainer-feature) 
4. [Using MATLAB with Jupyter](#using-matlab-with-jupyter)
5. [Using MATLAB Dockerfile](#using-MATLAB-dockerfile)



## Using the MATLAB Image on Docker Hub

To run a dev container with MATLAB using the [MATLAB Image on Docker Hub](https://hub.docker.com/r/mathworks/matlab), use this `devcontainer.json` configuration:
 
```json
{
  "name": "MATLAB",
  "image": "mathworks/matlab:latest",
  "waitFor": "updateContentCommand",
  "updateContentCommand": {
    "install-git": "sudo apt-get update && sudo apt-get install git -y",
  },
}
```

## Using the MATLAB Image on Docker Hub and MATLAB Proxy

To run a dev container with the [MATLAB Image on Docker Hub](https://hub.docker.com/r/mathworks/matlab) and [`matlab-proxy`](https://github.com/mathworks/matlab-proxy), which starts MATLAB in a browser tab, use this `devcontainer.json` configuration:

```json
{
  "name": "MATLAB",
  "image": "mathworks/matlab:latest",
  "hostRequirements": {
    "cpus": 4
  },
  "portsAttributes": {
    "8888": {
      "label": "MATLAB",
      "onAutoForward": "openBrowser"
    }
  },
  "waitFor": "updateContentCommand",
  "updateContentCommand": {
    "install-git": "sudo apt-get update && sudo apt-get install git -y",
    "update-matlab-proxy": "sudo python3 -m pip install --upgrade pip matlab-proxy"
  },
  "postStartCommand": "run.sh -browser"
}
```

The `postStartCommand` starts [matlab-proxy](https://github.com/mathworks/matlab-proxy) and `onAutoForward` opens a browser tab running MATLAB.

Note: Depending on your system configuration, you might need to click on the link presented in the VSCode terminal to start the browser session.

You can run the dev container configured above in Codespaces:

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new/mathworks-ref-arch/matlab-codespaces?template=false&devcontainer_path=.devcontainer%2Fdevcontainer.json)


## Using the MATLAB Devcontainer Feature

You can add functionality to your dev containers by adding self-contained units of code called [Features (GitHub)](https://github.com/devcontainers/features). For example, to install MATLAB `R2024a` in your dev container with the [MATLAB Feature (GitHub)](https://github.com/mathworks/devcontainer-features), use this `devcontainer.json` configuration:

```json
{
    "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
    "features": {
        "ghcr.io/mathworks/devcontainer-features/matlab": {}
    }
}
```

This configuration installs the MATLAB Feature on a `ubuntu` base image, with default settings. To customize the settings, modify the [MATLAB Feature Options (GitHub)](https://github.com/mathworks/devcontainer-features/tree/main/src/matlab#options). For example, to install MATLAB `R2023a` instead of `R2024a`, as well as the Symbolic Math Toolbox, use this configuration:

```json
{
    "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
    "features": {
        "ghcr.io/mathworks/devcontainer-features/matlab": {
            "release": "r2023a",
            "products": "MATLAB Symbolic_Math_Toolbox"
        }
    }
}   
```

You can run this dev container in Codespaces: 

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new/mathworks-ref-arch/matlab-codespaces?template=false&devcontainer_path=.devcontainer%2Fusing-devcontainer-feature%2Fdevcontainer.json)





## Using MATLAB with Jupyter

To use MATLAB in JupyterLab, running in codespaces, you can configure your dev container to include [MATLAB Integration for Jupyter](https://github.com/mathworks/jupyter-matlab-proxy). Use one of these methods:


1. Use the MATLAB Integration for Jupyter image:

  ```json
  {
  "name": "R2024a MATLAB Integration for Juptyer Prebuilt Image",
  "image": "ghcr.io/mathworks-ref-arch/matlab-integration-for-jupyter/jupyter-matlab-notebook:r2024a",
  "hostRequirements": {
    "cpus": 4
    }
  }
  ```

2. Use the [MATLAB Integration for Jupyter Dockerfile (GitHub)](https://github.com/mathworks-ref-arch/matlab-integration-for-jupyter/blob/main/matlab/Dockerfile). Customize your `args` as desired:
  ```json
  {
      "name": "MATLAB Integration for Jupyter Dockerfile",
      "build": {
          "dockerfile": "Dockerfile",
          "args": {
              "MATLAB_RELEASE": "r2023b",
              "MATLAB_PRODUCT_LIST": "MATLAB Symbolic_Math_Toolbox",
              "PYTHON_VERSION": "3.10"
          }
      },
      "hostRequirements": {
          "cpus": 4
      }
  }
  ```

3. Use the MATLAB Feature and customize the [MATLAB Feature Options (GitHub)](https://github.com/mathworks/devcontainer-features/tree/main/src/matlab#options) to install MATLAB, Python, JupyterLab, and the MATLAB Integration for Jupyter. For example:


  ```json
  {
      "name": "Using MATLAB with Jupyter",
      "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
      "hostRequirements": {
          "cpus": 4
      },
      "features": {
          "ghcr.io/mathworks/devcontainer-features/matlab": {
              "release": "r2024a",
              "products": "MATLAB Symbolic_Math_Toolbox",
              "installJupyterMatlabProxy": true,
              "installMatlabEngineForPython": true
          },
          "ghcr.io/devcontainers/features/python": {
              "version": "os-provided",
              "installJupyterlab": true,
              "configureJupyterlabAllowOrigin": "*"
          }
      }
      ,"containerUser": "codespace"
  }
  ```

You can run the dev container configured above in Codespaces: 

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new/mathworks-ref-arch/matlab-codespaces?template=false&devcontainer_path=.devcontainer%2Fusing-matlab-with-jupyter%2Fdevcontainer.json)




## Using MATLAB Dockerfile

To run a dev container using the [MATLAB Dockerfile](https://github.com/mathworks-ref-arch/matlab-dockerfile) and [`matlab-proxy`](https://github.com/mathworks/matlab-proxy), use this `devcontainer.json` configuration:

```json
{
    "name": "Using matlab-dockerfile",
    "build": {
        // See: https://github.com/mathworks-ref-arch/matlab-dockerfile
        "dockerfile": "Dockerfile",
        "args": {
            "MATLAB_RELEASE": "r2024a",
            "MATLAB_PRODUCT_LIST": "MATLAB Symbolic_Math_Toolbox"
        }
    },
    "hostRequirements": {
        "cpus": 4
    },
    "portsAttributes": {
        "8888": {
            "label": "MATLAB",
            "onAutoForward": "openBrowser"
        }
    },
    "waitFor": "updateContentCommand",
    "updateContentCommand": {
        "install-git-and-proxy": "sudo apt-get update && sudo apt-get install --no-install-recommends -y git python3 python3-pip xvfb && sudo python3 -m pip install --upgrade matlab-proxy"
    },
    "postStartCommand": "env MWI_APP_PORT=8888 matlab-proxy-app"
}
```

You can run this dev container in Codespaces: 

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new/mathworks-ref-arch/matlab-codespaces?template=false&devcontainer_path=.devcontainer%2Fusing-matlab-dockerfile%2Fdevcontainer.json)

## Related Links

* [Overview of Codespaces (GitHub)](https://docs.github.com/en/codespaces/overview)
* [Development Container Features (GitHub)](https://github.com/devcontainers/features/)
* [Development Container Specification (Microsoft&reg;)](https://containers.dev/implementors/spec/)
* [Setting Your Default Editor for Codespaces (GitHub)](https://docs.github.com/en/codespaces/setting-your-user-preferences/setting-your-default-editor-for-github-codespaces)
* [Run Dev Containers in VS Code (VS Code Docs) ](https://code.visualstudio.com/docs/devcontainers/create-dev-container)
----

Copyright 2024 The MathWorks, Inc.

----