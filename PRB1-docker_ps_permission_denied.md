**Problem1**

bash-
  docker ps 
  
  permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.49/containers/json": dial unix /var/run/docker.sock: connect: permission denied

This indicates that your current user does not have permission to communicate with the Docker daemon, which usually runs with root privileges.

## Solution: Add Your User to the `docker` Group

The most common and recommended solution is to add your user to the `docker` group. This grants your user the necessary permissions to interact with the Docker daemon without needing `sudo` for every command.

**Steps:**

1.  **Add your user to the `docker` group:**

    Open your terminal and run the following command. Replace `$USER` with your actual username if needed.

    ```bash
    sudo usermod -aG docker $USER
    ```

    * `sudo`: Executes the command with superuser privileges.
    * `usermod`: A command-line utility for modifying user account information.
    * `-aG docker`: Adds the user to the `docker` group without removing them from any other groups they belong to.
    * `$USER`: An environment variable that typically holds your current username.

2.  **(Sometimes Necessary) Change the ownership of the Docker socket:**

    In some cases, you might also need to change the ownership of the Docker socket file to your user.

    ```bash
    sudo chown $USER:$USER /var/run/docker.sock
    ```

    This ensures your user has direct ownership of the communication socket.

3.  **Restart your shell or log out and log back in:**

    For the group membership changes to take effect, you need to start a new session. Close your current terminal and open a new one, or log out of your Linux system and log back in.

4.  **Verify Docker access:**

    After logging back in, try running a Docker command without `sudo`, such as:

    ```bash
    docker ps
    ```

    If the error is resolved, you should see the output of the command (e.g., a list of running containers).

## Alternative (Less Recommended for Regular Use): Using `sudo`

You can always run Docker commands with `sudo` to execute them with root privileges. However, this can be inconvenient for regular use.

```bash
sudo docker ps
sudo docker run ...
