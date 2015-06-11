# Docker Workshop
In this workshop you will learn the basics of `docker`, `docker-compose`, and `Swagger` through
creating a small collection of microservices and writing documentation/tests for them. No Django or
Python experience is assumed, but it is helpful. Code for the web applications is written in Python,
but for the purposes of this workshop mostly involves editing configuration-like files.

## Summary of Workshop
You will build/complete a django web application (written in Python). The core logic has been
written for you, the majority of the areas to fill in are related to configuration. The goal isn't
to learn `django`, it is to learn `docker`.

### Backend JSON API
The backend API is nothing more than a thin wrapper around a third party JSON API, in this case
Google's geocoding API. For this demo we will be using the Google Maps geocode API.

Completing this app will require passing environment variables/configuration files around
correctly so that the secret API key is not in version control, but can be accessed by the app.
The app itself will be one Docker container.

Additionally, since APIs are commonly rate limited, you should cache requests made to external APIs.
It is good practice to enable caching for requests made to your backend API. This will be done using
an in memory cache called Redis and its accompanying package for python/django.

### Frontend JSON API
The frontend API should receive information from the user, make a request to backend, then return
the result processed in some way. This will require: knowing the host of the backend api, and
enabling communication to it via docker. The frontend will also have a basic user system to
demonstrate how to setup Docker to work with databases (in this case Postgres).

### Nginx reverse proxy
As with most web applications, things should be behind a reverse proxy of some variety. In this
workshop you will learn how Nginx can be configured to accomplish this.

### Swagger Docs and Testing
Its standard practice after implementing an API to test and/or document it. Unfortunately,
documentation has a tendency to fall out of sync with the code..

Swagger provides two powerful capabilities that help prevent this from happening:

1. Expressive and descriptive language for specifying the schema for JSON APIs
2. Based on that schema it can generate client API libraries in a variety of languages.

To insure that documentation, tests, and code are in sync, you can write Swagger docs, generate
API clients, then use that exclusively for writing integration tests. This enforces that the three
are always in sync.

# Installation and Setup
In this section the goal is to:

* Install Docker, docker-compose, and boot2docker.

## Terminology
Lets get some terminology out of the way
* Docker daemon: background service which manages running containers
* Docker host: the machine running the Docker daemon
* Docker client: the machine/process executing docker commands (eg build, run)

## Install boot2docker
Docker does not run natively on OSX. To work around that, boot2docker seamlessly sets up a
VirtualBox machine running a distribution of Linux which runs Docker. It also correctly maps
settings so that it can be accessed seamlessly from your OSX machine.

[Install boot2docker](http://boot2docker.io/)

Useful tips:
* By default, the docker host is reachable by the ip address of the VirtualBox VM. This can get
annoying. You can improve this by using `echo $DOCKER_HOST`, extracting the ip address, then
putting an entry in `/etc/hosts` that looks like `192.168.59.103 drydock`. This will map the host
`drydock` to the VirtualBox VM, making testing things such as web applications in the browser
nicer
* Useful commands: boot2docker up, boot2docker down, boot2docker ip. These can run/stop the VM
and print its ip address

## Install Docker
Now that there is a Docker host/daemon running, you need to install the client application so you
can manage/use it. Installation instructions can be found at
[docker.io](https://docs.docker.com/installation/).

## Install docker-compose
The final thing you need to install is docker-compose. This is a tool which uses configuration file
to coordinate running ordinary Docker commands. Everything docker-compose does, can be done with
regular Docker commands, but it makes it much easier.

[Install docker-compose](https://docs.docker.com/compose/install/)

NOTE: if you run into permissions issues, download the binary using curl to your downloads
directory, then rename it to docker-compose, chmod it, then move it to `/usr/local/bin`.

## Install Python
Although we will be working mostly within Docker containers, it would be helpful to have a recent
Python installation. For Macs, you can run `brew install python`. Also make sure that Python's
package manager (pip) is installed. When this is done, execute `pip install -r requirements.txt`
within this directory. If you would like to avoid dependency conflicts with existing projects, you
can make use of [virtualenv](https://virtualenv.pypa.io/en/latest/)

## API Key Setup
Before getting started, you will need to signup for the
[google API console](https://console.developers.google.com/project). Once you complete adding a
project, enable the Google Places API for Webservices (APIs & auth -> APIs). Next you will need to
generate credentials for reaching the API (APIs & auth -> credentials). Use the "Public API Access"
option to create a "server key". The last step will be to save this API key in a suitable location.

One way to do this is to create a bash script at `~/.secrets`. The file should look like:
```bash
export GOOGLE_PLACE_API_KEY=mysecretgoeshere
```

Then add this line to your `~/.bashrc`: `source ~/.secrets`.

Lets test that you can reach the Google Place API. As part of the python installation
(with `requirements.txt`) you also implicitly installed the python library we will use with the
Google Places API. To test, start a python terminal session by typing `python` into your terminal.
Then execute each line below:

```python
import os
from googleplaces import GooglePlaces

API_KEY = os.getenv("GOOGLE_PLACE_API_KEY")
google_places = GooglePlaces(API_KEY)

query_result = google_places.nearby_search(radius=50000, location='San Francisco, CA', keyword='climb touchstone')

for place in query_result.places:
    print place.name
```

The result should be a list of all the Touchstone climbing gyms in the area:
* Mission Cliffs
* Diablo Rock Gym
* Berkeley Ironworks
* The Studio Climbing
* Great Western Power Company

Also take note that this allowed you to access the API secret key without committing it to the
source. We will use a similar technique later on with Docker. In anticipation of this, browse
to `/docker-workshop/backend/environment`, copy `secrets.txt.template` to `secrets.txt`, then
input your API key. While you are at it, set the django secret key as well.

## Backend API
This first part of the project is mostly written for you to use as a reference
when working on the frontend API.

## Frontend API

## Nginx

# Licensing
This workshop, including code and documentation is licensed under the Creative Commons Attribution
ShareAlike 4.0 International License and the MIT license. Details for each license are below and
in LICENSE.

## MIT License
The MIT License (MIT)

Copyright (c) 2015 Pedro Rodriguez

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Creating Commons License
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
