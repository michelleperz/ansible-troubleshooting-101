The following are high level ticket categories that we see in Ansible support along with specific logs or endpoints that we regularly request from customers experiencing these problems.  Please note that this list is not comprehensive but is intended to serve as a foundation for common case types.  

**Installation/Upgrades**
- The inventory file used for the installation/upgrade
- The latest setup.log either within the installer directory or in `/var/log/tower`
- The version of Tower being installed
- The version of Tower that is being upgraded from

**Backup/Restore**
- The inventory file used for the backup
- The latest setup.log from the `./setup.sh -r` run

**LDAP**
- The output of https://tower.example.com/api/v2/settings/ldap
- An ldapsearch using the DN from the command line 
- Enable debug logging: Add the following snippet to `/etc/tower/custom.py` across all cluster nodes and restart services with `ansible-tower-service restart`

```
LOGGING['loggers']['django_auth_ldap'] = {
   'level': 'DEBUG', 'handlers': ['console', 'file', 'tower_warnings'],
   'propagate': True
}
```
**SAML**
- The output of https://tower.example.com/api/v2/settings/saml 
- A decoded SAML response from the IDP 
- Enable debug logging: Add the following snippet to `/etc/tower/custom.py` across all cluster nodes and restart services with `ansible-tower-service restart`
```
LOGGING['loggers']['django_auth_ldap'] = {
   'level': 'DEBUG', 'handlers': ['console', 'file', 'tower_warnings'],
   'propagate': True
}
```
**Logging Aggregation**
- The output of https://tower.example.com/api/v2/settings/logging 
- Enable debug logging: Add the following snippet to `/etc/tower/custom.py` across all cluster nodes and restart services with `ansible-tower-service restart:`
```
LOGGING['loggers']['django_auth_ldap'] = {
   'level': 'DEBUG', 'handlers': ['console', 'file', 'splunk'],
   'propagate': True
}
```
**Project Update Failures**
- The output of https://tower.example.com/api/v2/project_updates/N where N is the ID of the project update job
- The output of https://tower.example.com/api/v2/projects/N where N is the ID of the project 

**Playbook Failures**

*General*
- The relevant playbook
- The stdout from the job run at a higher verbosity (-vvvv) 
- The version of Ansible (ansible --version)
- Output of `ansible-config dump --only-changed`

*Windows:* In addition to the items requested previously, you may also need
- The inventory variables utilized to communicate to the Windows hosts
- The output of winrm get winrm/config on the affected Windows hosts

*Networking*
- Enable debug logging 

**Performance Issues**
- Sosreport from each cluster node(s)
- In addition to a sosreport, the following may be useful for troubleshooting the individual components:

*Postgres*
- If an external database, a tarball of `/var/log/pgsql/{VERSION}/data` or `/var/lib/pgsql/{VERSION}/data/pg_log`
- DB specifications including IOPS, RAM, CPU

*RabbitMQ*
- The output of https://tower.example.com/api/v2/ping/ 

*UI Slowness*
- The api output of any particular page that is slow to load. Example: https://tower.example.com/api/v2/job_events/ 
- SQL profile data while replicating the slowness:
   - su - awx
   - `awx-manage profile_sql --threshold 2 --minutes 5`
   - Attach files from /var/log/tower/profile

**Isolated Nodes:**
- The management_playbooks.log from `/var/log/tower`
- The output of `awx-manage test_isolated_connection --hostname <ISOLATED_NODE_HOSTNAME>` to verify connectivity between the Tower node(s) and the isolated node. 

**RBAC**
Most RBAC questions that require troubleshooting are due to users not being able to do something that they should be able to or vice versa.  Each object in Tower that a user can get access to will have an associated endpoint.  The following are examples of what to request for with permissions issues with a workflow template, but similar endpoints will be available for projects, inventories, job templates, etc.:

https://tower.example.com/api/v2/workflow_job_templates/N/object_roles/ where N is the ID of the workflow
https://tower.example.com/api/v2/workflow_job_templates/N/access_list/  
https://tower.example.com/api/v2/users/N/roles/  where N is the ID of the affected user

**General Ansible Tower Configuration**
In order to gather all configuration data about an Ansible Tower instance, please request the output of https://tower.example.com/api/v2/settings/all This is especially useful to confirm the presence of load balancer or proxy configuration. 

