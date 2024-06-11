# timeseer Docker Roles

## Introduction

This repository contains the Ansible roles for configuring and managing the timeseer environment within Docker. Each role is specifically tailored to streamline various aspects of the timeseer setup and deployment.

## Roles

- **timeseer**: Sets up Timeseer in docker, handling storage preparation,docker volumes, and deployment.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/timeseer)

- **timeseer_reverse_proxy**: Configures Timeseer and its reverse proxy in docker, including storage, docker containers, and proxy setup.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer_reverse_proxy-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/timeseer_reverse_proxy)

- **timeseer_traefik**: Deploys Traefik as a reverse proxy and load balancer in docker, focusing on SSL/TLS with Let's Encrypt.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer_traefik-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/traefik)

- **timeseer_data_service**: Deploys timeseer_data_service ,handling storage preparation,docker volumes, and deployment.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer_data_service-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/timeseer_data_service)

---

## Contributions

Contributions to improve or extend the roles are welcome. Please follow the contribution guidelines outlined in the repository.

## License

This collection is licensed under MIT. See the LICENSE file for more details.

## More Information

For detailed information and specific configuration instructions, please visit [Timeseer.AI Ansible Collection](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles).
