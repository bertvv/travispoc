# Proof-of-concept Travis + CentOS + Ansible

[![Build Status](https://travis-ci.org/bertvv/travis-poc.svg?branch=master)](https://travis-ci.org/bertvv/travis-poc)

Proof-of-concept for testing Ansible on CentOS hosts with Travis-CI.

The `Dockerfile` creates a Docker image with Ansible installed and the necessary files to run an Ansible test playbook. During build, `systemd` is unavailable. Running the playbook from within `Dockerfile` results in the error message:

```
The service failed to start due to D-BUS connection.
```

Systemd doesn't work out-of-the box inside the container. A [workaround](https://developerblog.redhat.com/2014/05/05/running-systemd-within-docker-container/) was developed by Dan Walsh, but this is not enough to run an Ansible role. The solution is to enable the service during the build phase, but not to start it right away.

However, executing `ansible-playbook` with `docker run` also doesn't work:

```
TASK: [bertvv.httpd | Ensure Apache is always running] ************************ 
failed: [localhost] => {"failed": true}
msg: no service or tool found for: httpd
```

Currently, my solution is to run the docker container detached and then execute `ansible-playbook` with `docker exec`. See the `.travis.yml` for details.

## References

- Inspiration comes from: <https://stackoverflow.com/questions/29453017/build-project-on-centos-using-travis>
- Official Docker image for CentOS 7 (and info on running systemd within the container): <https://hub.docker.com/_/centos/>
- Dockerfiles for CentOS 7 with Ansible: <https://github.com/William-Yeh/docker-ansible/>
- Related bug: <https://bugzilla.redhat.com/show_bug.cgi?id=1033604>
- Run systemd in an unprivileged container <https://maci0.wordpress.com/2014/07/23/run-systemd-in-an-unprivileged-docker-container/>

## Docker cheat sheet

| Command                          | Task                                              |
| :---                             | :---                                              |
| `docker build .`                 | Build Docker image with Dockerfile in current dir |
| `docker build -t TAG .`          | same, but also give the image a name              |
| `docker run -i -t TAG /bin/bash` | Log in to a container based on image TAG          |
| `docker images`                  | List base images                                  |
| `docker rmi IMAGE_ID`            | Remove specified image                            |
| `docker ps -a`                   | List all containers (including stopped ones)      |
| `docker rm CONTAINER_ID`         | Remove specified container                        |


## License

BSD

## Author Information

Bert Van Vreckem (bert.vanvreckem@gmail.com)

