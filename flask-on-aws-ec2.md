# Run Flask App on AWS EC2 Instance

Install Python Virtualenv
```bash
sudo apt-get update
sudo apt-get install python3-venv
```
Activate the new virtual environment in a new directory

Create directory
```bash
mkdir helloworld
cd helloworld
```
Create the virtual environment
```bash
python3 -m venv venv
```
Activate the virtual environment
```bash
source venv/bin/activate
```
Install Flask
```bash
pip install Flask
```
Create a Simple Flask API
```bash
sudo vi app.py
```
```bash
// Add this to app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
	return 'Hello World!'

if __name__ == "__main__":
	app.run()
```
Verify if it works by running 
```bash
python app.py
```
Run Gunicorn WSGI server to serve the Flask Application
When you “run” flask, you are actually running Werkzeug’s development WSGI server, which forward requests from a web server.
Since Werkzeug is only for development, we have to use Gunicorn, which is a production-ready WSGI server, to serve our application.

Install Gunicorn using the below command:
```bash
pip install gunicorn
```
Run Gunicorn:
```bash
gunicorn -b 0.0.0.0:8000 app:app 
```
Gunicorn is running (Ctrl + C to exit gunicorn)!

Use systemd to manage Gunicorn
Systemd is a boot manager for Linux. We are using it to restart gunicorn if the EC2 restarts or reboots for some reason.
We create a <projectname>.service file in the /etc/systemd/system folder, and specify what would happen to gunicorn when the system reboots.
We will be adding 3 parts to systemd Unit file — Unit, Service, Install

Unit — This section is for description about the project and some dependencies
Service — To specify user/group we want to run this service after. Also some information about the executables and the commands.
Install — tells systemd at which moment during boot process this service should start.
With that said, create an unit file in the /etc/systemd/system directory
	
```bash
sudo nano /etc/systemd/system/helloworld.service
```
Then add this into the file.
```bash
[Unit]
Description=Gunicorn instance for a simple hello world app
After=network.target
[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/helloworld
ExecStart=/home/ubuntu/helloworld/venv/bin/gunicorn -b localhost:8000 app:app
Restart=always
[Install]
WantedBy=multi-user.target
```
Then enable the service:
```bash
sudo systemctl daemon-reload
sudo systemctl start helloworld
sudo systemctl enable helloworld
```
Check if the app is running with 
```bash
curl localhost:8000
```
