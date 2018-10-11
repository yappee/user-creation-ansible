---

- name: Add user if not existing
  user:
     name={{ users }}
     state=present
     shell=/bin/bash
  sudo: yes
  tags: Add_new_user

- name: Create centralized location for the user
  shell: mkdir -p /etc/ssh/authorized_keys/{{ users }} &&  mkdir -p /etc/ssh/authorized_keys/{{ users }}/.ssh && touch /etc/ssh/authorized_keys/{{ users }}/.ssh/authorized_keys
  sudo: yes
  tags: Create_centerlized_location

- name: Backup the authorized_keys of user profile
  shell: cp -p /etc/ssh/authorized_keys/{{ users }}/.ssh/authorized_keys /etc/ssh/authorized_keys/{{ users }}/.ssh/authorized_keys_before_update.bak
  sudo: yes
  tags: Backup_authorized_keys_from_user_profile

- name: GRANT permission to user profile
  authorized_key:
    user: "{{users}}"
    path: /etc/ssh/authorized_keys/{{ users }}/.ssh/authorized_keys
    key: "{{ lookup('file','public_keys/{{item}}.pub') }}"
    state: present
  #exclusive: yes
  with_fileglob::
   - public_keys/{{ item }}.pub

#  notify: restart ssh service

