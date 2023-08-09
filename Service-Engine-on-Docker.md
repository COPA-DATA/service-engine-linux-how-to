# Service Engine on Docker - Getting Started

The zenon Service Engine is also available as Ubuntu based Docker image. This image includes both, the Service Engine and the Logic Service and is available for the amd64 architecture only.

## Requirements

**System requirements**
- A machine with a working Docker installation and the ability to run amd64 Linux Docker containers
- A working internet connection for downloading the Docker image.

**License Requirements**
- A machine with the zenon License Manager installed and an available network license
- Make sure to have a suitable license with the desired functionality (drivers, process gateways) for the intended use case.
- This machine acts as license server and the license needs to be shared for network access. Therefore this machine must be accessible from the Linux machine in order to retrieve a license.
- See [zenon Online Help](https://onlinehelp.copadata.com/) in the licensing section for more information on this topic


## Use Docker compose

To run the Service Engine as Docker container on Linux, perform the following steps:

1. Use the files `.env` and `docker-compose.yml` and copy them to a local folder of your choice.  
**Hint:** You can also find them as a package in the download section of the [COPA-DATA homepage](https://www.copadata.com/de/downloads/product-downloads/).
2. Create a `data` folder on your host machine, which will be used to store the zenon project and the configuration files of the Service Engine.  
    **Hint:** You can create a folder called `data` next to the file `docker-compose.yml`.
3. Create a `logs` folder on your host machine, which will be used to store the zenon logs.  
    **Hint:** You can create a folder called `logs` next to the file `docker-compose.yml`.    
4. Adapt the content of `.env` and provide the necessary configuration
    - Use the appropriate license serial and license server
    - Provide the configuration folder via the variable `CONFIG_PATH`
    - Provide the desired logging folder via the variable `LOGGING_PATH`
5. Compile your zenon project in the Engineering Studio and copy the project folder (e.g. "C:\Users\Public\Documents\zenon_Projects\LINUX_TEST") to the directory `data`.
6. Create a file called `zenon6.ini` in the directory `data` with the following content:
    ```ini
    [PATH]
    VBF30=/etc/copa-data/<Project Name>

    [DEFAULT]
    DEFANWENDUNG30=<Project Name>
    ```
7. Adapt the file `zenon6.ini` and use the appropriate project name for the placeholder `<Project Name>`.
**Pay attention that the folder path is case sensitive in Linux.**
8. Pull the Service Engine image with the command `docker compose pull`
9. Start the Service Engine using `docker compose up`

**Congratulations, you are running the Service Engine on Linux using docker.**

You can see the logs in the command line output or via `docker logs service-engine`. If your zenon project contains a logic project and the Docker container is configured to listen on `1200`, you can also connect to the Logic Service using the Logic Studio via the local port `1200`.
If the Service Engine cannot find the configured license or project, it will report it in the logs and will terminate.

**Hint:** The file `License.ini` in the directory `data` is automatically created when the Service Engine starts. It contains the license information passed as environment variable to the container.

# Troubleshooting

In case of troubles make sure to have a look at the troubleshooting section below.  
Reach out to support@copadata.com, if you need further assistance.

## Service Engine terminates with error -1 / load of project failed
Verify the following things:
- Check if the mounted volume is configured correctly in `docker-compose.yml`
- Check that the zenon project is placed in the correct host folder and if the folder is mounted successfully to the docker container.
- Verify the correct project name and path in `zenon6.ini`. Pay attention to case sensitivity of files and folder names in Linux.

## License errors
In case of license errors, check the following things:
- Check if the license server and the license serial are correct.
- Verify that the license server is accessible and the license can actually be used (e.g. by means of a Windows Service Engine).

## See the logs
To check the logs of the Service Engine use the Diagnosis Viewer and connect it to the zenon Logging Server operated next to the Service Engine in a separate Docker container.
- Use `localhost` and port `50780`
- You may need to stop the zenon logging server on your local machine, prior to connecting to the Docker based logging server.