# Ansible_Playbook
Ansible playbook to check if windows service exists or not, if exists check then it checks the state of the windows service, if the windows service is not running, it restart the windows service and verifies if the windows service is running.

Firstly we need to ensure the `ansible.windows` collection is installed on our control node using the `ansible-galaxy` command:
`ansible-galaxy collection install ansible.windows`.

To check for windows services, the `ansible.windows.win_service` module is the primary tool. This module is idempotent (meaning it will only take action if the service isn't already in the desired state), we can use `ansible.windows.win_service_info` to explicitly check for service existence and current service state before proceeding further.

`win_service_info` module queries the Windows Service Control Manager. It returns a dictionary including an exists boolean and a services list containing the state (e.g., running, stopped).

`fail with when`: acts as a guardrail. If exists is false, the playbook stops immediately for that host to prevent errors in subsequent tasks.

`win_service with state: restarted`: ensures the service is stopped and started again. The when condition ensures this only happens if the initial check found it wasn't already running.

`register: restart_result`: captures the outcome of the restart attempt. If the service was already running, this task is skipped, and restart_result.changed will be false.

`failed_when`: re-checks the service info and forces the task to fail if the state is still not running after the restart attempt.
