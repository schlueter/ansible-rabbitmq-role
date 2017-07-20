# A Rabbitmq role for Ansible

This role provisions a rabbitmq cluster.

## Usage

Include the role in your play's role list. If you are configuring multiple machines with replication. If you are configuring multiple machines in a cluster, set the *cluster_peer* variable to the hostname of an intended peer or something falsy (for one or more nodes which are joined to the cluster by being another machine's *cluster_peer*). Rabbitmq must be running on the targetted peer when the cluster connection is made. Examples of this include configurations such as:

```
- hosts: rabbitmq
  become: yes
  roles:
    - role: rabbitmq
      vars:
        cluster_peer: "{{ play_hosts[0] if ansible_hostname != play_hosts[0] else None }}"
```
or:
```
- hosts: rabbitmq
  become: yes
  roles:
    - role: rabbitmq
      vars:
        cluster_peer: "{{ groups['rabbitmq'][0] if ansible_hostname != groups['rabbitmq'][0] else None }}"
```
or if you must:
```
- hosts: rabbitmq-sentinel
  become: yes
  roles:
    - rabbitmq

- hosts: rabbitmq-legion
  become: yes
  roles:
    - role: rabbitmq
      vars:
        cluster_peer: "{{ groups['rabbitmq-sentinel'] }}"
```
*Note: each of these examples requires that each machine must be able to resolve the hostname of their cluster peer, so something like the following is required before the rabbitmq role is applied:*
```
- hosts: all
  gather_facts: yes
  become: yes
  pre_tasks:
    - lineinfile:
        line: "{{ hostvars[item].ansible_all_ipv4_addresses[1] }} {{ item }}"
        path: /etc/hosts
      with_items: "{{ play_hosts }}"

