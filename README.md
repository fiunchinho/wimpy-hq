# Wimpy Head Quarter
This Ansible playbooks are intended to be used to deploy your applications from an centralized place, so you can share configuration among them, instead of having the same configuration values in several applications.

The playbook uses the Wimpy role that deploys your application creating a new Auto Scaling Group, following immutable infrastructure principles.

## Usage
You need
- Dockerfile
- docker-compose file for every env

Execute this playbook from the path where the code of your application is. You need to choose the `hosts` file for the environment where you want to deploy.

```bash
$ ansible-playbook -i ../wimpy_hq/env_hosts ../wimpy_hq/deploy.yml
```

If you have encrypted variables, or you need to pass extra variables like the version to deploy, you can still use regular Ansible features

```bash
$ ansible-playbook -i ../wimpy_hq/env_hosts ../wimpy_hq/deploy.yml --vault-password-file ~/.vault_pass --extra-vars "wimpy_release_version=2.3"
```

## Customizing your Application deploy
If your application contains a file `deploy/deploy.yml` file with variables, WimpyHQ will include those variables.
This is useful because sometimes applications need to pass variables that only make sense to them, and it's not desirable to share them between different applications.

Apart from the `deploy/deploy.yml` file, there are other files that you can create inside this folder

```yml
------

- include: check_vars.yml

- include: "{{ wimpy_before_build_tasks_file | default('empty.yml') }}"

- include: build_docker_image.yml

- include: "{{ wimpy_before_deploy_tasks_file | default('empty.yml') }}"

- include: deploy.yml

- include: "{{ wimpy_after_deploy_tasks_file | default('empty.yml') }}"

- include: clean_launchconfigurations.yml
```