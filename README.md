# End-to-end process of On Premise Data Pipeline, API, and Reporting and Analytics

## **Phase 1: Creating a Virtual Environment and environment essentials**

<p>Version control is a way to track changes and documentation allowing us to backup and restore files if needed.

Virtual Environment is important due to dependency isolation and its reproducibility.
</p>

### Steps:
1. Created a local repository using git

2. Created a virtual environment
    ```
    python -m venv env
    # PowerShell
    env\Scripts\activate.ps1

    # cmd
    env\Scripts\activate.bat
    ```
3. Added packages: sqlalchemy, pandas, psycopg, pyarrow, schedule
    ```
    pip install -r requirements.txt
    ```

4. Setting up SSH Key in GitHub https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

5. Connected local repository to GitHub


## **Phase 2: Extracting data from web to FTP using Python**

<p>FTP or File Transfer Protocol is a way to download, upload, and transfer files from one location to another on the internet and between computer systems.

This section will setup an FTP server on WSL using vsftpd (on linux).
</p>

### Steps:

1. Installing the local WSL in your local PC using PowerShell

    ```
    wsl --install -d Ubuntu
    ```

2. Setting up the vsftpd:

    1. Switch to root user

        ```
        sudo su
        ```

    2. Install packages and fetch list of updates in Ubuntu

        ```
        apt update && apt upgrade
        ```

    3. Installing vsftpd in Ubuntu

        ```
        apt install vsftpd
        ```

    4. Copy vsftpd file to prevent any changes to the original file

        ```
        cp /etc/vsftpd.conf /etc/vsftpd.conf_original
        ```

    5. Updating the configuration to setup the local vsftpd

        ```
        local_enable=YES
        write_enable=YES
        chroot_local_user=YES
        chroot_list_enable=YES
        chroot_list_file=/etc/vsftpd.chroot_list
        ssl_enable=YES
        require_ssl_reuse=NO
        ```

    6. Restart to refresh changes in the file

        ```
        systemctl restart vsftpd
        ```

    7. Restricting user access to prevent any unwanted changes

        ```
        touch /etc/vsftpd.chroot_list
        ```

    8. Adding a user

        ```
        adduser ftpuser
        ```

    9. Creating a new file to be utilized by the newly added user

        ```
        mkdir /home/ftpuser/ftp
        ```

    10. Changing ownership to nobody for best practice and security measure

        ```
        chown nobody: /home/ftpuser/ftp
        ```

    11. Making the directory read-only for all users

        ```
        chmod a-w /home/ftpuser/ftp
        ```

    12. Adding the user to the directory

        ```
        echo "ftpuser" | sudo tee -a /etc/vsftpd.chroot_list
        ```

    13. Checking connection to the FTP

        ```
        wsl -u ftpuser
        ls /home/ftpuser/ -l
        ```

3. Adding FTP Environment Variables in `env/Activate.ps1`

    ```
    #FTP Environment Variables
    $Env:FTPHOST = "172.30.10.130"
    $Env:FTPUSER = "ftpuser"
    $Env:FTPPASS = "password"
    $Env:FTPPORT = "22"
    ```

4. Configuring the JSON, attaching URL link and identifying parameters.

5. Using Python, getting and authenticating the FTP, building the data pipeline, and scheduling the pipeline.
