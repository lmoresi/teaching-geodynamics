## Google cloud

[Cloud Dashboard](https://console.cloud.google.com/home/dashboard?project=iconic-works-162100)

### Instances / machines

[Start a new instance](https://cloud.google.com/compute/docs/instances/create-start-instance).
The machine type depends on the number of users but it is possible to choose faster disk, more
disk space, more cpu's

  - [Machine types](https://cloud.google.com/compute/docs/machine-types)
  - Container-optimised-os (stable)

Seems to work quite well. Most of the disk space is in /var where docker puts images etc.
Todo: work out where the /home space gets used up and link to /var if needed.

### Docker on Google cloud

  - [Google Summary](https://cloud.google.com/compute/docs/containers/)
  - ["How to" with Container OS](https://cloud.google.com/container-optimized-os/docs/how-to/)

When logged in, the default user is (in my case) `louis_moresi`, the default docker user is `chronos` so
it is necessary to set permissions carefully when adding volumes to user-accessible spaces.

### What to do ...

  1. Set up new instance of required machine with container-optimised OS
      - Ensure that the http firewall is open by default on setup
      - Make sure enough disk / cpu resources are allocated

  2. Add other ports to the http firewall using this (via gcloud console):
``` sh
      gcloud compute firewall-rules update default-allow-http --allow=tcp:80,tcp:8080-8089
      # did it work ?
      gcloud compute firewall-rules list
```

  3. Log into the container-optimised virtual machine on google
    - `docker pull` the required image
    - `mkdir vieps_geo_users`
    - `cd vieps_geo_users && mkdir www[1-8]`
    - `chmod -R a+w` on these (maybe `sudo chown -R chronos:chronos vieps_geo_users` ?)

  4. Run the container with the following command:
```sh
nohup docker run -p 8080:8080 -p 8081:8081 -p 8082:8082 -p 8083:8083 -p 8084:8084 -p 8085:8085 -p 8086:8086 -p 8087:8087 -p 8088:8088 -v  /home/louis_moresi/vieps_geo_users:/geodynamics/build lmoresi/docker-vieps-geo-8user:2017 &
```

  5. exit the shell using `^D` to ensure that the process continues to run in the background

  6. Log into the root instance of the site (`http:?.?.?.?:8080`) and use the `jupyter` terminal to build the site (only the root instance has content by default
     as this keeps the image size manageable) `cd /geodynamics && ./scripts/run-sitebuilder8.py`

  7. Do any other site maintenance / preparation such as caching data via the root instance. This will change the master copies of notebooks and can be propagated out via the `scripts/run-sitebuilder8.py` command.

#### Notes on these instructions

Volumes exposed in the image are:

``` sh
  /geodynamics/www/external
  /geodynamics/build# ??
```
We need to use absolute paths in the `docker run` command
otherwise these things get created quietly somewhere in the `/var/docker` area.
That might be OK as it would be persistent, but not helpful for backup etc.

### Usage

The root instance is the user logging in via



#### Passwords

Pretty obvious, but can be changed in the
