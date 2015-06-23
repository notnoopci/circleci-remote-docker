# Using dedicated machines for Docker in CircleCI

Toy project to have CircleCI projects use a dedicated docker-machine, to speed
up pulling images and building images.

## Background: CircleCI Support and Limitations
CircleCI, the continuous integration platform, has out-of-the-box native
Docker support.  Users can build, test, and publish Docker containers without
requiring any additional setup.

Due to some technical limitations, CircleCI has some limitations:

* Builds start without cached docker layers, resulting into longer than desired build times
* Builds limited by container capabilities, making integration tests of
  multiple heavy-usage services a bit challenging
* Builds limited to using btrfs storage engine and lxc execution engine.
  Users needing native/libcontainer support and/or other storage enginers are
  out of luck!

This toy project illustrates how you can start up a dedicated machine to run
Docker operations, and have CircleCI builds use that instance.  The layers
will be cached across build (making builds be very fast).


## How?

This toy project illustrates how you can bootstrap a new instance with
docker-machine, and have CircleCI builds use it!

Working with this, we require:

* `docker-machine` - check https://docs.docker.com/machine/
for installation info.
* `CIRCLECI_TOKEN` to be your user API token.

The steps are quite simple - run the following on your local computer

```bash
$ # Start up machine with docker-machine
$ # check create arguments for your desired provier
$ docker-machine create --driver amazonec2 [...] toy-instance
INFO[0002] Launching instance...
INFO[0032] Waiting for SSH on [<REDACTED>]
sudo: unable to resolve host ip-10-0-1-85
sudo: unable to resolve host toy-instance
sudo: unable to resolve host toy-instance
INFO[0234] "toy-instance" has been created and is now the active machine.
INFO[0234] To point your Docker client at it, run this in your shell: eval "$(docker-machine env toy-instance)"

$ # Get CircleCI builds to use it
$ ./circleci-docker-machine toy-instance circleci/circleci-docker-machine
New circleci/circleci-docker-machine builds will be using Docker daemon on toy-instance ([<REDACTED>])
```

All done!  Try to use docker in builds.  You can check builds in CircleCI
(e.g. https://circleci.com/gh/circleci/circleci-docker-machine/3)
