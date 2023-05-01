
# Ansible Role - potos\_dconf

This is **WORK IN PROGRESS**:

* the automated checks are currently failing
* meta is not updated yet

`dconf` is the [official way](https://help.gnome.org/admin/system-admin-guide/stable/dconf.html.en) to manage
gnome user and system settings and override gnome or distribution specific settings.

In its final form, this role will allow to configure any gnome setting system wide, but currently
only a limited set is implemented (see *Role Variables* below).

By default, gnome interprets system settings as defaults that can be overriden by the user.
But it is also possible to [lock down](https://help.gnome.org/admin/system-admin-guide/stable/dconf-lockdown.html.en)
settings, so users cannot change them any more.

## Wishlist

* Mouse/Mousepad settings
  * emulate middle button (when no hw button available)
  * restore linear mouse movement (no acceleration)
* change window behaviour (I don't want the focus window to autoraise)
* Activation of custom gnome extension (e.g. [argos](https://github.com/p-e-w/argos.git))
* `.desktop` Files to be used as favorite app with custom entries
* Implement *lock down*.

[![Test](https://github.com/nis65/ansible-role-potos_dconf/actions/workflows/test.yml/badge.svg)](https://github.com/nis65/ansible-role-potos_dconf/actions/workflows/test.yml)

## Development hints

Be aware that most of these command return different values
depending on the user session you are logged in right now.

* `dconf dump /` lists all dconf settings. This lists only
  the settings explicitly set somewhere.
* `gsettings list-recursively` lists really all variables
  currently known and there values. Use this to search for
  a certain setting, translate it back in `dconf` format
  and then implement it here.

## Example Playbook

As this role is tested via Molecule one can use [that
playbook](./molecule/default/converge.yml) as a starting point:

```yaml
---

- name: Converge
  hosts: all
  gather_facts: yes
  tasks:
    - name: run role
      ansible.builtin.include_role:
        name: 'ansible-role-potos_dconf'
```

## Role Variables

The default variables are defined in [defaults/main.yml](./defaults/main.yml):

```yaml
---

# List of entries to be added to the favorite app
potos_dconf_org_gnome_shell_favorite_apps:
  - "org.gnome.Nautilus.desktop"
  - "libreoffice-writer.desktop"
  - "snap-store_ubuntu-software.desktop"
  - "yelp.desktop"
```

Another option is to use `ansible-doc` to read the argument specification:

```sh
ansible-doc --type role -r . main ansible-role-potos_dconf
```

## Requirements

N/A

## License

See [LICENSE](./LICENSE)

## Author Information

[Project Potos](https://github.com/projectpotos)

