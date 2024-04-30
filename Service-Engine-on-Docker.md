# Service Engine on Docker - Getting Started

The zenon Service Engine is also available as Ubuntu based Docker image. This image includes both, the Service Engine and the Logic Service and is available for the amd64 architecture only.

## Requirements

**System requirements**
- A machine with a working Docker installation and the ability to run amd64 Linux Docker containers
- A working internet connection for downloading the Docker image.

**License Requirements**
- A Windows based machine with the zenon License Manager installed and an available network license.  
**Important:** Do not attempt to use a Linux based machine, as the required zenon specific licensing services are only available on Windows. Install it using the zenon install media for Windows.
- This machine acts as license server and the license needs to be shared for network access. Therefore this machine must be accessible from the Linux machine in order to borrow a license.
- Make sure to have a suitable license with the desired functionality for the intended use case (e.g. Service Engine, Drivers, Process Gateways) and activate it on the license server.
- See [zenon Online Help](https://onlinehelp.copadata.com/) in the licensing section for more information on these topics


## Configuration and Startup 

To run the Service Engine as Docker container on Linux, perform the following steps:

1. Clone this repo or download it and extract it to a location of your choice.
2. Note that the docker-compose folder already has two directories in it. `data` and `logs`.  
These are referenced as volumes in the `.env` file
    - The data directory includes the zenon6.ini and a dummy project
        - `zenon6.ini` defines startup-parameters of the zenon Service Engine
        - `DUMMY_PROJECT_14` is an empty zenon project. Compiled in the zenon Engineering Studio
    - The logs directory is empty
        - The zenon logging server will write it's log files into here
3. Adapt the content of `.env` and provide the necessary configuration:
    - Provide the appropriate license serial and license server for the components needed
    - Optional: Provide your own configuration folder `data` via the variable `CONFIG_PATH`
    - Optional: Provide your own logging folder `logs` via the variable `LOGGING_PATH`  

    **Follow steps 4-5 to load your own project**  

4. Compile your own zenon project in the Engineering Studio on your host machine and copy the project folder (e.g. "C:\Users\Public\Documents\zenon_Projects\<Project Name>") to the directory `data`.
5. Edit the file `zenon6.ini` in the directory `data` to match your own project:  
Pay attention that the folder path is case sensitive in Linux.
    ```ini
    [PATH]
    VBF30=/etc/copa-data/<Project Name>

    [DEFAULT]
    DEFANWENDUNG30=<Project Name>
    ```
6. Pull and start the Service Engine using `docker compose up`

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
- Check if the configured project functionality is supported by the Linux Service Engine. Please consult the  [zenon Online Help](https://onlinehelp.copadata.com/) for this.

## License errors
In case of license errors, check the following things:
- Check if the license server and the license serial are correct.
- Verify that the license server is accessible and the license can actually be used (e.g. by means of a Windows Service Engine).
- Verify via Codemeter WebAdmin that enough licenses are available on the License Server and that they are not occupied by any zenon services running on different machines.
- If the License Server and Service Engine machines are connected with each other via VPN, you might need to restart the Codemeter Service on the License Server to ensure the Codemeter Service also listens on the VPN interface.

## See the logs
To check the logs of the Service Engine use the Diagnosis Viewer and connect it to the zenon Logging Server operated next to the Service Engine in a separate Docker container.
- Use `localhost` and port `50780`
- You may need to stop the zenon logging server on your local machine, prior to connecting to the Docker based logging server.
