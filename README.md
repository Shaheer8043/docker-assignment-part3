\# Docker Assignment 1 — Part 3: Docker Volumes



\## 1. Introduction



The objective of Part 3 is to demonstrate Docker volume creation, data persistence, and sharing files between multiple containers using a named Docker volume.



In this assignment, a volume named `my\_volume` is shared between Nginx and Apache web server containers.



\## 2. Check Existing Docker Volumes



\*\*Command:\*\*



```powershell

docker volume ls

```



\*\*Description:\*\* Lists the Docker volumes available on the local machine.



\*\*Result:\*\* The existing volumes were displayed, including `mysql-vol`. A separate volume named `my\_volume` was created for this assignment.



\## 3. Create a Docker Volume



\*\*Command:\*\*



```powershell

docker volume create my\_volume

```



\*\*Description:\*\* Creates a named Docker volume called `my\_volume` to store data independently of a container's lifecycle.



\*\*Expected output:\*\*



```text

my\_volume

```



\*\*Result:\*\* The volume was created successfully.



\## 4. Inspect the Docker Volume



\*\*Command:\*\*



```powershell

docker volume inspect my\_volume

```



\*\*Description:\*\* Displays detailed information about the volume, including its name, driver, mount point, and scope.



\*\*Observed results:\*\*



\- Name: `my\_volume`

\- Driver: `local`

\- Scope: `local`



\*\*Result:\*\* The volume information was displayed successfully.



\## 5. Create an HTML File



\*\*Command:\*\*



```powershell

mkdir site

```



\*\*Description:\*\* Creates a directory named `site` in the Part 3 project folder.



\*\*Command:\*\*



```powershell

Set-Content site\\index.html "<h1>Hello from my\_volume!</h1>"

```



\*\*Description:\*\* Creates an HTML file containing a test message.



\*\*Verification command:\*\*



```powershell

Get-Content site\\index.html

```



\*\*Expected output:\*\*



```html

<h1>Hello from my\_volume!</h1>

```



\*\*Result:\*\* The HTML file was created and verified successfully.



\## 6. Copy the HTML File into the Docker Volume



\*\*Command:\*\*



```powershell

docker run --rm -v my\_volume:/data -v "${PWD}\\site:/source:ro" alpine sh -c "cp /source/index.html /data/index.html"

```



\*\*Description:\*\* Starts a temporary Alpine container, mounts `my\_volume` at `/data`, mounts the local `site` directory as read-only at `/source`, and copies `index.html` into the named volume.



The `--rm` option automatically removes the temporary container after the command finishes.



\*\*Verification command:\*\*



```powershell

docker run --rm -v my\_volume:/data alpine cat /data/index.html

```



\*\*Expected output:\*\*



```html

<h1>Hello from my\_volume!</h1>

```



\*\*Result:\*\* The file was copied into the named volume and read back successfully.



\## 7. Run the Nginx Container



\*\*Command:\*\*



```powershell

docker run -d --name nginx-volume-test -p 8081:80 -v my\_volume:/usr/share/nginx/html:ro nginx

```



\*\*Description:\*\* Creates and starts an Nginx container with the following configuration:



\- Container name: `nginx-volume-test`

\- Host port: `8081`

\- Container port: `80`

\- Shared volume mount: `my\_volume`

\- Mount path: `/usr/share/nginx/html`

\- Mount mode: Read-only (`ro`)



\*\*Verification command:\*\*



```powershell

docker ps

```



\*\*Application URL:\*\*



http://localhost:8081



\*\*Expected result:\*\* The browser displays `Hello from my\_volume!`.



\## 8. Run the Apache Container



\*\*Command:\*\*



```powershell

docker run -d --name apache-volume-test -p 8082:80 -v my\_volume:/usr/local/apache2/htdocs:ro httpd

```



\*\*Description:\*\* Creates and starts an Apache HTTP Server container using the same named volume.



\- Container name: `apache-volume-test`

\- Host port: `8082`

\- Container port: `80`

\- Shared volume mount: `my\_volume`

\- Mount path: `/usr/local/apache2/htdocs`

\- Mount mode: Read-only (`ro`)



\*\*Verification command:\*\*



```powershell

docker ps

```



\*\*Application URL:\*\*



http://localhost:8082



\*\*Expected result:\*\* The browser displays `Hello from my\_volume!`.



\## 9. Verify the Shared File in Nginx



\*\*Command:\*\*



```powershell

docker exec nginx-volume-test cat /usr/share/nginx/html/index.html

```



\*\*Description:\*\* Executes the `cat` command inside the Nginx container to read the HTML file mounted from `my\_volume`.



\*\*Expected output:\*\*



```html

<h1>Hello from my\_volume!</h1>

```



\## 10. Verify the Shared File in Apache



\*\*Command:\*\*



```powershell

docker exec apache-volume-test cat /usr/local/apache2/htdocs/index.html

```



\*\*Description:\*\* Executes the `cat` command inside the Apache container to read the same HTML file from `my\_volume`.



\*\*Expected output:\*\*



```html

<h1>Hello from my\_volume!</h1>

```



\## 11. Results and Conclusion



The assignment demonstrates how Docker named volumes allow data to be stored independently of individual containers and shared between different containers.



The `my\_volume` volume stores `index.html`. Both Nginx and Apache mount this volume at their respective document-root directories.



Successful access to the same file through both containers demonstrates shared data access using a Docker volume.



The Nginx and Apache mounts use read-only mode, which prevents the web server containers from modifying the mounted files.



\## 12. Technologies Used



\- Docker

\- Docker volumes

\- Nginx

\- Apache HTTP Server

\- Alpine Linux

\- PowerShell

\- HTML

